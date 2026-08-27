# /goal — Personal Knowledge System

## Mission

持续打造一个可跨会话、跨 AI、跨项目长期演化的个人知识体系。GitHub repository 是唯一长期 Source of Truth；聊天、临时笔记和外部资料只是输入。

## Current Milestone

**PKS v0.3 — Stable / Minor Hardening**

v0.3 核心目标已达成：ChatGPT / AI 能在真实对话中主动识别长期高价值内容，并按照统一协议自动维护 GitHub 知识模型，而不是等待用户每次明确说“保存”。当前不启动 v0.4，仅吸收真实使用与同行评审中低成本、高确定性的防御性改进。

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
- v0.3 已建立并通过真实验收：
  - `AUTO_CAPTURE.md`
  - AUTO-CAPTURE / CANDIDATE / REJECT 三态判断
  - Capture Gate
  - commit prefix 规范
  - 新对话触发与恢复规则
  - 事件驱动 review loop
  - 首轮三主题 Capture Gate 验证
  - 全新对话自动恢复 / 自动 Capture 盲测
- GitHub read plane 已完成真实 E2E：
  - `GitHub Text MCP v3` 已连接 ChatGPT
  - `read_text_file` / `list_directory` 可用
  - 已成功读取 `personal-knowledge` 文本文件
  - 已成功读取 private repo `github-text-mcp-bridge`
  - 返回 inline `TextContent`，未进入 file materialization 读取路径
  - PKS 默认采用 GitHub Text MCP read plane + Github MCP write plane
- Human View Layer 已被真实摩擦触发：
  - Android 上直接使用 GitHub UI 阅读 PKS 不方便
  - 引入 `Obsidian + GitSync` 作为 Android View / Edit Layer
  - GitHub 继续保持唯一 Source of Truth
  - 新增 `HOME.md` 作为手机友好导航入口
  - `.obsidian/` 等设备/UI 状态默认不进入 Git
- Peer Review Minor Hardening 已吸收：
  - Novelty / Duplicate Exclusion Gate
  - pre-write secret / sensitive information guard
  - SHA-based optimistic concurrency guard
  - high-risk mutation branch / PR policy
  - lightweight operational friction metrics
  - search / frontmatter 保持条件触发，不立即实施

## Operating Loop

`真实问题 → 先解决问题 → 判断 capture 价值 → 通过 Text MCP 读取当前知识状态 → Duplicate / Novelty Gate → 更新既有模型优先 → 检查证据/反例/边界 → safety + freshness guard → 通过 Github MCP commit → GitHub 成为最新 canonical state → Human/AI 后续从 GitHub 同步并复用或修正`

## Rules

1. 不保存大量聊天转录；保存经整理后的稳定知识。
2. 优先沉淀机制、模型、决策框架、边界、反例、项目验证和长期知识缺口。
3. Fact / Estimate / Inference / Judgment / Unknown 必要时明确区分。
4. 自动 capture 的具体判断遵守 `AUTO_CAPTURE.md`。
5. 小型知识更新使用 atomic commit；结构、高风险或核心协议变化使用 branch → PR → review → merge。
6. 优先更新已有节点；重复且无 material delta 的内容不写入。
7. 真实项目是知识验证层，而不是与知识库分离的附件。
8. 新 AI / 新对话触发知识沉淀时，先读取 `GOAL.md` 与 `MAINTENANCE.md`，再按需要读取其他上下文。
9. GitHub 文本读取优先使用 GitHub Text MCP；写入与 GitHub 控制操作使用 Github MCP。
10. Obsidian / GitSync 是 Human View / Edit Layer，不是第二 Source of Truth。
11. 写入前执行敏感信息 / secret safety guard，并基于最新 SHA 防止 stale overwrite。
12. 不把未验证假设写成 Fact；候选内容进入 hypothesis / Learning Queue。
13. 自动维护的目标是提升知识质量，不是增加 commit 数量。

## v0.3 Validation Summary

### Validation 01 — Capture Gate

通过三个真实主题验证：

- AUTO-CAPTURE：Git-backed durable knowledge model
- CANDIDATE：Obsidian / GitSync frontend 仅作为条件触发候选
- REJECT：短期额度、安装/同步步骤等操作事实不进入长期知识库

未发现需要调整 Capture Gate 阈值的真实误判。

### Validation 02 — New-conversation recovery

2026-08-27 在全新对话完成盲测：

