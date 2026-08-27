# Knowledge System Maintenance Protocol v0.3

## 1. Canonical Source of Truth

**Git repository 是长期 canonical knowledge store；当前托管平台是 GitHub。**

GitHub 是当前实现与协作 host，不应被误写成不可替换的知识原则。未来更换 Git host 时，只要 Git history、Markdown knowledge state 与维护协议完整，PKS 的核心 Source of Truth 不应变化。

聊天记录、model memory、Obsidian、本地 Vault、RAG、UI 都是输入、缓存、交互或检索层，不是 canonical knowledge state。

`GOAL.md` 是当前长期目标、checkpoint 与跨会话接力入口；`AUTO_CAPTURE.md` 定义自动知识捕获规则。若 checkpoint 与实际 Git history 冲突，以当前 repository state + Git history 为准，并修正 `GOAL.md`。

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

- **AUTO-CAPTURE**：稳定、高价值、低争议且具有长期复用价值，可维护 Source of Truth。
- **CANDIDATE**：值得保留但证据不足，必须保留 epistemic status，必要时进入 `learning-queue.md`。
- **REJECT**：低长期价值、重复且无 material delta、或触发 visibility/safety hard gate 的内容，不进入知识节点。

优先更新现有知识节点；只有形成稳定独立主题时才新增文件。

## 4. 每次重要学习的处理协议

1. 先回答并解决现实问题。
2. 判断是否触发 Automatic Capture。
3. 读取当前 repository canonical state；若无法读取，fail closed，不写。
4. 执行 Repository Visibility / Safety Gate。
5. 执行 Novelty / Duplicate Exclusion Gate，确认是新增、更新还是 no-op。
6. 定位 Knowledge Map 节点。
7. 连接已有知识。
8. 判断能否抽象或修正 Mental Model。
9. 明确 Epistemic Status 与 evidence。
10. 检查前提、反例和失效条件。
11. 若有真实项目，记录验证方式与结果。
12. 发现的新缺口进入 Learning Queue。
13. 必要时更新 Mastery。
14. 写入前执行 sensitive/secret safety guard，并基于最新 SHA 防 stale overwrite。
15. 使用符合 `AUTO_CAPTURE.md` 的 commit prefix 保存认知变化。
16. 更新 `reviews/capture-log.md`（如该主题进入正式 Capture Gate）。
17. 对产生 Git write 的 Capture 给用户极简 correction receipt。

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
- 结构、高风险或协议变化：feature branch → PR → review → merge。
- 不为整理而整理，不追求形式上的完整。
- 保留历史，不无理由覆盖原有重要判断。
- 认知被修正时，优先保留“旧判断 → 新证据 → 新判断”的演化逻辑。
- 不因自动化能力存在而扩大 scope。
- AUTO-CAPTURE 不得自行修改 Capture / Safety / Maintenance 规则。

## 7. Commit Prefix

- `knowledge:` 长期知识新增或实质更新
- `model:` Mental Model / 决策框架新增或修正
- `hypothesis:` 待验证假设
- `project:` 项目验证经验
- `queue:` 学习缺口调整
- `review:` 复盘、证据刷新、认知重构、capture decision log
- `chore:` 结构与维护协议

## 8. Mandatory Epistemic Status

对新增或实质修改的知识性主张，必须明确：

- **Fact**：有可复核 evidence 支持。
- **Estimate**：基于模型或有限数据估算，并说明关键假设/时间点。
- **Inference**：由事实推导，但并非直接观测。
- **Judgment**：带有目标、权重或价值取舍。
- **Unknown**：当前证据不足。

规则：

- `Fact` 必须能指出直接观测、项目结果、Git/运行证据、可靠一手资料、标准或高质量可复核来源；
- 没有足够 evidence 的综合解释默认最高为 `Inference`；
- 项目验证是强证据，但不是 `Fact` 的唯一来源；
- 改变旧结论时必须保留演化逻辑，禁止静默覆盖；
- 依赖法规、市场、软件版本、价格等变化的 Fact，应显式考虑 staleness / evidence refresh。

## 9. 掌握度管理

