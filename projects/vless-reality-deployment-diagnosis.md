# Project Learning Record — VLESS + REALITY Deployment and Failure Diagnosis

## Problem / Goal

在一台通用 Debian VPS 上部署 VLESS + REALITY + XTLS Vision，供移动网络客户端作为稳定代理出口；要求不影响同机既有服务，并能明确区分“端口可达”“REALITY 握手可用”“真实代理数据面可用”。

## Decision Model

核心诊断模型：

`网络可达性 → 服务监听 → REALITY/TLS 握手 → VLESS 认证 → 真实代理数据面 → 具体客户端/接入网`

不要跳层。上层失败不应在下层尚未证实前归因给客户端或运营商。

## Evidence

### Fact — 端口通不等于代理可用

在 2026-09-04 的真实部署中：

- VPS 的 Xray service 为 active，并监听 TCP/443；
- 外部 TCP/443 连接成功；
- 未认证 TLS 回落到 REALITY target 可返回 HTTP 200；
- 但使用匹配参数的原生 Xray 客户端做 SOCKS → HTTPS 端到端请求时仍发生 connection reset。

因此，`TCP/443 reachable + fallback TLS works` 只能证明网络入口和部分 REALITY target 行为正常，不能证明 VLESS + REALITY 数据面正常。

### Fact — debug log 比客户端“timeout”更有判别力

将 REALITY `show=true`、Xray log level 临时调到 debug 后，服务端能够看到客户端版本、Short ID、握手阶段以及最终 `handshake did not complete successfully`。这把故障从“手机/移动网/App 可能有问题”收缩到了 REALITY target handshake。

### Fact — www.microsoft.com 在该版本链上存在已知 8192-byte 边界问题

项目使用 Xray-core v26.3.27 时，服务端 debug log 对 `www.microsoft.com` 记录到 `Certificate: 8273`，随后握手未完成。Xray-core issue #6356/#6402 记录了同类问题：REALITY 相关实现存在 8192-byte 的预读/record handling 上限，而 Microsoft 的 Certificate TLS record 可达到 8273 bytes。

Evidence (as of 2026-09-04):
- https://github.com/XTLS/Xray-core/issues/6356
- https://github.com/XTLS/Xray-core/issues/6402

### Fact — target substitution provided decisive project validation

保持 UUID、Reality key、Short ID、TCP/443、Vision flow 等参数不变，只将 REALITY target/SNI 从 `www.microsoft.com` 换成另一兼容目标后，原生 Xray 客户端端到端请求成功，出口 IP 与 VPS 出口一致。随后 Android 客户端也成功连接。

## What Changed in the Model

### Old assumption

“大型、稳定、TLS 1.3 正常的网站通常都可以作为 REALITY target；只要 TCP/443 和 TLS 回落成功，REALITY 大概率没问题。”

### New model

REALITY target 是协议数据路径的一部分，而不是纯装饰性伪装域名。目标站 TLS 行为、证书记录形态、ALPN、边缘节点变化都可能直接破坏 REALITY 握手。

因此 target 选择必须被当作一个需要验证的 runtime dependency。

Epistemic status: **Fact + Inference**。事实部分由项目端到端测试与 Xray debug log 支持；“target 应视为 runtime dependency”是从机制和项目结果抽象出的工程推论。

## Reusable Diagnostic Procedure

1. **先验证监听与冲突**：确认目标 TCP/UDP 端口未被既有服务占用。
2. **再验证云侧入口**：本机 firewall 无阻断仍不代表 Security Group 已放行；必须从外部主机测试端口。
3. **验证 REALITY target 本身**：使用 `xray tls ping <target>` 或等价方法确认目标 TLS 行为，不能只看浏览器能否打开。
4. **必须做原生端到端测试**：用与服务端匹配的 Xray 客户端建立本地 SOCKS，再通过代理请求外部 IP 服务。出口 IP 正确才算数据面 PASS。
5. **客户端 timeout 不直接归因于接入网**：如果另一个独立客户端也失败，优先回到服务端/协议层。
6. **临时打开 debug/show**：只在诊断期间启用；定位后恢复较低日志级别，减少噪声与暴露面。
7. **一次只改一个高信息量变量**：本例只换 target/SNI，其余认证参数不动，因此修复具有较强因果判别力。
8. **最后才测试真实移动网络与 App**：服务器数据面先 PASS，再判断运营商、Hiddify、Mihomo 等客户端/网络差异。

## Boundary / Failure Conditions

- 某 target 今天可用，不代表长期可用；CDN、证书链、TLS record 形态可能变化。
- `xray tls ping` 成功是必要的兼容性线索，但仍不能替代真正的 VLESS + REALITY 端到端测试。
- 不应仅依据“大厂域名”“流量看起来像 HTTPS”选择 target；协议实现的具体版本边界仍可能造成兼容失败。
- 升级 Xray 可能修复某些 target 问题，也可能引入新的客户端兼容约束；真实故障时优先做最小变量修复，再决定是否升级。
- REALITY 代理是用户态出站代理时，通常不依赖 Linux `net.ipv4.ip_forward=1`；这与 WireGuard/subnet-router 等 L3 routed VPN 不同。

## Practical Principle

**健康检查必须覆盖用户真正依赖的数据面，而不是只检查外围症状。**

对于代理系统，推荐的最小 Done Definition 是：

`service active + port externally reachable + authenticated tunnel established + proxied HTTPS request succeeds + observed egress IP is expected`

前四项中的任意前缀都不能单独替代最后的端到端验证。

## New Knowledge Gaps

- REALITY target 的长期稳定性是否需要轻量定期验证，还是故障触发验证即可？当前倾向事件驱动，避免过度工程化。
- 新版 Xray/REALITY 是否已经消除或改变 8192-byte record handling 边界，需要在计划升级时重新验证，而不是提前追新版本。
