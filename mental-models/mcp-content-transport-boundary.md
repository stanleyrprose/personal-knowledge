# MCP Content Transport Boundary Model

**Domain:** AI / Agent / MCP  
**Mastery:** M3  
**Status:** active  

## Why It Matters

在 autonomous agent workflow 中，“工具是否有权限执行”与“工具返回结果是否需要客户端再次人工审批”是两个不同层次的问题。即使 GitHub MCP 的普通 action 已被允许，文件返回类型仍可能触发 ChatGPT 的 file materialization approval，从而打断无人值守执行。

因此，Agent 自动化能力不能只检查 API 权限，还必须检查 **content transport / representation**。

## Mechanism

MCP tool result 的表示方式会影响客户端后续处理路径：

`Tool permission → Tool executes → Result representation → Client handling policy → Human gate / no gate`

本次真实验证中，GitHub 官方 MCP 的文件读取路径返回 file/embedded resource 语义，ChatGPT 随后进入 file materialization 流程并要求人工点击 Allow。

而一个只返回：

`content: [{ type: "text", text: "..." }]`

的 read-only MCP tool 不需要把结果变成 file attachment / EmbeddedResource，因此可以绕开 **file materialization 这一特定审批路径**。

## Key Distinction

### Action authorization

回答：

> 这个 MCP tool 是否允许执行？

可由 App permission / Never ask 等客户端权限策略影响。

### Result materialization authorization

回答：

> MCP 已返回一个文件/资源，客户端是否允许把它装载成会话中的文件？

这是另一道 gate。关闭普通 tool approval 不等价于关闭 materialization approval。

## Design Pattern

对于需要长期无人值守读取 GitHub 内容的 Agent，可采用 read/write plane separation：

`Read plane: custom read-only MCP → inline TextContent`

`Write plane: official GitHub MCP → branch / commit / PR / Actions`

这样把解决 materialization 的定制范围限制在读取侧，不重新实现完整 GitHub 写能力。

## Authentication Pattern

推荐：

`ChatGPT/MCP Client → MCP OAuth 2.1 + PKCE → Text MCP Bridge → GitHub OAuth user flow + PKCE → GitHub API`

对于 GitHub 侧，优先 GitHub App + OAuth user flow，而不是 classic OAuth App：GitHub App 可以限制到指定 repository，并给予 `Contents: Read-only` 等细粒度权限。

Bridge 应避免持久保存长期 GitHub token；能让 MCP client 持有 OAuth token、Bridge 每次验证并转发时，服务端 token-at-rest 风险更小。

## Preconditions

- MCP client 能消费 inline `TextContent`。
- Remote MCP endpoint 可通过 HTTPS 访问。
- OAuth discovery / authorization flow 与目标 MCP client 兼容。
- GitHub token 对目标 repository 有读取权限。
- 文件大小必须受到限制，避免把超大文件直接注入模型 context。

## Boundaries / Failure Conditions

1. **只解决 file materialization gate，不保证消除所有审批。** 客户端仍可能对普通 tool invocation、OAuth 登录或高风险 action 要求人为确认。
2. TextContent 不是大文件传输方案；大内容应分页、截断、搜索后局部读取。
3. GitHub classic OAuth App 的 `repo` scope 很粗，不适合作为最小权限首选。
4. Bridge 如果增加 write tools，风险模型会发生变化，必须重新做权限与审批设计。
5. 客户端实现可能变化，所以“不会触发 materialization”必须通过目标 ChatGPT 客户端做真实端到端验证，而不能只依赖 MCP schema 推断。

## Evidence

### Fact / Project validation

2026-08-25 创建 `stanleyrprose/github-text-mcp-bridge`：

- Remote MCP read-only bridge
- MCP OAuth 2.1 / Authorization Code / PKCE
- upstream GitHub OAuth + PKCE
- `read_text_file`
- `list_directory`
- 两个 tool 只返回 inline MCP TextContent
- regression test 明确检查结果中不存在 `resource`
- GitHub Actions 完成 unit test、TypeScript build 和 Docker build，最终 CI green

### Inference

由于 bridge 输出不包含 EmbeddedResource / file attachment，预期 ChatGPT 不会进入 file materialization 流程。

### Unknown

仍需部署 Remote MCP 并在真实 ChatGPT Custom MCP/App 中完成一次端到端 OAuth + private repo read 测试，确认 UI 中不再出现 `Allow file materialization?`。

## Connections

- Agent Architecture Model：Tools 不只是能力接口，还包含 transport 与 client policy boundary。
- Reversible Decision Model：先仅定制 read plane，避免同时重写 write plane。
- Evidence Ladder：schema-level test 低于真实客户端 E2E，因此当前不能把“ChatGPT 一定不会弹窗”写成 Fact。
- MCP architecture and trust boundaries。

## Project Validation

`stanleyrprose/github-text-mcp-bridge`

当前状态：代码与 CI 已验证；真实 ChatGPT OAuth/E2E 尚待部署后验证。

## Open Questions

- ChatGPT 当前 Remote MCP OAuth 对 2026 MCP CIMD 与 DCR fallback 的实际兼容行为如何？
- 是否仍会出现普通 tool approval，以及 `Never ask` 能否稳定覆盖 read-only tools？
- E2E 成功后，是否值得让长期知识库读取默认切换到 Text Bridge？