- AI 主动识别长期知识价值；
- 主动恢复 PKS guidance / maintenance / auto-capture protocol；
- 主动检查 Knowledge Map / Core Models placement；
- 判断为 AUTO-CAPTURE；
- 更新已有 `mental-models/core-models.md`；
- 通过 atomic commit 保存：
  - `e8d4c305f7e6046a3d2c49282adf16bc99036d32`
  - `model: add single-consumer request body handoff model`

详细证据见：

`reviews/2026-08-27-cross-conversation-validation-v03-stable.md`

### Validation 03 — Peer review hardening

外部 AI 同行评审总体结论为 `Keep v0.3 / Minor Improve`。吸收了无需 v0.4 的防御性改进，拒绝了缺乏测量基础的 80% semantic-similarity hard threshold，并因真实 Android 摩擦已经发生而拒绝“继续 defer Obsidian”。详细 decision record 见：

`reviews/2026-08-27-peer-review-response-01.md`

## Next Priorities

### P0 — 真实运行 v0.3 stable

不再为了验收制造测试主题。让系统在真实工作、学习、工程和决策对话中持续运行，观察：

- duplicate / retrieval miss；
- capture correction（真实误存，而不是简单“30 天没引用”）；
- retrieval effort proxy：定位节点需要读取的文件 / tool-call 数量；
- stale facts；
- stale SHA / merge conflict；
- secret / sensitive information safety block；
- GitHub Text MCP read plane 的长期稳定性；
- Obsidian + GitSync 的 Android 阅读收益与 conflict 成本。

只有真实问题持续出现时再修改协议。

### P1 — Event-driven Review

在以下情况触发 Review：

- 累积若干新的真实 capture；
- 重大项目结束；
- 核心模型被现实反例挑战；
- 某领域准备进入重要实际决策；
- duplicate / retrieval miss 重复发生；
- Human View Layer 出现重复 sync/conflict friction；
- safety guard 或 concurrency guard 频繁触发。

### P2 — Condition-triggered Future Work（当前不实施）

只有真实规模与维护成本证明有需要时，才考虑：

- GitHub Text MCP `search_text` / keyword / line-range search；优先轻量文本搜索，再考虑 semantic retrieval
- 极简 typed frontmatter（如 id / aliases / epistemic_status / last_verified / tags），仅在 metadata 能明确降低检索或 staleness 成本时实施
- 同步少量经过证明有价值的 Obsidian vault 配置
- 定期 staleness / confidence review
- evidence refresh automation
- GitHub Actions 自动检查
- semantic search / RAG / GraphRAG / Knowledge Graph

明确继续 **不实施**：独立 Postgres/SQLite 作为第二 Source of Truth；无真实检索失败证据时的 Vector DB / RAG / GraphRAG。

## Done Definition for v0.3

- [x] 定义自动 capture policy
- [x] 定义 AUTO-CAPTURE / CANDIDATE / REJECT
- [x] 定义 Capture Gate
- [x] 定义 commit prefix
- [x] 定义新对话触发与恢复流程
- [x] 定义事件驱动 Review
- [x] 至少用 3 个真实主题验证 capture 行为
- [x] 建立并验证 materialization-free GitHub Text MCP read plane
- [x] 至少一次在新对话中成功恢复并执行协议
- [x] 根据真实验证结果修订协议并发布 v0.3 stable
- [x] 吸收首轮同行评审中的低成本 safety / duplicate / concurrency hardening

## Handoff

继续本 /goal 时：

1. 查看 `main` 最新 commit 与 open PR。
2. 通过 GitHub Text MCP 读取 `GOAL.md`、`MAINTENANCE.md`、`AUTO_CAPTURE.md`。
3. v0.3 已 stable；不要重新设计整套体系，不因同行建议直接启动 v0.4。
4. 从真实对话继续，只有触发 Capture Gate 时才维护知识库。
5. Capture 前检查 duplicate / material delta；write 前检查 secret / sensitive data 与最新 SHA。
6. 每次只沉淀真正具有长期价值的知识。
7. 写入与 GitHub 控制操作使用 Github MCP。
8. Android / Obsidian 是 Human View / Edit Layer；发生 sync conflict 时以 Git history 与当前知识语义判断，不把本地副本当作新的 Source of Truth。
9. 若本文件与实际仓库状态冲突，以 Git history + 当前文件状态为准，并修正本 checkpoint。
