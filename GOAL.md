# /goal — Personal Knowledge System

## Mission

持续打造一个可跨会话、跨 AI、跨项目长期演化的个人知识体系。GitHub repository 是唯一长期 Source of Truth；聊天、临时笔记和外部资料只是输入。

## Current Milestone

**PKS v0.2 — Continuity & Capture**

目标：让任何未来 AI 在读取仓库后，都能准确恢复当前结构、维护规则、学习重点与下一步，而不需要用户重新解释。

## Current State

- v0.1 已建立：
  - `knowledge-map/master-map.md`
  - `mental-models/core-models.md`
  - `learning-queue.md`
  - `MAINTENANCE.md`
- v0.2 新增：
  - `/goal` 持久 checkpoint
  - 项目实践模板
  - 定期复盘模板
  - 标准知识条目模板

## Operating Loop

`真实问题 → 判断是否值得沉淀 → 定位知识节点 → 连接已有知识 → 抽象机制/模型 → 检查反例与边界 → 项目验证 → 更新掌握度 → 进入 Source of Truth / Learning Queue`

## Rules

1. 不保存大量聊天转录；保存经整理后的稳定知识。
2. 优先沉淀机制、模型、边界、反例和可迁移结论。
3. Fact / Estimate / Inference / Judgment / Unknown 必要时明确区分。
4. 小改动可直接原子 commit；结构变化使用 branch → PR → review → merge。
5. 不为目录完整而创建低价值内容。
6. 真实项目是知识验证层，而不是与知识库分离的附件。
7. 新 AI 接手时先读取本文件，再读 README、Master Map、Mental Models、Learning Queue、Maintenance 与最近 commits/PRs。

## Next Priorities

### P0 — 使用真实学习内容验证 v0.2

从后续真实对话中选择高价值主题，按统一模板沉淀，避免为了填库而制造内容。

### P1 — 建立领域节点的渐进拆分

仅当某领域内容足够多时，再从 `master-map.md` 拆出独立文件，例如：

- `knowledge-map/telecom/`
- `knowledge-map/ai-agents/`
- `knowledge-map/business/`
- `knowledge-map/thinking/`

### P2 — 建立知识复习与陈旧度机制

当条目数量足够多后，再设计 review cadence、staleness、confidence 与 evidence refresh；目前不提前复杂化。

## Done Definition for v0.2

- [x] 有可恢复的 `/goal` checkpoint
- [x] 有项目实践模板
- [x] 有 review 模板
- [x] 有知识条目最小模板
- [ ] 用至少 3 个真实学习主题验证 capture 流程
- [ ] 根据验证结果修订模板，再发布 v0.2

## Handoff

继续本 /goal 时：

1. 查看 `main` 最新 commit 与 open PR。
2. 读取 `GOAL.md`。
3. 不重新设计整套体系；从 `Next Priorities` 继续。
4. 每次只沉淀真正长期有价值的知识。
5. 若本文件与实际仓库状态冲突，以 Git history + 当前文件状态为准，并修正本 checkpoint。
