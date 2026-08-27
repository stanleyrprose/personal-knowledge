# Knowledge Review — 2026-08-27 Peer Review Response 01

## Input

一份外部 AI 对 PKS v0.3 的同行评审，整体结论：

**Keep v0.3 / Minor Improve**

评审肯定：

- Git-backed Durable Source of Truth
- GitHub Text MCP read plane + Github MCP write plane
- Epistemic Status
- Update Existing Node First

并提出：

- retrieval scalability
- Capture Gate duplicate risk
- concurrent writes
- pre-write secret guard
- typed frontmatter
- lightweight grep/search
- high-risk branch/PR
- 90-day observation metrics

## Overall Decision

**ACCEPT overall verdict: Keep v0.3 / Minor Hardening.**

不启动 v0.4。只吸收当前已经可以低成本执行、且不会增加第二 Source of Truth 的防御性改进。

## Accepted

### 1. Retrieval scalability is a real future risk

**Decision:** ACCEPT as watchpoint / condition-triggered work, not current bug.

`list_directory + read_text_file` 在当前规模工作正常，但文件数量增长后可能出现：

- retrieval miss
- duplicate nodes
- read amplification
- cold-start latency / context overhead

因此将 lightweight `search_text` / keyword / line-range search 加入 Learning Queue。

**Not implemented now.** 只有真实 retrieval friction 出现后才扩展 GitHub Text MCP。

### 2. Duplicate risk requires a stronger exclusion rule

**Decision:** ACCEPT concept, MODIFY implementation.

新增 `Novelty / Duplicate Exclusion Gate`：

- 已有节点包含同一核心结论；
- 新内容没有新增 evidence / boundary / counterexample / correction / decision impact / project validation；
- 则 `REJECT / no-op`；
- 有 material delta 时更新 existing node。

这比单纯“Update Existing Node First”更明确地允许系统选择 **不写**。

### 3. Pre-write secret guard

**Decision:** ACCEPT.

写入前：

- 先做敏感信息语义检查；
- 若 Github MCP 提供 secret scanning，则扫描拟提交内容 / diff；
- 命中 credential / key / token / password / private key 等则阻断写入；
- 去敏后重新扫描。

重要边界：这目前是 **write-protocol hard gate**，不是 GitHub server-side pre-receive hook。若未来真实出现 bypass，再考虑把 guard 下沉到独立 write proxy / GitHub protection layer。

### 4. Concurrent writes need explicit protection

**Decision:** ACCEPT.

现有 Github MCP 对 existing-file update 要求 SHA，本身已经提供了 optimistic concurrency 原语，但之前维护协议没有把它提升为明确系统规则。

现在正式规定：

`fresh read → latest SHA → write → stale SHA means re-read / merge, never force overwrite`

多文件和高风险变更走 branch → PR → review → merge。

### 5. High-risk mutation policy

**Decision:** ACCEPT / strengthen existing rule.

原协议已经要求结构变化走 PR，本次把高风险类别显式化：

- delete / rename / move
- node merge / split
- `AUTO_CAPTURE.md` / `MAINTENANCE.md` 协议修改
- master-map 大规模重构
- 跨目录重构
- 多文件语义 conflict resolution

### 6. Keep Vector DB / RAG / external DB out for now

**Decision:** ACCEPT.

当前没有证据说明轻量 Git/Markdown retrieval 已经失效。继续坚持：

`direct text → lightweight search → semantic retrieval only if proven necessary`

独立 Postgres / SQLite 不作为第二 canonical state。

## Modified / Partially Accepted

### 1. Reject numeric “semantic overlap >= 80%” hard threshold

**Peer suggestion:** semantic overlap >= 80% 且无新边界/反例则 REJECT。

**Decision:** REJECT numeric threshold, ACCEPT duplicate exclusion principle.

原因：当前系统没有一个稳定、可审计、跨模型一致的语义相似度测量器。写 `80%` 会制造 false precision，并可能让未来 AI 把主观判断伪装成数学事实。

替代：使用 `material delta` 判据。

### 2. Typed frontmatter

**Peer suggestion:** 现在引入 `id, aliases, epistemic_status, last_verified, tags`。

**Decision:** DEFER.

这些字段可能有长期价值，但当前知识规模不足以证明 migration 成本值得承担。

触发条件：

- duplicate / alias resolution 真实变成问题；
- staleness review 需要机器可读 `last_verified`；
- metadata filtering 能显著降低全文读取成本。

如果触发，优先从极简字段开始，不一次性 schema 化所有 Markdown。

### 3. False Positive metric

**Peer suggestion:** 30 天内从未被引用或修正的“死节点”比例。

**Decision:** REJECT as false-positive definition.

“30 天没被引用”不代表知识低价值；低频但高杠杆模型可能很久才再次使用。

替代指标：**Capture Correction**——后续 Review 明确判断某次 capture 应被删除、降级、合并或实质纠正的事件。

### 4. Token cost metric

**Peer suggestion:** 单次 Capture 平均 Token 开销。

**Decision:** ACCEPT intent, MODIFY metric.

精确 token 成本在当前 connector/tool 链路未必可稳定观察。先使用：

- 为定位目标节点读取的文件数；
- tool-call 数；
- 是否发生明显 read amplification。

如未来有可靠 telemetry，再升级到 token/time/cost 指标。

### 5. 90-day rigid experiment

**Decision:** ACCEPT observation spirit, REJECT rigid calendar as protocol requirement.

采用大约 30–90 天真实运行窗口是合理 heuristic，但 PKS 当前仍坚持 event-driven review；不为了满足日历而制造 review。

## Rejected / Stale Advice

### Defer Obsidian / GitSync

**Decision:** REJECT as stale relative to current evidence.

之前 PKS 的确把 Obsidian 设为 CANDIDATE / defer，但 2026-08-27 已出现明确真实摩擦：Android 上 GitHub Markdown 阅读体验不方便。

因此 Obsidian + GitSync 已经按照原先定义的触发条件进入实施，而且保持：

- GitHub = Source of Truth
- Obsidian = Human View / Edit Layer
- `.obsidian/` 默认不进 Git

这正是 friction-driven architecture evolution 的一个成功案例，而不是对旧策略的背离。

## Operational Watchpoints Adopted

v0.3 继续运行时记录：

1. **Duplicate / Retrieval Miss** — 新 AI 没找到实际已有节点；
2. **Capture Correction** — 后续证据证明某次 capture 低价值或错误；
3. **Retrieval Effort Proxy** — 定位节点需要读取的文件 / tool-call 数；
4. **Concurrency Conflict** — stale SHA / merge conflict；
5. **Safety Block** — secret / sensitive info 被 pre-write guard 阻断；
6. **Human View Friction** — Obsidian/GitSync 的 sync 与 conflict 成本。

不建立独立 telemetry database；先在真实事件中通过 Review / Git history 观察。

## Current v0.4 Decision

**Do not start v0.4.**

下一版本必须由持续发生的真实 friction 定义，而不是由功能清单定义。

最可能的第一个候选是 lightweight GitHub Text MCP content search，但只有以下证据出现后才实施：

- repeated retrieval miss；
- repeated duplicate nodes；
- read amplification 已明显影响延迟或 context；
- 当前目录/文本读取方式成为真实瓶颈。

## Result

本次同行评审没有改变 PKS v0.3 的核心架构，而是强化了三个 failure boundary：

`Duplicate / Novelty → Safety → Concurrency`

新的维护链路为：

`Capture value → existing-node check → material delta → sensitive/secret guard → latest SHA → atomic write or branch/PR`

这是 Minor Hardening，不是 v0.4。
