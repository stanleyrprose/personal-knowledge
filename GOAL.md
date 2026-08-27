# /goal — Personal Knowledge System

## Mission

持续打造一个可跨会话、跨 AI、跨项目长期演化的个人知识体系。**Git repository 是 canonical knowledge store；当前托管与协作平台是 GitHub。** 聊天、model memory、Obsidian、临时笔记和外部资料只是输入、交互或检索层。

## Current Milestone

**Capture / Maintenance Protocol v0.3 — Validated & Frozen**  
**Long-term PKS System — Experimental / Real-use Observation**

这一区分是刻意的：目前已经证明协议可以在真实 ChatGPT 新对话中恢复并执行，但还没有足够证据证明跨 AI、跨领域、长期数月运行的知识连续性已经稳定。

当前不启动 v0.4。只允许低成本 observability / safety / epistemic hardening，之后转入真实使用。

## Current State

### Protocol foundation

- v0.1：Knowledge Map / Mental Models / Learning Queue / Maintenance
- v0.2：`/goal` checkpoint / project-review templates / AI handoff
- v0.3：
  - `AUTO_CAPTURE.md`
  - AUTO-CAPTURE / CANDIDATE / REJECT
  - Capture Gate
  - Novelty / Duplicate Exclusion Gate
  - Mandatory Epistemic Status
  - commit prefix
  - new-conversation recovery
  - event-driven review
  - optimistic concurrency / latest-SHA guard
  - visibility / sensitive-data guard
  - protocol self-modification PR gate
  - capture decision log + correction receipt

### Validated in ChatGPT

- GitHub Text MCP v3 `read_text_file` / `list_directory` 可用；
- private repository read 已做 E2E；
- inline `TextContent` read path 未进入 file materialization；
- Github MCP write / atomic commit 可用；
- 全新 ChatGPT 对话曾主动恢复 PKS protocol 并 AUTO-CAPTURE 到已有 node；
- read/write adapter separation 在当前 ChatGPT 产品约束下有效。

### Not yet validated as a long-term system

- 不同 AI（非 ChatGPT）是否能仅靠 repo handoff 正确恢复；
- 电信、商业、投资等非 PKS/MCP 领域是否会被正确 Capture / Reject / 修正；
- trigger 在长期、多设备、connector/token 变化后是否稳定；
- 知识规模增长后 retrieval 是否仍可靠；
- 用户 correction、capture precision/recall proxy 是否长期可接受；
- Human/AI 同时写入时 conflict 成本是否可控。

### Human View Layer

Android 上 GitHub UI 阅读不方便这一真实摩擦已经触发：

- `Obsidian + GitSync` 作为 Android View / Edit Layer；
- Git repo 继续是 canonical state；
- `HOME.md` 作为手机入口；
- `.obsidian/` 等设备/UI 状态默认不进 Git；
- graph/backlink/plugin 不因“存在”而自动升级为 PKS 架构。

## Knowledge-State Reality Check

当前 repository 的高质量 durable knowledge 仍主要集中在：

- Git-backed durable knowledge model
- MCP content transport / materialization boundary
- Single-consumer request-body handoff model
- GitHub Text MCP Bridge project validation
- PKS 自身 maintenance / review evidence

因此当前最大的实验风险不是“功能不够”，而是 **self-referential capture diet**：系统不断沉淀 PKS/MCP 自身，而真实领域知识仍然稀薄。

`knowledge-map/master-map.md` 已明确区分：

- **Possessed / Instantiated Knowledge**：已有条目、证据或真实使用；
- **Intended Coverage**：希望学习/连接的领域，不代表已经拥有或掌握。

## Operating Loop

`真实问题 → 先解决 → 判断 capture → read latest canonical state → visibility/safety → duplicate/material delta → epistemic status/evidence → update existing node → latest SHA → atomic commit or PR → capture log → user correction receipt`

如果无法读取最新 canonical state：**fail closed，不写。**

## Rules

1. 不保存大量聊天转录；保存经整理后的稳定知识。
2. 优先沉淀机制、模型、决策框架、边界、反例、项目验证和长期知识缺口。
3. 新增/实质修改的知识性主张必须标 Fact / Estimate / Inference / Judgment / Unknown。
4. 自动 capture 遵守 `AUTO_CAPTURE.md`；AUTO-CAPTURE 不得修改自己的规则。
5. 小型知识更新使用 atomic commit；结构、高风险、协议变化使用 branch → PR → review → merge。
6. 优先更新已有节点；重复且无 material delta 的内容不写入。
7. 新 AI / 新对话触发知识沉淀时，先读 `GOAL.md` / `MAINTENANCE.md` / `AUTO_CAPTURE.md`。
8. 无可靠 read path 时禁止 write-without-read。
9. GitHub Text MCP + Github MCP 是当前 ChatGPT adapter，不是不可替换的 PKS 本体原则。
10. Obsidian / GitSync 是 Human View / Edit Layer，不是第二 Source of Truth。
11. repository visibility 未明确验证为 private 前，默认 PUBLIC_SAFE；敏感/未公开内容不 AUTO-CAPTURE。
12. 写入前执行 safety + latest SHA guard。
13. 正式 Capture Gate 决策写入 `reviews/capture-log.md`；Capture write 后给用户可纠正回执。
14. 自动维护的目标是提升知识质量，不是增加 commit 数量。

