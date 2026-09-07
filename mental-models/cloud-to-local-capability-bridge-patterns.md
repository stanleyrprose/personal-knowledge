# Cloud-to-Local Capability Bridge Patterns

**Status:** INSTANTIATED MODEL  
**Epistemic Status:** Fact（项目观测） + Inference（跨场景泛化） + Judgment（方案选择）  
**Project evidence date:** 2026-09-07

## Core Insight

远程 AI 调用一台本地/远程 Mac 的能力时，应把问题拆成两个边界：

1. **Remote Reachability / Control Plane**：Cloud ChatGPT 如何安全到达 Mac；
2. **Local Capability Plane**：到达 Mac 以后，如何调用 Browser、Files、Shell、Build Tool 或其他本机 runtime。

核心模型：

`Cloud reachability transport != Local capability transport`

云端一侧需要网络可达，不代表 Mac 内部每个能力都必须暴露成 HTTP 服务。只有真正跨主机的边界才需要网络 transport；同机进程之间可以继续使用 stdio、Unix socket、CLI 或其他本地 IPC。

## Project-Validated Pattern — Remote Bridge + Local MCP stdio

**Fact（项目观测）:** 在 Mac Browser Plane 项目中，Cloud ChatGPT 已通过受控远程执行桥到达 Mac，然后在 Mac 本地启动一个 MCP client；该 client 再通过 MCP stdio 调用 Browser Plane MCP server。Browser Plane 后端继续复用同一 JobStore / Worker / Chrome runtime，没有新增 HTTP listener、公开 MCP port 或第二套浏览器 runtime。

抽象路径：

`Cloud ChatGPT -> authenticated remote execution bridge -> local MCP client -> MCP stdio -> local capability runtime`

本地 Agent 则可以跳过远程 bridge：

`Local Agent -> MCP stdio -> same local capability runtime`

这形成一个可复用原则：

`One local capability runtime, multiple clients; remote clients terminate at a reachability bridge, local clients connect directly.`

## Why Local stdio Can Be Better Than Adding HTTP

**Inference:** 当调用方已经通过可信桥进入目标 Mac，MCP stdio 往往比再增加一层本地 HTTP API 更简单：

- 不需要为该本地能力再开放 TCP port；
- 不需要再为 localhost capability 单独处理 TLS、HTTP auth、listener lifecycle 和端口冲突；
- MCP tool schema、tool discovery、structured result 仍然保留；
- MCP server 生命周期可以绑定到 client process，减少常驻 daemon；
- 本地 capability surface 不必直接暴露给网络。

**Boundary:** stdio 本身不是远程传输。它适合“同一主机上的 client/server process”。如果 cloud client 没有任何受控方式在 Mac 上启动/连接本地进程，stdio 不能替代远程 reachability layer。

## Six Cloud-to-Mac Access Patterns

### Pattern A — Remote Execution Bridge + Local MCP stdio

`Cloud AI -> secure bridge -> local MCP client -> MCP stdio -> capability runtime`

**Epistemic Status:** Fact（本项目路径） + Judgment（当前默认推荐）

适合：已有可信 Cloud→Mac bridge；希望避免新增公网/局域网 listener；本机已有 MCP capability server。

优点：网络暴露面小、复用 MCP schema、无需第二套 API、实现简单、容易保持 One Runtime。

代价/边界：安全性高度依赖 remote bridge 的授权范围；bridge 若能执行任意命令，其权限面可能远大于单个 MCP tool，需要独立约束。

### Pattern B — Direct Remote MCP over HTTPS

`Cloud AI -> HTTPS / private network / tunnel -> MCP network transport -> capability server`

**Epistemic Status:** Inference / standard architecture alternative

适合：多个远程 client 都需要直接发现/调用同一个 MCP server；不希望依赖某个专用 remote-execution bridge。

优点：端到端 MCP；client 直接 tool discovery；跨主机边界清晰。

代价：必须管理网络 listener、TLS、authentication/authorization、rate limiting、service lifecycle、NAT/tunnel/VPN 或公网入口；攻击面与运维面明显大于纯本地 stdio。

### Pattern C — Custom REST / HTTP Capability API

`Cloud AI -> HTTPS API -> local service adapter -> capability runtime`

**Epistemic Status:** Inference / architecture alternative

适合：能力本身已经是稳定 service API；需要支持大量非 MCP client；需要清晰的业务 REST contract。

优点：生态广、调试容易、语言无关、适合 service-to-service integration。

代价：需要自己定义 endpoint/schema/error/idempotency/versioning/auth；如果内部已经有 MCP contract，可能形成重复 API surface。

### Pattern D — SSH / Remote Shell

`Cloud AI / operator -> SSH -> shell/CLI -> local resource`

**Epistemic Status:** Inference + Judgment

适合：bootstrap、诊断、部署、运维、一次性工程操作。

优点：通用、几乎任何 CLI capability 都能调用；无需为每种资源先写 API。

代价：能力面通常过宽、tool semantics 弱、参数/输出不结构化、审计与最小权限更难。长期把 SSH 当“业务 capability API”通常不如 MCP/REST 清晰。

### Pattern E — Outbound Agent / Queue / Relay

`Mac agent -> outbound connection/queue broker <- Cloud AI`

Cloud 侧提交 job，Mac 主动拉取或维持 outbound session，再返回结果。

**Epistemic Status:** Inference / architecture alternative

适合：Mac 在 NAT、防火墙或不稳定网络后；不希望任何 inbound listener；任务天然异步或需要离线重试。

优点：无需 inbound port；适合 durable async job、retry、store-and-forward。

