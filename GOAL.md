# /goal — Personal Knowledge System

## Mission

持续打造一个可跨会话、跨 AI、跨项目长期演化的个人知识体系。GitHub repository 是唯一长期 Source of Truth；聊天、临时笔记和外部资料只是输入。

## Current Milestone

**PKS v0.3 — Automatic Knowledge Capture**

目标：让 ChatGPT / AI 在真实对话中主动识别长期高价值内容，并按照统一协议自动维护 GitHub 知识模型，而不是等待用户每次明确说“保存”。

## Current State

- v0.1 已建立：
  - `knowledge-map/master-map.md`
  - `mental-models/core-models.md`
  - `learning-queue.md`
  - `MAINTENANCE.md`
- v0.2 已建立：
  - `/goal` 持久 checkpoint
  - 项目实践模板
  - 定期复盘模板
  - 标准知识条目模板
  - 跨会话 AI handoff 流程
- v0.3 新增：
  - `AUTO_CAPTURE.md`
  - AUTO-CAPTURE / CANDIDATE / REJECT 三态判断
  - Capture Gate
  - commit prefix 规范
  - 新对话触发与恢复规则
  - 事件驱动 review loop

## Operating Loop

`真实问题 → 先解决问题 → 判断 capture 价值 → 读取当前知识状态 → 更新既有模型优先 → 检查证据/反例/边界 → commit → 后续对话复用或修正`

## Rules

1. 不保存大量聊天转录；保存经整理后的稳定知识。
2. 优先沉淀机制、模型、决策框架、边界、反例、项目验证和长期知识缺口。
3. Fact / Estimate / Inference / Judgment / Unknown 必要时明确区分。
4. 自动 capture 的具体判断遵守 `AUTO_CAPTURE.md`。
5. 小型知识更新使用 atomic commit；结构变化使用 branch → PR → review → merge。
6. 优先更新已有节点，不为每次讨论创建新文件。
7. 真实项目是知识验证层，而不是与知识库分离的附件。
8. 新 AI / 新对话触发知识沉淀时，先读取 `GOAL.md` 与 `MAINTENANCE.md`，再按需要读取其他上下文。
9. 不把未验证假设写成 Fact；候选内容进入 hypothesis / Learning Queue。
10. 自动维护的目标是提升知识质量，不是增加 commit 数量。

## Next Priorities

### P0 — 用真实对话验证 v0.3

至少观察 3 个真实高价值主题，验证：

- 是否能正确区分 AUTO-CAPTURE / CANDIDATE / REJECT
- 是否优先更新已有节点
- 是否避免把短期事实误写成长期知识
- commit 是否能清楚表达认知变化

### P1 — 根据验证结果调整 Capture Gate

只依据真实误判调整阈值，不预先增加复杂评分系统。

### P2 — 验证跨会话恢复

在后续新对话中确认：触发长期知识沉淀时，AI 能从 `GOAL.md` / `MAINTENANCE.md` / Git history 恢复当前维护协议，而无需重新设计体系。

### P3 — Future Work（当前不实施）

只有知识规模与实际维护成本证明有需要时，才考虑：

- Obsidian / GitSync 前端同步
- 定期 staleness / confidence review
- evidence refresh automation
- GitHub Actions 自动检查
- RAG / GraphRAG / Knowledge Graph

## Done Definition for v0.3

- [x] 定义自动 capture policy
- [x] 定义 AUTO-CAPTURE / CANDIDATE / REJECT
- [x] 定义 Capture Gate
- [x] 定义 commit prefix
- [x] 定义新对话触发与恢复流程
- [x] 定义事件驱动 Review
- [ ] 至少用 3 个真实主题验证 capture 行为
- [ ] 至少一次在新对话中成功恢复并执行协议
- [ ] 根据真实验证结果修订协议并发布 v0.3 stable

## Handoff

继续本 /goal 时：

1. 查看 `main` 最新 commit 与 open PR。
2. 读取 `GOAL.md`、`MAINTENANCE.md`、`AUTO_CAPTURE.md`。
3. 不重新设计整套体系；从 `Next Priorities` 继续。
4. 每次只沉淀真正具有长期价值的知识。
5. 若本文件与实际仓库状态冲突，以 Git history + 当前文件状态为准，并修正本 checkpoint。