## Validation Summary

### Validation 01 — Three-state Gate

已验证 Gate 能产生 AUTO-CAPTURE / CANDIDATE / REJECT，不要求每个主题都产生知识 commit。

### Validation 02 — New-conversation protocol recovery

2026-08-27 在全新 ChatGPT 对话完成盲测：AI 主动恢复 protocol、检查 existing node、AUTO-CAPTURE，并提交：

`e8d4c305f7e6046a3d2c49282adf16bc99036d32`  
`model: add single-consumer request body handoff model`

**Evidence supports:** protocol recovery in ChatGPT.  
**Evidence does not yet support:** long-term cross-AI / cross-domain system stability.

### Validation 03 — Peer-review hardening 01

已吸收 duplicate / safety / concurrency hardening；拒绝伪精确 80% semantic threshold；search/frontmatter/RAG 保持 condition-triggered。

### Validation 04 — Grok independent review

独立评审指出：协议打通不等于系统 Stable；知识地图混合 intended/possessed；缺 capture observability；epistemic status 过于可选；activation 不在 Git 内。

处理记录：

`reviews/2026-08-27-peer-review-response-02-grok.md`

## Next Priorities

### P0 — Real-use observation, not more architecture

让 PKS 处理真实、非元问题。观察并记录：

- AUTO-CAPTURE / CANDIDATE / REJECT
- non-meta capture ratio
- duplicate / retrieval miss
- capture correction
- trigger miss
- retrieval effort proxy
- Inference → Fact drift
- stale SHA / merge conflict
- safety block
- Obsidian/GitSync friction

不制造测试主题，不为了“证明 PKS 有用”补全领域地图。

### P1 — Real-domain evidence

只有真实工作/学习对话自然出现时，优先观察以下类型是否能正确进入或退出 PKS：

- Telecom / infrastructure mechanism or decision model
- Business / investment judgment with explicit Judgment / Unknown
- 与 PKS/MCP 无关的工程经验
- 一次真实 REJECT
- 一次旧判断被新证据修正
- 一次用户对 AUTO-CAPTURE 的纠正/否决

不要求为了实验刻意制造这些主题。

### P2 — Cross-AI handoff validation

当有合适的不同 AI + Git read/write 环境时，做一次真正 cross-AI handoff：只给 repo / handoff 指令，看它能否读取协议、找到 existing node、避免重建分类。

失败时先改 handoff/runbook，不立即上新架构。

### P3 — Condition-triggered future work

只有持续真实 friction 才考虑：

1. GitHub Text MCP lightweight `search_text` / keyword / line-range search
2. minimal typed metadata/frontmatter
3. staleness automation
4. semantic retrieval
5. RAG / GraphRAG / Knowledge Graph

明确不实施：

- 为完整性补全所有地图节点
- 第二 canonical database
- 自动 mastery 打分
- 定时 AI 重写整个知识库
- 把 Text MCP 扩成 write plane

## Protocol v0.3 Done Definition

- [x] 定义 automatic capture policy
- [x] 定义 AUTO-CAPTURE / CANDIDATE / REJECT
- [x] 定义 Capture Gate
- [x] 定义 duplicate/material-delta gate
- [x] mandatory epistemic status
- [x] visibility / safety guard
- [x] optimistic concurrency guard
- [x] protocol high-risk PR gate
- [x] new-conversation recovery流程
- [x] event-driven Review
- [x] Git read/write adapter E2E in ChatGPT
- [x] 至少一次 ChatGPT 新对话成功恢复并执行协议
- [x] capture decision log / correction receipt protocol

## System Maturity — Not Done

- [ ] 多个真实非元领域 capture/reject/correction 样本
- [ ] 长期 trigger reliability evidence
- [ ] cross-AI handoff validation
- [ ] retrieval scaling evidence
- [ ] user correction / capture quality evidence
- [ ] human/AI sync conflict evidence

## Handoff

继续本 `/goal` 时：

1. 读取 main 最新 commit / open PR。
2. 读取 `GOAL.md`、`MAINTENANCE.md`、`AUTO_CAPTURE.md`。
3. 明确：**protocol v0.3 frozen；system experimental。** 不把 protocol validation 当成 long-term system maturity。
4. 从真实问题继续，不为了 PKS 制造题目。
5. Capture 前检查 visibility / duplicate / epistemic status；write 前检查最新 canonical state / SHA / safety。
6. 普通知识 atomic commit；协议或 high-risk mutation 走 PR。
7. Capture write 后给用户 correction receipt。
8. 若 repository state 与本文件冲突，以 Git history + 当前 state 为准并修正 checkpoint。