代价：需要 broker/queue、job state、deduplication、retry、result storage，复杂度高于直接 bridge；交互式低延迟任务不一定划算。

### Pattern F — Desktop / Computer-Use Relay

`Cloud AI -> desktop relay -> mouse/keyboard/screen -> GUI application`

**Epistemic Status:** Inference / fallback pattern

适合：目标能力只有 GUI，没有 API/MCP/CLI；需要操作桌面应用。

优点：覆盖面极广，理论上任何可视 GUI 都能操作。

代价：结构化程度最低；容易受 UI 变化、窗口状态、分辨率、登录态影响；不适合作为已有 API/MCP capability 的第一选择。

## Comparison

| Pattern | Mac inbound capability listener | Structured tool contract | Async suitability | Implementation complexity | Security surface | Best fit |
|---|---|---:|---:|---:|---:|---|
| Bridge + local MCP stdio | No | High | Medium | Low if bridge exists | Low local network exposure; bridge authority matters | Existing Cloud→Mac bridge + local MCP |
| Direct remote MCP/HTTPS | Yes | High | Medium | Medium–High | Network service must be hardened | Multi-client direct MCP |
| REST/HTTP API | Yes | Medium–High | High | Medium–High | Network service + custom auth/API | Stable service API / non-MCP clients |
| SSH/shell | SSH service | Low | Low | Low to bootstrap | Broad privilege surface | Ops / diagnosis / bootstrap |
| Outbound queue/relay | No | Medium–High | High | High | Broker + agent auth | NAT / async / unreliable inbound |
| Desktop/Computer Use | Depends on relay | Low | Low | Medium–High | Broad GUI authority | GUI-only capability |

## Selection Heuristic

**Judgment:** 默认优先选择“满足需求的最窄远程暴露面”，而不是“最统一的协议”。

1. **已有可信 Cloud→Mac bridge + capability 与 bridge 同机** → 首选 `bridge + local MCP stdio`。
2. **多个独立远程 client 需要直接共享 MCP** → 考虑 `remote MCP over HTTPS/private network`。
3. **能力本身是稳定业务 service，且需要非 MCP 生态** → REST/HTTP。
4. **只是部署、修复、诊断** → SSH，避免把 SSH 变成长期业务 API。
5. **无可靠 inbound、任务异步** → outbound agent/queue。
6. **只有 GUI，无结构化接口** → computer-use relay 作为 fallback。

## Design Rules

### 1. Only put network transport at boundaries that need network reachability

不要因为最外层 client 在 cloud，就把 Mac 内所有内部调用都 HTTP 化。

### 2. Separate reachability from capability semantics

Remote bridge 解决“能不能到 Mac”；MCP/REST/CLI 解决“到了以后怎么表达能力”。两者是正交问题。

### 3. Prefer one runtime, many adapters/clients

不要为 ChatGPT、OpenClaw、Hermes、Codex 各启动一套 Browser runtime。多个 client 应复用同一 capability contract 与执行 runtime，除非隔离需求有明确证据。

### 4. Narrow the remote authority surface

本地 stdio 减少的是 network exposure，不自动保证整体安全。如果 remote bridge 权限过宽，风险仍然存在。应对 bridge 做 workspace、executable、command、resource 或 capability scope 控制。

### 5. HTTP is a transport choice, not a maturity level

把 local stdio 改成 HTTP 并不会自动让架构“更正式”。只有出现真实需求——跨主机直连、多 client 网络共享、独立 service lifecycle——HTTP 才带来 material value。

## Failure Modes

- **Protocol uniformity bias**：错误地认为 Cloud→Mac 和 Mac 内部必须用同一协议。
- **Tunnel-everything**：为每个本地 capability 都创建公网/tunnel endpoint，增加攻击面和运维面。
- **SSH-as-API**：把宽权限 shell 当长期 agent capability interface。
- **Runtime duplication**：每个 client 启一套独立 runtime，造成 profile/state/resource 冲突。
- **Bridge authority blindness**：看到 stdio 没开端口，就忽略 remote execution bridge 本身可能具有很高权限。
- **GUI-first automation**：目标已有 MCP/API，却仍优先使用脆弱的鼠标键盘自动化。

## Project Validation

**Fact, as of 2026-09-07:** Mac Browser Plane 已验证以下路径：

`Cloud ChatGPT -> remote execution bridge -> local MCP client -> MCP stdio -> Browser Plane -> Browser worker/runtime`

并验证本机多个 Agent 可直接通过同一 MCP stdio contract 复用同一 Browser Plane。

该 evidence 支持：

- remote reachability layer 与 local capability transport 可以分离；
- cloud client 不要求 local capability 必须暴露 HTTP；
- one runtime / multiple clients 在该项目中可行。

该 evidence **不证明**：

- bridge + stdio 对所有远程系统都优于 HTTPS MCP；
- 所有本地 capability 都适合 MCP；
- 不需要独立 auth/sandbox/authorization policy。

## Reusable Mental Model

遇到“Cloud AI 如何调用远程机器能力”的问题，先画四层：

`Cloud Client -> Reachability Boundary -> Local Capability Contract -> Execution Runtime`

然后逐层问：

- 哪一层真正跨网络？
- 哪一层需要长期 listener？
- 哪一层需要 structured tool discovery？
- 哪一层需要最小权限？
- 哪一层需要异步 durability？
- 能否复用现有 runtime，而不是新增一套服务？

先按边界选择 transport，再按 capability 选择 protocol，通常比“所有层统一成 HTTP”更稳健。
