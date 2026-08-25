# Knowledge Review — 2026-08-25 Auto Capture Validation 01

## Purpose

使用三个来自真实对话、长期价值不同的主题验证 PKS v0.3 的 AUTO-CAPTURE / CANDIDATE / REJECT 三态判断，重点检查系统是否会为了“完成沉淀指标”而过度保存内容。

## Topic 1 — Git-backed durable knowledge as Source of Truth

**Outcome:** AUTO-CAPTURE

### Gate

满足：

- 可跨多个场景复用
- 会提升长期 AI 协作与恢复质量
- 解释机制而非单纯工具事实
- 明确了 frontend / storage 分层和失效条件

### Action

更新 `mental-models/core-models.md`，新增 `Git-backed Durable Knowledge Model`。

### Why

这是可迁移的系统设计原则：把 conversation / UI / model memory 与 canonical knowledge state 分离，通过 Markdown + Git history 保存稳定知识和认知演化。

## Topic 2 — 是否现在引入 Obsidian + GitSync

**Outcome:** CANDIDATE / DEFER

### Gate

它可能提升未来的人类阅读、移动编辑和双向同步体验，但当前没有真实摩擦证明复杂度值得引入。

### Action

不把它写成当前架构结论；仅在 `learning-queue.md` 的 `Deferred / Condition-triggered` 中记录触发条件。

### Why

这是一个条件依赖的工具选择，而不是稳定事实。当前最合理判断是继续验证 `ChatGPT + GitHub MCP`，只有出现实际 frontend/sync 痛点时再升级。

## Topic 3 — GitHub Actions 免费额度、GitSync 安装/同步步骤等短期操作事实

**Outcome:** REJECT

### Gate

主要是会变化的产品额度、安装步骤或一次性操作信息；除非未来抽象出稳定机制，否则不满足长期 capture 条件。

### Action

不写入 Knowledge Map、Mental Models 或 Learning Queue。

### Why

保存这类内容会增加 staleness 和维护成本，却不能显著改善长期认知模型。

## Validation Findings

1. 三态 Gate 能产生不同结果，而不是把所有“看起来有用”的内容都写入仓库。
2. `AUTO-CAPTURE` 能优先更新已有 Mental Model 库，而不是新建孤立主题文件。
3. `CANDIDATE` 可以被降级为条件触发的 Learning Queue，而不伪装成当前结论。
4. `REJECT` 本身是成功结果；真实验收不应要求每个测试主题都产生 commit。
5. 当前 `2-of-8` Capture Gate 未发现需要调整的真实误判，因此暂不修改阈值。

## Model Changes

新增一个稳定模型：Git-backed durable knowledge architecture。

没有把 Obsidian/GitSync 升格为当前架构要求；其状态保持 deferred。

## Learning Queue Changes

新增条件触发候选：Human-facing PKM frontend for the Git knowledge base。

## Next Validation

仍需完成：

- 至少一次在**全新对话**中，由全局触发规则主动识别长期知识价值；
- 自动读取 `GOAL.md` / `MAINTENANCE.md` / `AUTO_CAPTURE.md`；
- 在无需用户重复说明仓库规则的情况下完成一次正确 capture 或 reject；
- 完成后再决定是否发布 v0.3 stable。
