# MCP Content Transport Boundary Model

**Domain:** AI / Agent / MCP  
**Mastery:** M3  
**Status:** active / E2E validated  

## Why It Matters

在 autonomous agent workflow 中，“工具是否有权限执行”与“工具返回结果是否需要客户端再次人工审批”是两个不同层次的问题。

即使 GitHub MCP 的普通 action 已被允许，文件返回类型仍可能触发 ChatGPT 的 file materialization approval，从而打断无人值守执行。

因此，Agent 自动化能力不能只检查 API 权限，还必须检查：

`Permission model + Result representation + Client handling policy`

## Core Mechanism

MCP tool result 的表示方式会影响客户端后续处理路径：

`Tool permission → Tool executes → Result representation → Client handling policy → Human gate / no gate`

如果读取结果带有 file / `EmbeddedResource` / attachment 语义，客户端可能进入 materialization 流程。

如果成功结果只返回：

```json
{
  "content": [
    { "type": "text", "text": "..." }
  ]
}
```

则文本可以直接进入模型上下文，而不需要先 materialize 为会话文件。

## Key Distinction

### 1. Action authorization

回答：

> 这个 MCP tool 是否允许执行？

由 App permission、tool confirmation、客户端安全策略等影响。

### 2. Result handling authorization

回答：

> Tool 已经执行成功，但客户端如何处理它返回的数据？

这是另一层 boundary。关闭普通 tool approval 不等价于关闭 file materialization approval。

## Validated Design Pattern — Read / Write Plane Separation

GitHub Text MCP v3 的最终有效架构采用：

```text
Read plane
ChatGPT → GitHub Text MCP → inline TextContent

Write plane
ChatGPT → Github MCP → branch / commit / PR / Actions
```

### Why this works

真正的摩擦只发生在 **GitHub 文本读取结果的 transport representation**，不是 GitHub 写能力本身。

因此最小修复不是重写完整 GitHub MCP，而是：

- 只替换 read path；
- 保留成熟 write/control plane；
- 把 blast radius 和 OAuth 权限保持在最小范围。

这是一个更通用的 Agent 架构原则：

> 当某个集成问题只发生在单一 capability boundary 时，优先替换最窄的 plane，而不是重建完整 integration。

## Authentication Model — Final Architecture

最初方案让 Bridge 同时承担：

- MCP Resource Server
- OAuth Authorization Server
- DCR / CIMD client registration
- GitHub OAuth upstream relay

真实集成中，这一层带来了过多兼容面。

最终验证有效的架构是：

```text
ChatGPT
  │
  │ GitHub OAuth（预注册 OAuth client）
  ▼
GitHub
  │
  │ GitHub user access token
  ▼
GitHub Text MCP Bridge
  │
  ▼
GitHub API
```

Bridge 回到单一职责：**受保护的 read-only MCP Resource Server**。

### Reusable Principle

> 如果上游身份提供方已经能直接完成标准 OAuth，而中间服务不需要拥有独立身份域，就不要额外实现一层 Authorization Server。

每增加一层 OAuth broker，都会增加：

- discovery 兼容性；
- DCR / CIMD 差异；
- callback 校验；
- token exchange；
- refresh-token；
- client-auth method；
- state / PKCE 调试面。

## GitHub Text MCP v3 — Engineering Lessons

### Lesson 1 — Schema / unit test success is below real-client E2E

代码能够返回 `TextContent`、CI green、Docker build 成功，只能证明服务器实现正确到一定程度。

真正的验收链应逐层验证：

```text
OAuth discovery
→ authorization
→ callback
→ token exchange
→ MCP initialize
→ tools/list
→ tool call
→ result representation
→ client UI behavior
```

**Fact:** 本项目在前几层多次“看起来正常”，但 ChatGPT 仍无法完成连接，直到后续层分别被真实观测和修复。

### Lesson 2 — Debug protocol integrations by stage, not by symptom

“连接失败”“403”“unexpected error”这类 UI 文案通常不足以定位根因。

有效方法是给每一层加入最小诊断：

- method / path / status；
- 是否有 `client_id`；
- redirect URI；
- resource；
- scope；
- PKCE 是否存在；
- `state` 只记录 hash，不记录原值。

**Security rule:** 不记录 authorization code、token、client secret、cookie 或凭证正文。

这个方式最终确认过：

- GitHub OAuth 已成功；
- callback 已成功；
- ChatGPT 已带有效 GitHub token 请求 `/mcp`；
- 故障已经从 OAuth 层转移到了 MCP transport 层。

### Lesson 3 — 不要过早把中间层当成根因

DCR 阶段一度怀疑 Cloudflare / WAF 403。

后来用接近 ChatGPT 的请求特征手工复现，公网 `/register` 仍能返回 201，说明“Cloudflare 拦截”并不能解释真实失败。

可复用原则：

> 在归因给 CDN / WAF / proxy 前，先用等价 method、headers、body 与 client behavior 复现；不能复现就保持该假设为 Inference，而不是 Fact。

### Lesson 4 — OAuth capability declaration必须等于实际实现能力

Bridge 曾宣告 CIMD support，但 ChatGPT 生成的 CIMD 使用：

- `private_key_jwt`
- JWKS URI
- RS256

而 Bridge 实际只支持 `token_endpoint_auth_method = none`。

这是一种典型 integration bug：

> capability advertisement 比真实 implementation 更宽。

原则：