- M0：陌生
- M1：理解概念
- M2：理解机制
- M3：能实际使用
- M4：能设计与判断
- M5：能迁移与创新

Mastery 只应标注在真正拥有、使用过或验证过的知识节点上。领域地图里的 Intended Coverage 不等于已经达到任何 Mastery 等级。

## 10. Review

当前阶段使用事件驱动 Review，而不是固定高频自动任务。适合在以下时点执行：

- 已积累若干真实 capture 后
- 重大项目结束后
- 核心模型被现实反例挑战后
- 某领域准备进入重要实际决策阶段时
- 出现 duplicate/retrieval miss、trigger miss、并发冲突或 safety block 等系统摩擦时

Review 检查：

- 哪些模型被现实证伪或需要修正？
- 哪些知识已经从 M1/M2 提升到 M3/M4？
- 哪些 Learning Queue 条目长期没有价值，应删除？
- 是否出现新的跨域连接？
- 哪些项目经验值得沉淀为通用模型？
- 哪些依赖时效的 Fact 已经 stale？
- 是否出现 duplicate node / retrieval miss？
- 是否出现 trigger miss / write-without-read 风险？
- 是否出现 stale write / merge conflict？
- 是否出现 secret / sensitive information block？
- 是否出现 Inference → Fact 漂移？
- capture-log 中是否有高 correction rate 或明显 self-referential bias？

不为了日历自动重写知识库。时间窗口（例如 30–90 天）只是观察 heuristic，不是协议必须满足的 cadence。

## 11. AI Handoff / New Conversation

新 AI 或新对话如触发长期知识维护，优先读取：

1. `GOAL.md`
2. `MAINTENANCE.md`
3. `AUTO_CAPTURE.md`
4. `knowledge-map/master-map.md`（按需要）
5. `mental-models/core-models.md`（按需要）
6. `learning-queue.md`（按需要）
7. `reviews/capture-log.md`（需要评估系统表现时）
8. 最近 commits / PRs

先恢复当前知识结构、目标和维护协议，再继续维护，避免重新建立另一套分类体系。

## 12. Activation Dependency / Fail-Closed Write

Git repository 只能规定“被访问后如何维护”，不能保证每个全新 ChatGPT / AI 对话都会主动访问仓库。

因此需要区分：

- **Canonical storage:** Git repository
- **Activation:** 当前 AI 环境的全局规则 / instruction / user trigger
- **Read adapter:** 当前可用的 Git/MCP read capability
- **Write adapter:** 当前可用的 Git/MCP write capability

Activation 不在 Git 内，这是当前系统真实边界。

### Fail-closed rule

如果当前 AI：

- 没有触发 PKS recovery；或
- 无法读取最新 canonical state；或
- read adapter 失效但 write adapter 仍可用；

则**不得凭聊天记忆、旧摘要或模型 memory 写入 PKS**。

正确行为是：停止 PKS mutation，恢复/更换 read path，或让用户显式触发后再继续。

这条规则用于防止 `write-without-read` 覆盖当前知识。

## 13. Git Read / Write Adapter

当前 ChatGPT 环境默认采用分离的 GitHub tool plane，但这是 **adapter choice，不是 PKS 本体原则**。

### Current read adapter

优先使用 **GitHub Text MCP**：

- `read_text_file`
- `list_directory`

原因：在当前 ChatGPT connector 行为下，它返回 inline `TextContent`，避免 `EmbeddedResource` / file materialization 人工 gate。

### Current write adapter

继续使用 **Github MCP** 完成：

- create/update file
- branch
- commit
- PR / merge
- GitHub control operations

### Invariant

真正不可让步的原则不是“必须使用这两个 MCP”，而是：

`read latest canonical state before write + preserve Git history + minimize write capability surface`

若未来 Claude、本地 Agent、其他 Git host 或 ChatGPT 产品行为变化，可替换 adapter，而不应改写 PKS 核心知识模型。

### Default rule

- 若 GitHub Text MCP 可用，优先使用；
- 若不可用，只能使用能够可靠读取**最新 canonical state**的 fallback；
- 若没有可靠 read path，则禁止 write；
- 不因为 read/write plane 分离而扩大 GitHub 权限。

