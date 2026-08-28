# Knowledge Review — 2026-08-28 Peer Review Response 03

## Input

外部 AI 对 PKS v0.3 的第三轮架构评审，整体结论：

**Keep v0.3 + Minor Improve；暂缓 v0.4，优先真实场景运行实验。**

评审再次肯定：

- Git repository 作为 canonical Source of Truth
- 保存认知模型而不是聊天记录
- 项目实践作为知识验证层
- Read/Write Plane Separation
- Event-driven Review
- Friction-driven evolution
- Update Existing Node First

并重点担忧：

- retrieval scaling
- Capture Gate drift
- epistemic status decay
- stale knowledge
- sensitive-data capture
- MCP/OAuth long-term stability
- AI 主观判断缺少人类抽样制衡

## Overall Decision

**ACCEPT overall direction. Do not start v0.4.**

本轮大多数 Must Fix / Should Improve 已经由前两轮评审落地，因此不重复制造新规则。只吸收仍有 material delta 的少量治理增强。

## Already Covered — No New Change Needed

以下建议当前协议已经覆盖，因此本轮不重复实施：

1. **Sensitive information hard reject**
   - 已有 Repository Visibility Gate + Pre-write Safety Gate。
   - secrets / credentials 永久 REJECT；PUBLIC_SAFE 下私人/未公开商业信息也禁止自动进入 Git history。

2. **Duplicate check before capture**
   - 已有 Novelty / Duplicate Exclusion Gate。
   - 重复且无 material delta → REJECT/no-op；有新增 evidence/boundary/correction → update existing node。

3. **Mandatory epistemic status**
   - 已从“必要时”升级为新增/实质修改的知识主张必须明确 Fact / Estimate / Inference / Judgment / Unknown。

4. **Capture observability**
   - 已有 `reviews/capture-log.md`。
   - 已明确 log 不能单独测 false negative / recall；recall 需要抽样回看真实对话。

5. **Review checks for duplicate / stale / drift**
   - Event-driven Review 已包含 duplicate/retrieval miss、Inference→Fact drift、stale facts、trigger miss、conflict、safety block 等。

6. **High-risk protocol changes use PR**
   - `AUTO_CAPTURE.md` / `MAINTENANCE.md` / Capture/Safety/Epistemic rules 已强制 branch → PR → review → merge。

7. **Search/RAG/frontmatter defer**
   - lightweight grep/search 仍是 condition-triggered candidate；RAG/GraphRAG/Vector DB/第二数据库继续不做。

## Accepted — Material Delta Implemented

### 1. Epistemic Promotion Gate

**Decision: ACCEPT.**

现有 Mandatory Epistemic Status 可以防止新条目无状态，但还不够明确约束“后续 AI 把 CANDIDATE / Inference 静默升级为 Fact”。

新增规则：

`CANDIDATE / Inference / Judgment / Unknown → Fact / AUTO-CAPTURE / INSTANTIATED MODEL`

必须同时记录：

`旧状态 → 新 evidence → 新状态`

没有新增 evidence，不允许只靠更自信的措辞完成 status promotion。

如果 promotion 会显著改变核心 Mental Model、Knowledge Map 的 Possessed/Instantiated 状态或重大真实决策依据，则走 branch → PR。

### 2. Time-sensitive Fact gets a minimal time anchor

**Decision: ACCEPT, without frontmatter migration.**

政策、法规、市场、价格、软件/产品版本等 time-sensitive Fact，正文至少记录：

- `As of YYYY-MM-DD`；或
- evidence/source date；或
- 明确适用版本/时间窗口。

这能降低 silent staleness 风险，同时不把 Markdown 提前 schema 化。

### 3. Human sampling during experimental phase

**Decision: ACCEPT as lightweight governance, not approval workflow.**

Long-term PKS System 仍处于 experimental / real-use observation，因此 Event-driven Review 时人类优先抽样：

- `mental-models/` 的实质变更；
- `knowledge-map/` Possessed/Instantiated 状态变化；
- epistemic promotion；
- 用户曾纠正的 Capture；
- 高影响真实决策结论。

目标是发现 Capture drift 和 AI self-reinforcement，不要求用户审批每个普通 commit。

## Modified / Rejected

### 1. “所有 mental-models 修改都必须 branch + PR”

**Decision: REJECT blanket rule; ACCEPT risk-based version.**

如果所有小 evidence 补充和措辞修正都走 PR，会让正常知识演化过度笨重。

保留：

- 普通 evidence 补充 / 小修正 → atomic commit
- core model 实质重写 / epistemic promotion / node merge-split / map status change → branch + PR

### 2. “Obsidian / GitSync 继续 defer”

**Decision: REJECT as stale advice.**

Android 上 GitHub 阅读不方便这一真实摩擦已经发生，所以 Obsidian + GitSync 已按原 friction-driven 规则进入 Human View/Edit Layer；Git repo 仍是 canonical Source of Truth。

### 3. Rigid 90-day gate

**Decision: ACCEPT observation spirit, REJECT rigid calendar requirement.**

30–90 天可以作为观察窗口，但 protocol 仍采用 event-driven review。不要为了满足日历制造测试或 review churn。

### 4. Typed metadata for all candidates now

**Decision: DEFER.**

当前通过正文 Epistemic Status + promotion evidence + time anchor 已能解决主要风险。只有真实检索/staleness friction 证明机器可读 metadata 有 ROI 时再引入。

## Current Experimental Focus

下一阶段不继续建设 PKS 自身，而是观察真实使用：

- 非 PKS/MCP 的真实领域知识是否自然被 Capture / Reject / 修正；
- 重要知识是否 trigger miss；
- existing node 是否能被找到；
- epistemic promotion 是否真正由 evidence 驱动；
- stale Fact 是否造成错误使用；
- user correction 是否能稳定被吸收；
- Obsidian/GitSync 与 AI write 是否产生 conflict；
- MCP/OAuth/read adapter 是否出现长期失效。

## v0.4 Decision

**No v0.4 now.**

未来任何 v0.4 必须由重复、可观察的 failure mode 定义，例如：

- repeated retrieval miss → lightweight search
- repeated duplicate nodes → duplicate-assist tooling
- repeated stale Fact errors → optional time/confidence metadata
- repeated human/AI conflict → sync/write-path hardening

不是因为“某功能先进”而升级。