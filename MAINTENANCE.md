# Knowledge System Maintenance Protocol v0.3

## 1. Source of Truth

GitHub 是长期 Source of Truth。聊天记录是输入，不是最终知识资产。

`GOAL.md` 是当前长期目标、checkpoint 与跨会话接力入口；`AUTO_CAPTURE.md` 定义自动知识捕获规则。若 checkpoint 与实际 Git history 冲突，以当前仓库状态和 Git history 为准，并修正 `GOAL.md`。

## 2. 什么值得进入知识库

优先记录：

- 可跨场景复用的机制
- 心智模型与决策框架
- 经验证的重要结论
- 经纠错后的稳定结论
- 重要边界与失效条件
- 能挑战既有判断的重要反例
- 项目实践改变了原有认知的内容
- 高价值长期知识缺口

默认不记录：

- 短期新闻本身
- 一次性查询结果
- 无证据且无长期价值的临时观点
- 大量原始聊天转录
- 临时操作步骤
- 仅属于某项目日常执行的 task log

## 3. Automatic Capture

所有自动 capture 判断遵守 `AUTO_CAPTURE.md`。

处理分为：

- **AUTO-CAPTURE**：稳定、高价值、低争议且具有长期复用价值，可直接维护 Source of Truth。
- **CANDIDATE**：值得保留但证据不足，必须保留 epistemic status，必要时进入 `learning-queue.md`。
- **REJECT**：低长期价值或重复且无 material delta 的内容，不进入仓库。

优先更新现有知识节点；只有形成稳定独立主题时才新增文件。

## 4. 每次重要学习的处理协议

1. 先回答并解决现实问题。
2. 判断是否触发 Automatic Capture。
3. 读取必要的仓库状态，避免重复或与既有模型冲突。
4. 执行 Novelty / Duplicate Exclusion Gate，确认是新增、更新还是 no-op。
5. 定位 Knowledge Map 节点。
6. 连接已有知识。
7. 判断能否抽象或修正 Mental Model。
8. 检查前提、反例和失效条件。
9. 若有真实项目，记录验证方式与结果。
10. 发现的新缺口进入 Learning Queue。
11. 必要时更新 Mastery。
12. 写入前执行敏感信息 / secret safety guard。
13. 使用符合 `AUTO_CAPTURE.md` 的 commit prefix 保存认知变化。

## 5. 知识条目的最小质量标准

长期知识条目优先回答：

- Why it matters
- Mechanism
- How to use
- Preconditions
- Boundaries / failure conditions
- Counterexamples / alternatives
- Evidence
- Connections
- Project validation
- Open questions

不要求每个条目机械填满所有字段，但不能只保存定义或结论。

## 6. 变更原则

- 小型知识补充：直接 atomic commit。
- 一个 commit 尽量表达一个清晰认知变化或紧密相关主题。
- 跨目录结构调整或大规模重构：feature branch → PR → review → merge。
- 不为整理而整理，不追求形式上的完整。
- 保留历史，不无理由覆盖原有重要判断。
- 认知被修正时，优先保留“旧判断 → 新证据 → 新判断”的演化逻辑。
- 不因自动化能力存在而扩大 scope。

## 7. Commit Prefix

- `knowledge:` 长期知识新增或实质更新
- `model:` Mental Model / 决策框架新增或修正
- `hypothesis:` 待验证假设
- `project:` 项目验证经验
- `queue:` 学习缺口调整
- `review:` 复盘、证据刷新、认知重构
- `chore:` 结构与维护协议

## 8. Fact / Estimate / Inference / Judgment / Unknown

知识条目在必要时明确区分：

- **Fact**：有可靠证据支持。
- **Estimate**：基于模型或有限数据估算。
- **Inference**：由事实推导，但并非直接观测。
- **Judgment**：带有目标、权重或价值取舍。
- **Unknown**：当前证据不足。

依赖法规、市场、软件版本、价格等变化的事实，应显式考虑 staleness / evidence refresh。

## 9. 掌握度管理

- M0：陌生
- M1：理解概念
- M2：理解机制
- M3：能实际使用
- M4：能设计与判断
- M5：能迁移与创新

学习投入依据用途决定，不追求所有节点满级。

## 10. Review

当前阶段使用事件驱动 Review，而不是固定高频自动任务。适合在以下时点执行：

- 已积累若干真实 capture 后
- 重大项目结束后
- 核心模型被现实反例挑战后
- 某领域准备进入重要实际决策阶段时
- 出现重复检索失败、并发冲突或 safety guard block 等系统摩擦时

Review 检查：

- 哪些模型被现实证伪或需要修正？
- 哪些知识已经从 M1/M2 提升到 M3/M4？
- 哪些 Learning Queue 条目长期没有价值，应删除？
- 是否出现新的跨域连接？
- 哪些项目经验值得沉淀为通用模型？
- 哪些依赖法规、市场、软件版本等变化的 Fact 已经 stale？
- 是否出现 duplicate node / retrieval miss？
- 是否出现 stale write / merge conflict？
- 是否出现 secret / sensitive information block？

知识规模扩大、维护成本实际出现后，再评估月度 cadence、confidence、staleness 与 GitHub Actions 自动检查。

## 11. AI Handoff / New Conversation

新 AI 或新对话如触发长期知识维护，优先读取：

1. `GOAL.md`
2. `MAINTENANCE.md`
3. `AUTO_CAPTURE.md`
4. `knowledge-map/master-map.md`（按需要）
5. `mental-models/core-models.md`（按需要）
6. `learning-queue.md`（按需要）
7. 最近 commits / PRs

先恢复当前知识结构、目标和维护协议，再继续维护，避免重新建立另一套分类体系。

## 12. Global Trigger Dependency

