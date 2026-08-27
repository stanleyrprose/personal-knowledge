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
- v0.3 已建立：
  - `AUTO_CAPTURE.md`
  - AUTO-CAPTURE / CANDIDATE / REJECT 三态判断
  - Capture Gate
  - commit prefix 规范
  - 新对话触发与恢复规则
  - 事件驱动 review loop
- v0.3 真实验收 #1 已完成：
  - AUTO-CAPTURE：Git-backed durable knowledge model
  - CANDIDATE：Obsidian / GitSync frontend 仅作为条件触发候选
  - REJECT：短期额度、安装/同步步骤等操作事实不进入长期知识库
- GitHub read plane 已完成真实 E2E：
  - `GitHub Text MCP v3` 已连接 ChatGPT
  - `read_text_file` / `list_directory` 可用
  - 已成功读取 `personal-knowledge` 文本文件
  - 已成功读取 private repo `github-text-mcp-bridge`
  - 返回 inline `TextContent`，未进入 file materialization 读取路径
  - PKS 默认采用 GitHub Text MCP read plane + Github MCP write plane

## Operating Loop

`真实问题 → 先解决问题 → 判断 capture 价值 → 通过 Text MCP 读取当前知识状态 → 更新既有模型优先 → 检查证据/反例/边界 → 通过 Github MCP commit → 后续对话复用或修正`

## Rules

1. 不保存大量聊天转录；保存经整理后的稳定知识。
2. 优先沉淀机制、模型、决策框架、边界、反例、项目验证和长期知识缺口。
3. Fact / Estimate / Inference / Judgment / Unknown 必要时明确区分。
4. 自动 capture 的具体判断遵守 `AUTO_CAPTURE.md`。
5. 小型知识更新使用 atomic commit；结构变化使用 branch → PR → review → merge。
6. 优先更新已有节点，不为每次讨论创建新文件。
7. 真实项目是知识验证层，而不是与知识库分离的附件。
8. 新 AI / 新对话触发知识沉淀时，先读取 `GOAL.md` 与 `MAINTENANCE.md`，再按需要读取其他上下文。
9. GitHub 文本读取优先使用 GitHub Text MCP；写入与 GitHub 控制操作使用 Github MCP。
10. 不把未验证假设写成 Fact；候选内容进入 hypothesis / Learning Queue。
11. 自动维护的目标是提升知识质量，不是增加 commit 数量。

## Next Priorities

### P0 — 完成跨会话真实验收

在一个全新对话中验证：

- 全局触发规则能否主动识别长期高价值知识；
- AI 能否自行通过 GitHub Text MCP 读取 `GOAL.md` / `MAINTENANCE.md` / `AUTO_CAPTURE.md`；
- 无需用户重复解释 PKS 规则即可完成正确 capture / candidate / reject；
- 写入能否通过 Github MCP 完成符合当前协议的 atomic commit；
- 整个过程不依赖 file materialization。

### P1 — 根据跨会话验证结果决定是否发布 v0.3 stable

当前首轮 3 主题验证未发现 Capture Gate 阈值需要调整。只有出现真实误判时才修改 Gate。

### P2 — Future Work（当前不实施）

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
- [x] 至少用 3 个真实主题验证 capture 行为
- [x] 建立并验证 materialization-free GitHub Text MCP read plane
- [ ] 至少一次在新对话中成功恢复并执行协议
- [ ] 根据真实验证结果修订协议并发布 v0.3 stable

## Handoff

继续本 /goal 时：

1. 查看 `main` 最新 commit 与 open PR。
2. 通过 GitHub Text MCP 读取 `GOAL.md`、`MAINTENANCE.md`、`AUTO_CAPTURE.md`。
3. 不重新设计整套体系；从 `Next Priorities` 继续。
4. 每次只沉淀真正具有长期价值的知识。
5. 写入与 GitHub 控制操作使用 Github MCP。
6. 若本文件与实际仓库状态冲突，以 Git history + 当前文件状态为准，并修正本 checkpoint。
