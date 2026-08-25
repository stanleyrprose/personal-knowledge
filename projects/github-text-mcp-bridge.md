# Project Learning Record — GitHub Text MCP Bridge

## Problem / Goal

ChatGPT 通过官方 GitHub MCP 读取 repository file 时，会出现 `Allow file materialization?` 人工审批，导致 autonomous workflow 无法连续执行。

目标：构建一个最小 read-only Remote MCP bridge，让 GitHub 文件内容以 inline MCP `TextContent` 返回，而不是 file attachment / EmbeddedResource，从而消除 **file materialization 这一特定人工 gate**。

## Hypothesis

如果 MCP tool result 只返回 `TextContent`，不返回 `EmbeddedResource` / `ResourceLink` / connector file reference，则 ChatGPT 不需要进入 file materialization path。

同时，为避免 PAT，Bridge 应使用 OAuth：

- MCP client ↔ Bridge：OAuth 2.1 Authorization Code + PKCE
- Bridge ↔ GitHub：GitHub OAuth web flow + PKCE

## Relevant Knowledge Nodes

- Agent Architecture Model
- Reversible Decision Model
- Evidence Ladder
- MCP Content Transport Boundary Model
- MCP architecture and trust boundaries

## Decision Model

采用 **read/write plane separation**：

- Text Bridge 只负责 read-only GitHub file/directory reads
- 官方 GitHub MCP 继续负责 branch / commit / PR / merge / Actions 等写操作

理由：真正的问题只发生在文件读取结果的 transport representation，没有必要为解决一个 read-path friction 重写整套 GitHub MCP。

认证优先选择 **GitHub App + OAuth user flow**，而不是 classic OAuth App：GitHub App 可以限定 repository，并设置 `Contents: Read-only`，最小化 OAuth token 权限。

## Evidence

### Fact

2026-08-25 已创建 private repository：

`stanleyrprose/github-text-mcp-bridge`

v0.1 实现：

- MCP 2026 Remote HTTP server
- MCP OAuth discovery
- Authorization Code + PKCE
- DCR fallback
- CIMD client metadata support
- upstream GitHub OAuth + PKCE
- `ALLOWED_GITHUB_LOGIN` identity restriction
- `read_text_file`
- `list_directory`
- tool result 仅返回 `content[].type = text`
- 最大 inline read 1 MB
- binary-content guard
- Dockerfile / compose
- GitHub Actions CI

PR #1 最终 CI green：

- PKCE regression test
- TextContent / no-resource regression test
- TypeScript build
- Docker build

最终 squash merge：

`b29ad84cf74c7dba423c804d06ebc4028281616a`

### Inference

由于 MCP tool result 中没有 EmbeddedResource / attachment semantics，ChatGPT 应不会触发 file materialization approval。

### Unknown

尚未把 Remote MCP 真正部署到 HTTPS endpoint 并连接 ChatGPT，因此“真实 ChatGPT UI 不再出现 materialization prompt”仍未完成 E2E 验证。

## Action / Experiment

1. 创建独立 bridge repo，避免修改官方 GitHub MCP。
2. 只实现两个 read-only tool，严格控制 scope。
3. 使用 OAuth-first，拒绝 PAT 作为长期架构。
4. regression test 显式断言 tool result 是 `TextContent` 且不存在 `resource`。
5. 用 GitHub Actions 做最小 CI。
6. 第一次 CI 因 workflow cache/lockfile 前置配置问题未进入真实测试；移除不必要 npm cache 后继续。
7. 为解决 GitHub MCP 无法直接读取 Actions 原始失败日志的问题，在 CI 中增加失败时把日志回贴 PR 的机制，保证后续 AI 可以恢复失败原因。
8. CI green 后 squash merge。

## Result

### 已完成

- Bridge source code complete
- OAuth + PKCE implementation complete
- unit/regression test complete
- TypeScript build complete
- Docker build complete
- PR merged to main

### 尚未完成

- GitHub App registration
- `GITHUB_CLIENT_ID` / `GITHUB_CLIENT_SECRET`
- public HTTPS deployment
- ChatGPT Remote MCP registration
- OAuth login E2E
- 实际 private repository read
- 确认不再出现 materialization approval

## What Changed in the Model

### 原先隐含假设

“只要 GitHub MCP 的 tool permission 设为不询问，就可以无人值守读取 GitHub。”

### 新证据

真实 ChatGPT UI 显示：GitHub file read 在 tool 已执行后仍会进入独立的 `file materialization` approval。

### 修正后模型

无人值守 Agent workflow 至少存在两个不同的 approval boundary：

1. **Action authorization** — tool 能不能执行
2. **Result handling authorization** — tool 返回的数据是否会触发 file/resource materialization 等客户端 gate

因此设计 autonomous integrations 时，必须同时检查：

`Permission model + Result representation + Client handling policy`

## Boundary / Failure Conditions

- 本方案只证明代码层能产生 TextContent，不等于已经证明 ChatGPT E2E 一定不弹 materialization。
- GitHub OAuth/App 的首次授权仍需要用户交互；目标是授权完成后的持续运行不被每次 file read 打断。
- ChatGPT 仍可能对普通 MCP tool invocation 维持自己的安全审批策略。
- 如果未来 Bridge 增加 write tools，需要重新做权限、token、destructive action 与 confirmation 威胁模型。
- 大文件不能简单全部塞进 TextContent；后续应优先 search / range read / pagination，而不是提高上限。

## New Knowledge Gaps

1. ChatGPT 当前对 MCP 2026 CIMD / DCR 的实际 OAuth compatibility。
2. GitHub App OAuth user token 在 ChatGPT Remote MCP 场景中的刷新与长期运行行为。
3. `Never ask` 与 read-only custom MCP tool 的实际作用边界。
4. TextContent E2E 是否完全绕开 materialization UI。
5. 若 E2E 成功，是否把 `personal-knowledge` 和工程 repo 的默认 read plane 切换到 Text Bridge。