GitHub 仓库只能规定“被访问后如何维护”，不能保证每个全新 ChatGPT 对话都会主动访问仓库。

因此跨对话自动化依赖一个仓库外的全局触发规则：当对话出现长期高价值知识时，主动使用 GitHub MCP 维护 `stanleyrprose/personal-knowledge`，并先读取 `GOAL.md` 与 `MAINTENANCE.md`。

如果某个 ChatGPT 环境没有该全局触发能力，则用户可用“继续长期知识库”或“记录到长期知识库”显式触发；GitHub 内部协议仍保持一致。

## 13. GitHub Read / Write Plane

PKS 默认采用分离的 GitHub tool plane：

### Read plane

优先使用 **GitHub Text MCP**：

- `read_text_file`
- `list_directory`

理由：Text MCP 返回 inline MCP `TextContent`，不进入 `EmbeddedResource` / file attachment materialization 路径，适合跨会话自动恢复和无人值守读取。

### Write plane

继续使用 **Github MCP** 完成：

- create/update file
- branch
- commit
- PR / merge
- GitHub Actions 等写入或控制操作

### Default rule

- 读取 `personal-knowledge` 或其他 GitHub 文本内容时，若 GitHub Text MCP 可用，默认不要使用 `get_file_contents`。
- 写入前仍必须遵守 Capture Gate、epistemic status、atomic commit / PR 规则。
- 若 Text MCP 不可用，才允许使用其他可用读取路径作为 fallback，并明确记录该环境限制。
- 不因为 read/write plane 分离而扩大知识库 scope 或 GitHub 权限。

## 14. Human View / Edit Layer — Obsidian + GitSync

当 GitHub UI 在 Android 上产生真实阅读摩擦后，允许增加 Obsidian 作为 Human View / Edit Layer，但 **GitHub repository 仍是唯一 canonical Source of Truth**。

### Sync architecture

`GitHub → GitSync → Android local folder → Obsidian`

人工编辑返回：

`Obsidian → local Markdown → GitSync commit/push → GitHub`

### Rules

1. 打开 Obsidian 前先 pull / sync，减少编辑 stale copy 的概率。
2. 编辑完成后尽快 commit + push，缩短 human/AI divergent-edit window。
3. `.obsidian/`、`.trash/` 和其他设备/UI 状态默认不进入 PKS。
4. 同一 Vault 不同时使用 GitSync 与 Obsidian Git plugin 管理同一个 `.git` repository。
5. 内部导航优先使用标准 Markdown 相对链接；Obsidian-only wiki link 不能成为唯一导航形式。
6. 不启用 `*.md merge=union` 等会静默拼接知识冲突的策略。
7. 发生 conflict 时保留冲突事实，比较 human edit、AI commit 与 Git history 后再解决。
8. 如果未来需要同步少量 Obsidian 设置，应逐项证明长期价值，而不是直接提交整个 `.obsidian/`。

### Boundary

Obsidian 的 graph、backlink、plugin、workspace 属于 frontend capability。只有真实使用证明它们改善 retrieval / navigation / editing 时，才进入 PKS architecture；不能因为 Obsidian 支持这些功能就默认启用。

## 15. Pre-write Safety Guard

PKS 的自动写入不得依赖“提交后再发现 secret”的补救模式。

在写入前：

1. 对拟写入内容做语义级敏感信息检查；
2. 若 Github MCP 提供 `run_secret_scanning` 或同类能力，对拟提交内容 / diff 进行模式扫描；
3. 检测到 credential、API key、token、password、private key、cookie 或 secret 时阻断 write；
4. 去敏后重新扫描；
5. 商业敏感、私人或受限信息即使未命中 secret scanner，也不得因 AUTO-CAPTURE 自动进入仓库。

Scanner 是辅助 guard，不是安全边界的全部。

## 16. Optimistic Concurrency / Freshness Guard

PKS 现在可能同时被多个 ChatGPT 会话、AI agent 与 Android/Obsidian 修改，因此不能假设单线程写入。

### Existing-file update

- 更新已有文件前，先读取当前最新版本及 SHA；
- 使用 Github MCP 更新已有文件时必须基于该最新 SHA；
- 如果 SHA stale / write conflict，禁止 force overwrite；
- 必须重新读取最新版本，比较并合并语义差异，再重新提交。

这相当于以 Git blob SHA 作为 lightweight optimistic concurrency control。

### Multi-file / high-risk change

以下默认使用 branch → PR → review → merge：

- 删除、重命名或移动长期知识文件；
- 批量合并或拆分 knowledge nodes；
- 修改 `AUTO_CAPTURE.md` 或 `MAINTENANCE.md` 等核心维护协议；
- 大规模改写 `knowledge-map/master-map.md`；
- 跨多个目录的结构重构；
- 解决 human edit 与 AI edit 之间存在实质语义冲突的多文件变更。

`GOAL.md` 的普通 checkpoint / status 更新仍可 atomic commit；如果它同时改变系统规则，则随对应 structural PR 一起修改。

## 17. Operational Metrics — Lightweight Only

当前不引入独立 telemetry/database。只在真实事件发生时记录以下 friction signals：

- **Duplicate / Retrieval Miss**：AI 新建或准备新建一个实际已有的概念；
- **Capture Correction**：后续 Review 证明某次 capture 应被降级、删除或改写；
- **Retrieval Effort Proxy**：为定位目标节点需要读取多少文件 / 发起多少 tool calls；不强求不可观测的精确 token 成本；
- **Concurrency Conflict**：stale SHA、merge conflict、Android 与 AI divergent edit；
- **Safety Block**：secret / sensitive information 在写入前被阻断；
- **Human View Friction**：Obsidian/GitSync sync 或 conflict 是否形成重复维护成本。

只有这些信号持续出现，才升级检索、元数据或自动 review 架构。