- discovery metadata 只能宣告已经真正实现并测试过的能力；
- 不要为了“看起来兼容”同时打开多个未验证协议路径；
- fallback 应建立在实际可验证行为上。

### Lesson 5 — Protocol adapter 与 web framework body parser 是独立 boundary

最终 MCP initialize 的 400 根因不是 OAuth。

Express 已通过：

```ts
express.json()
```

提前消费 request body，但 MCP Node adapter 原来按：

```ts
app.all('/mcp', toNodeHandler(mcpHandler))
```

挂载，导致 adapter 尝试重新读取已经消费掉的 stream。

正确方式是显式传入 parsed body：

```ts
const nodeMcpHandler = toNodeHandler(mcpHandler);
app.all('/mcp', (req, res) => {
  void nodeMcpHandler(req, res, req.body);
});
```

通用原则：

> 当 framework middleware 会消费 request stream 时，后续 protocol adapter 必须明确知道 body 已被解析；不要默认底层仍能重新读取原始流。

适用范围不仅是 MCP，也包括：

- webhook signature middleware；
- GraphQL / RPC adapters；
- streaming upload；
- proxy middleware；
- 需要 raw body 的支付 / webhook 验签。

### Lesson 6 — Deterministic dependency resolution 是部署正确性的一部分

第一次 production build 暴露 TypeScript dependency/type drift，而此前 CI 已经 green。

修复包括：

- 增加 `package-lock.json`；
- CI 使用 `npm ci`；
- Docker build/runtime 使用 `npm ci`。

原则：

> 没有 lockfile 的“CI 通过”不能证明未来 production dependency graph 可复现。

### Lesson 7 — Production 环境约束要视为独立验证层

本机 `8787` 已被已有 Python/Uvicorn 服务占用。

正确处理不是杀掉未知服务，而是：

- 保持 container 内部 8787；
- host 改为 `127.0.0.1:18787`；
- Cloudflare Tunnel 指向新 host port。

原则：

> 遇到 production resource collision 时，优先隔离新服务，不破坏未知既有依赖。

### Lesson 8 — Secrets 是 runtime concern，不是 AI knowledge concern

GitHub App Client Secret 的处理原则：

- 不发送到聊天；
- 不写入 repository；
- 不记录日志；
- AI 只验证 secret 是否已配置、权限是否正确；
- 用户直接在 ChatGPT / runtime UI 中填写 secret。

通用原则：

> AI 可以管理 secret lifecycle 的结构和验证，但不需要知道 secret value。

## Evidence — Real E2E Validation

### Fact

2026-08-27，`GitHub Text MCP v3` 已在真实 ChatGPT 环境完成以下验证：

- Custom MCP 连接成功；
- GitHub OAuth 成功；
- `read_text_file` 与 `list_directory` 已暴露；
- 成功读取 `stanleyrprose/personal-knowledge/GOAL.md`；
- 成功读取 `MAINTENANCE.md`；
- 成功读取 private repo `stanleyrprose/github-text-mcp-bridge/README.md`；
- 结果以内联 MCP `TextContent` 返回；
- 上述读取没有进入 `Allow file materialization?` 流程。

### Model update

原先：

> TextContent 应该可以避免 materialization，但尚未完成真实 ChatGPT E2E。

现在：

> 在当前 ChatGPT + GitHub Text MCP v3 实现中，read-only GitHub text reads 已完成真实 E2E，并验证为 materialization-free read path。

## Preconditions

- MCP client 支持 Remote MCP + inline `TextContent`。
- GitHub OAuth token 对目标 repository 有读取权限。
- MCP server 使用 HTTPS。
- client 能正确 discover protected-resource metadata。
- 文件大小受到上限控制。

## Boundaries / Failure Conditions

1. **这只解决 file materialization gate，不等于消除所有审批。** OAuth 首次授权、普通 tool invocation 或高风险 action 仍可能有客户端 confirmation。
2. TextContent 不是大文件传输方案；大文件应使用 range/search/pagination。
3. 当前验证基于当时的 ChatGPT / MCP / GitHub OAuth 行为；客户端实现变化后要重新验证。
4. Bridge 当前只读；若增加 write tools，必须重新设计 destructive-action confirmation 和 token 权限。
5. direct GitHub OAuth 是本项目当前最简且已验证路径，但不意味着所有 IdP / MCP client 都能复制同一配置。

## Connections

- **Agent Architecture Model**：Tool capability、transport representation、client policy 是不同控制面。
- **Reversible Decision Model**：只替换 read plane，比重建完整 GitHub integration 更可逆。
- **Evidence Ladder**：unit test < protocol simulation < production endpoint < real client E2E。
- **Least Privilege**：read/write plane separation 降低定制桥接服务的权限面。
- **Observability / Failure Recovery**：逐层 instrumentation 比 UI 错误文案更可靠。

## Project Validation

项目：`stanleyrprose/github-text-mcp-bridge`

状态：**v0.1 core goal validated in real ChatGPT**。

PKS 已据此采用：

- **GitHub Text MCP = default read plane**
- **Github MCP = write/control plane**

## Remaining Open Questions

1. GitHub OAuth user token 在长期 ChatGPT Remote MCP 使用中的 refresh / expiration 行为。
2. 大文件的最佳 `read_range` / search API 设计。
3. ChatGPT 产品升级后，materialization / OAuth / MCP transport 行为是否发生变化。
4. 新会话是否能稳定自动恢复 PKS capture protocol，而无需用户显式提醒。
