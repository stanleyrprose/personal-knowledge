# Project Learning Record — GitHub Text MCP Bridge

## Problem / Goal

ChatGPT 通过普通 GitHub MCP 的 repository-file read 路径时，可能触发 `Allow file materialization?` 人工审批，导致 autonomous workflow 被结果处理阶段打断。

目标：构建一个最小 read-only Remote MCP bridge，让 GitHub 文件内容以 inline MCP `TextContent` 返回，而不是 file attachment / `EmbeddedResource`，从而消除 **file materialization 这一特定人工 gate**。

## Current Architecture

采用 **read/write plane separation**：

- `GitHub Text MCP v3`：GitHub 文件与目录只读面
  - `read_text_file`
  - `list_directory`
  - 返回 inline `TextContent`
- `Github MCP`：写入与控制面
  - branch / file update / commit / PR / merge / Actions 等

认证最终采用更简单的 **direct GitHub OAuth**：

`ChatGPT → GitHub OAuth → GitHub user access token → GitHub Text MCP Bridge → GitHub API`

Bridge 当前只作为受保护的 MCP Resource Server，不再要求 ChatGPT 通过 Bridge 自建的 OAuth authorization/token 中转层完成 code exchange。

## Why This Architecture

真正的问题发生在 GitHub file read 的 **result representation / client handling path**，而不是 GitHub 写操作本身。

因此没有必要重写完整 GitHub MCP：

- read path 用 Text MCP 避免 EmbeddedResource / materialization；
- write path 继续复用成熟的 Github MCP；
- 权限与 blast radius 保持最小。

## Evidence

### Fact — implementation

Bridge repository：

`stanleyrprose/github-text-mcp-bridge`

v0.1 核心能力：

- Remote MCP HTTP server
- GitHub OAuth
- `ALLOWED_GITHUB_LOGIN` identity restriction
- `read_text_file`
- `list_directory`
- successful tool result 只返回 `content[].type = text`
- 最大 inline read 1 MB
- binary-content guard
- Docker / Compose
- Cloudflare Tunnel HTTPS deployment
- GitHub Actions CI

### Fact — production deployment

生产 endpoint：

`https://github-mcp.stanleyxyz.com/mcp`

部署过程中确认并修复：

1. dependency drift：增加 `package-lock.json`，Docker / CI 改用 `npm ci`；
2. 本机 `8787` 已被其他服务占用，host binding 改为 `127.0.0.1:18787`；
3. 创建独立 Cloudflare Tunnel `github-text-mcp`；
4. ChatGPT DCR/CIMD 与 Bridge 自建 OAuth authorization-server 兼容性不稳定，最终改为 ChatGPT 直接使用 GitHub OAuth；
5. `express.json()` 已消费 MCP POST body，而 `toNodeHandler()` 未收到 `req.body`，导致 `/mcp` initialize 返回 400；改为显式 `nodeMcpHandler(req, res, req.body)` 后修复 transport handshake。

### Fact — ChatGPT / private repo E2E

2026-08-27：

- `GitHub Text MCP v3` 已在 ChatGPT 中连接成功；
- 本会话通过 `read_text_file` 成功读取 `stanleyrprose/personal-knowledge/GOAL.md` 与 `MAINTENANCE.md`；
- 本会话通过 `read_text_file` 成功读取 private repo `stanleyrprose/github-text-mcp-bridge/README.md`；
- 返回结果为 inline MCP `TextContent`；
- 读取路径未进入 file materialization 工具/附件流程。

因此以下原 Unknown 已转为已验证：

- Remote MCP HTTPS deployment：**已验证**
- ChatGPT OAuth connection：**已验证**
- private repository read：**已验证**
- inline TextContent read path：**已验证**
- PKS 可将 Text MCP 作为默认 GitHub read plane：**已验证**

## What Changed in the Model

### 原先隐含假设

“只要 GitHub MCP 的 tool permission 设为不询问，就可以无人值守读取 GitHub。”

### 新证据

真实 ChatGPT 使用表明，至少存在两个不同的 approval boundary：

1. **Action authorization** — tool 能不能执行；
2. **Result handling authorization** — tool 的返回类型是否触发 file/resource materialization 等客户端 gate。

### 修正后模型

设计 autonomous integrations 时必须同时检查：

`Permission model + Result representation + Client handling policy`

对 GitHub 文本读取，返回 `TextContent` 而不是 `EmbeddedResource` 是 architecture-level control，而不是单纯 permission setting。

## Additional Engineering Lessons

### 1. Discovery success ≠ end-to-end success

OAuth metadata 可正常发现，并不代表 authorization callback、token exchange、MCP initialize、`tools/list` 都兼容。必须逐层观测真实链路。

### 2. Protocol adapter 与 web framework body parser 是独立边界

Express 等框架预先消费 request body 后，MCP Node adapter 必须显式接收 parsed body。否则 OAuth 可完全成功，但 MCP initialize 仍会以 400 失败。

### 3. Deterministic dependency resolution belongs to deployment correctness

没有 lockfile 时，本地/CI/production 的类型定义或 dependency graph 可能漂移。对长期运行的 bridge，应使用 lockfile + `npm ci`。

### 4. Prefer removing unnecessary protocol layers

Bridge 自建 OAuth authorization server 引入了 DCR / CIMD / callback compatibility surface。最终把 OAuth 直接委托给 GitHub，使 Bridge 回到单一职责：**受保护的 read-only MCP Resource Server**。

## Boundary / Failure Conditions

- Text MCP 只解决 GitHub text-file / directory read 的 result representation 问题；普通 MCP tool invocation 仍受 ChatGPT 自身安全策略约束。
- GitHub OAuth 的首次授权仍需用户交互；目标是授权后的持续读取不被每次 materialization 打断。
- 大文件不应无限塞进 TextContent；应优先 search / range read / pagination。
- Bridge 当前保持 read-only；如果增加 write tools，必须重新做 destructive action / token / confirmation 威胁模型。
- GitHub App / OAuth / ChatGPT MCP 行为可能随产品版本变化，需要在真实失败时 evidence refresh。

## PKS Integration Decision

从本次 E2E 之后：

- `stanleyrprose/personal-knowledge` 的默认 GitHub read plane：**GitHub Text MCP**；
- 默认 GitHub write plane：**Github MCP**；
- 不再把 `get_file_contents` 作为 PKS 的默认读取方式；
- 写入前仍遵守 `GOAL.md` / `MAINTENANCE.md` / `AUTO_CAPTURE.md` 与 atomic commit / PR 规则。

## Remaining Knowledge Gaps

1. GitHub OAuth user token 在长期 ChatGPT Remote MCP 使用中的 refresh 行为与失效边界。
2. Text MCP 对大文件的最佳 range/search API 设计。
3. ChatGPT 新会话是否能稳定自动恢复 PKS capture protocol，而无需用户显式提醒。
