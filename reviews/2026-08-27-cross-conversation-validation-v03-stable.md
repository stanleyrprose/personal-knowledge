# Knowledge Review — 2026-08-27 Cross-Conversation Validation / PKS v0.3 Stable

## Purpose

验证 PKS v0.3 最后一项 Done Definition：在一个全新对话中，无需用户显式要求“保存到知识库”或重新解释 PKS 规则，AI 能否主动恢复维护协议、判断 Capture、定位已有节点并完成符合规范的 atomic commit。

## Blind-Test Input

测试对话只提供一个工程案例：

- Node / Express + MCP Streamable HTTP
- OAuth 已成功、token 有效
- `POST /mcp initialize` 持续返回 400
- 根因是 `express.json()` 已消费 request body，而 MCP `toNodeHandler` 再次尝试读取 raw body
- 显式把 `req.body` 传给 `toNodeHandler` 后恢复正常

用户只要求抽象成可复用工程原则并分析适用场景与边界，没有要求保存到 PKS。

## Validation Result

### 1. Automatic trigger

**PASS**

新对话主动判断该案例适合沉淀为长期工程知识，而不是一次性 MCP bug 记录。

### 2. Protocol recovery

**PASS**

用户提供的运行截图明确显示新对话主动执行了：

- `Reading PKS guidance before storing the engineering principle`
- `Reading Maintenance Guidelines and Auto-Capture Settings`
- `Reading Auto-Capture Instructions`
- `Reviewing Knowledge Map and Core Models Placement`
- `Reviewed PKS guidance, auto-capture instructions, and model placement`

这证明 AI 在没有用户重复解释 PKS 协议的情况下，主动恢复了 `GOAL.md` / `MAINTENANCE.md` / `AUTO_CAPTURE.md` 及知识节点定位流程。

### 3. Correct Capture / placement

**PASS**

AI 判断为 `AUTO-CAPTURE`，并明确选择已有节点：

`mental-models/core-models.md`

没有新建孤立文件，也没有把该案例保存为项目流水账，而是抽象为：

`Single-Consumer Request Body / Parsed-Body Handoff Model`

模型包含：

- Why it matters
- Mechanism
- How to use
- Preconditions
- Boundaries / failure conditions
- Counterexamples / alternatives
- Project validation

因此满足 Capture Gate 的跨场景复用、机制解释、边界明确和真实项目验证要求。

### 4. Atomic Git write

**PASS**

实际 commit：

`e8d4c305f7e6046a3d2c49282adf16bc99036d32`

Commit message：

`model: add single-consumer request body handoff model`

只修改一个已有文件 `mental-models/core-models.md`，属于符合 v0.3 规则的 atomic knowledge-model update。

## Supporting Infrastructure Validation

本次跨会话验证同时建立在已经完成的 GitHub read/write plane 上：

- GitHub Text MCP v3：`read_text_file` / `list_directory`，inline TextContent
- Github MCP：write / commit / PR / GitHub control plane
- private repository read 已完成 E2E
- GitHub Text MCP read path 未进入 file materialization 流程

因此跨会话恢复不再依赖 GitHub file materialization approval。

## Findings

1. v0.3 的全局 trigger 能在真实新对话中生效，而不是只在构造测试里工作。
2. Capture Gate 能把一次具体 bug 抽象成跨 Express / MCP / webhook / gateway / stream processing 可迁移的工程模型。
3. “优先更新已有节点”在真实对话中得到执行。
4. read plane 与 write plane 分离能支持一次完整的 autonomous knowledge-maintenance loop。
5. 当前没有观察到需要修改 `2-of-8` Capture Gate 的误判。

## Decision

**PKS v0.3 Done Definition 全部满足，发布为 stable。**

当前不升级到新的复杂架构。下一阶段先让 v0.3 在真实使用中持续运行，只有出现明确维护摩擦或模型失效时再引入新的机制。

## Post-v0.3 Watchpoints

后续重点观察：

- false positive：是否过度 AUTO-CAPTURE；
- false negative：是否漏掉高价值长期知识；
- duplicate nodes：是否没有优先更新已有节点；
- stale facts：时效性 Fact 是否长期未刷新；
- OAuth / MCP read-plane 稳定性；
- 知识规模增长后是否真正需要 automated review、RAG / GraphRAG 或 PKM frontend。