## 14. Human View / Edit Layer — Obsidian + GitSync

当 GitHub UI 在 Android 上产生真实阅读摩擦后，允许增加 Obsidian 作为 Human View / Edit Layer，但 Git repository 仍是唯一 canonical knowledge store。

### Sync architecture

`Git repo/GitHub → GitSync → Android local folder → Obsidian`

人工编辑返回：

`Obsidian → local Markdown → GitSync commit/push → Git repo/GitHub`

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

Obsidian 的 graph、backlink、plugin、workspace 属于 frontend capability。只有真实使用证明它们改善 retrieval / navigation / editing 时，才进入 PKS architecture。

## 15. Repository Visibility / Data Classification

在 repository 未被明确验证为 private 前，PKS 使用 **PUBLIC_SAFE** 默认模式。

PUBLIC_SAFE 禁止 AUTO-CAPTURE：

- 私人敏感信息
- 财务/账户敏感信息
- 未公开商业、客户、合同、人事或竞争信息
- 第三方非公开信息
- 私人通信原文
- secrets / credentials

只允许保存去敏后的通用机制、模型和公开/可安全共享知识。

如果未来 repository 改为 private，可重新定义允许范围，但 credentials/secrets 仍然禁止进入 Git history。

可见性策略的改变属于 high-risk policy change，必须显式 review，不由 AUTO-CAPTURE 自动决定。

## 16. Pre-write Safety Guard

PKS 的自动写入不得依赖“提交后再发现 secret”的补救模式。

在写入前：

1. 对拟写入内容做语义级敏感信息检查；
2. 若 Github MCP / repository backend 提供并真正启用了 secret scanning，则扫描拟提交内容 / diff；
3. 检测到 credential、API key、token、password、private key、cookie 或 secret 时阻断 write；
4. 去敏后重新检查；
5. 商业敏感、私人或受限信息即使未命中 secret scanner，也必须服从 Visibility Gate。

当前 repository backend 的 GitHub Advanced Security secret scanning 未被验证为可用，因此 scanner 是 capability-dependent enhancement，不是已实现的强制安全层。

## 17. Optimistic Concurrency / Freshness Guard

PKS 可能同时被多个 ChatGPT 会话、AI agent 与 Android/Obsidian 修改，因此不能假设单线程写入。

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
- 修改 `AUTO_CAPTURE.md` 或 `MAINTENANCE.md`；
- 改变 Capture / Safety / Source of Truth / Epistemic Status 规则；
- 大规模改写 `knowledge-map/master-map.md`；
- 跨多个目录的结构重构；
- 解决 human edit 与 AI edit 之间存在实质语义冲突的多文件变更。

`GOAL.md` 的普通 checkpoint / status 更新仍可 atomic commit；如果它同时改变系统规则，则必须随对应 PR 修改。

## 18. Protocol Governance

AUTO-CAPTURE 不得修改自身的 Capture Gate、Safety Gate、Epistemic Status 或 Maintenance constitution。

协议变化必须由显式 review 触发，并走：

`branch → PR → diff review → merge`

这避免系统在一次高置信但错误的对话里自动放宽自己的约束。

## 19. Capture Decision Log / Observability

当前不引入独立 telemetry/database。使用：

`reviews/capture-log.md`

记录正式 Gate 决策和少量 friction signals。

最小观察项：

- **AUTO-CAPTURE / CANDIDATE / REJECT** 数量
- **Non-meta capture**：非 PKS/MCP 自身主题的知识 Capture
- **Duplicate / Retrieval Miss**
- **Capture Correction**
- **Trigger Miss**：事后发现应恢复 PKS 却没有读协议
- **Retrieval Effort Proxy**：定位节点需要读取的文件 / tool-call 数
- **Concurrency Conflict**
- **Safety Block**
- **Human View Friction**

这些是观察指标，不是 KPI。只有持续 friction 才能定义下一版本。

## 20. User Correction Loop

AUTO-CAPTURE / CANDIDATE write 后，在当前对话用一句话告知：

- outcome
- target file
- commit
- epistemic status

并明确用户可以直接纠正。

这是事后可见性，不是写前审批流。
