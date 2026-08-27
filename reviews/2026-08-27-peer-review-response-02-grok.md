# Knowledge Review — 2026-08-27 Peer Review Response 02 (Grok)

## Input

独立 AI 对 PKS v0.3 做了 repository-level peer review，并明确把 Git history 当证据，而不是接受“v0.3 Stable”标签作为先验。

核心结论：

**Keep v0.3. Do not build v0.4. Protocol can freeze; long-term system is still experimental.**

## Overall Decision

**ACCEPT direction.**

本次不启动 v0.4，而是做一次 observability / epistemic / safety hardening，然后冻结 protocol，让真实使用产生下一阶段证据。

## Accepted — Implement Now

### 1. Separate protocol validation from system maturity

**Accepted.**

原 `PKS v0.3 Stable` 过度概括了证据。

修正为：

- `Capture / Maintenance Protocol v0.3 — Validated & Frozen`
- `Long-term PKS System — Experimental`

已经完成的盲测证明 ChatGPT 内 protocol recovery；不证明跨 AI、跨领域、长期数月连续性。

### 2. Visibility strategy before real private/domain knowledge

**Accepted, with safe default rather than automatic repo-visibility mutation.**

当前协议新增 `PUBLIC_SAFE` 默认模式：在 repository 未明确验证为 private 前，个人敏感、财务/账户、未公开商业/客户/第三方信息和私人通信原文全部 REJECT，只允许去敏后的通用机制/模型。

本次 **不自动改变 repository visibility**。可见性改变属于用户级、高影响 policy decision，应显式决定。

### 3. Capture Decision Log

**Accepted.**

新增 `reviews/capture-log.md`，只记录真正进入 Gate 的 AUTO-CAPTURE / CANDIDATE / REJECT。

这是为了让 correction、duplicate、trigger miss、non-meta ratio 等可被复盘，而不是新增 telemetry database。

重要边界：REJECT 仍不创建知识节点；log 只是 operational evidence。

### 4. Epistemic Status becomes mandatory

**Accepted, modified.**

知识性主张必须标 `Fact / Estimate / Inference / Judgment / Unknown`。

`Fact` 必须有可复核 evidence。

但拒绝“没有项目验证的机制最高只能 Inference”这一绝对规则：可靠标准、一手资料、直接观测也可以支持 Fact；项目验证只是强 evidence 之一。

### 5. Possessed vs Intended knowledge map

**Accepted.**

`master-map.md` 原先主要是课程/领域大纲，却容易被 future AI 误读成已有知识。

现在显式拆成：

- Possessed / Instantiated
- Intended Coverage

Intended Coverage 不代表 mastery，也不自动构成 existing node。

### 6. Models 1–8 downgrade to reference stubs

**Accepted.**

当前 `core-models.md` 的 1–8 只有简短教科书式提纲，没有完整 evidence / boundaries / project validation。

现在统一标为 `REFERENCE STUB / Unknown / not yet instantiated`。

Models 9–10 保持 instantiated，并补充 Epistemic Status + Evidence。

### 7. Fail-closed write / write-without-read protection

**Accepted.**

如果 trigger 没生效、read adapter 不可用、或只能写不能读：禁止凭 memory /旧摘要直接修改 PKS。

Invariant：

`read latest canonical state before write`

### 8. Protocol files / self-modification PR gate

**Accepted and strengthened.**

AUTO-CAPTURE 不得自动修改 Capture Gate / Safety Gate / Maintenance constitution。

协议修改必须 explicit review + branch + PR + diff review。

### 9. Lightweight user correction loop

**Accepted.**

Capture write 后回复：outcome + file + commit + epistemic status，并允许用户直接纠正。

不是写前审批流。

### 10. GitHub is host/adapter, Git repo is principle

**Accepted.**

当前 GitHub Text MCP + Github MCP read/write separation 在 ChatGPT 2026 环境下仍保留，但它们是 adapter，不是 PKS 本体教条。

PKS invariant：canonical Git state + latest-read-before-write + Git history + minimal write surface。

## Accepted as Watchpoint / Defer

### Cross-AI validation

值得做，但不制造人工测试工程。等存在合适的另一 AI + Git 环境时，做一次真实 handoff。

### Lightweight grep/search

仍是最可能的下一个 read-plane enhancement，但只有 repeated retrieval miss / read amplification 出现后实施。

### 30–90 day observation

接受“先长期真实使用”的精神，不把 90 天变成 protocol calendar gate。Event-driven review 保留。

## Rejected / Not Worth Implementing Now

- 不启动 v0.4；
- 不引入 Vector DB / RAG / GraphRAG / Knowledge Graph；
- 不做定时 AI 自动整理；
- 不同步第二套 canonical store；
- 不为了实验刻意制造电信/商业/工程测试题；
- 不做 typed frontmatter migration；
- 不把 Text MCP 扩成 write plane；
- 不为了“地图完整”批量扩写 Intended Coverage。

## Important Modification to Peer Advice

### “90 天必须有指定 7 类样本”

**Modified.**

这些样本类型是很好的 coverage checklist，但不能成为“必须制造的测试任务”，否则会重新污染实验。

正确做法：真实对话自然出现时记录；90 天后若样本仍全是 PKS/MCP 元主题，则把它视为系统未进入真实使用的负面 evidence。

### “整个 PKS 必须立刻 private”

**Not auto-executed.**

风险判断成立，但 repository visibility 是用户层面的高影响选择。本次先 fail safe：PUBLIC_SAFE hard gate。是否切 private 需要显式决定。

## New System Boundary

现在必须区分四层：

`Canonical storage = Git repo`

`Activation = AI environment / instructions / user trigger`

`Read adapter = current Git/MCP capability`

`Write adapter = current Git/MCP capability`

Git 解决 durable state，不自动解决 activation。

## Current Decision

**Freeze Protocol v0.3 after this patch.**

下一阶段不问“还能加什么”，只观察：

- 非元知识是否自然增长并被复用；
- 重要知识是否漏 capture；
- trigger 是否失效；
- retrieval 是否失败；
- 用户是否纠正 capture；
- Inference 是否漂移成 Fact；
- Human/AI sync 是否产生真实冲突。

如果未来 v0.4 出现，它必须由一个**重复发生、可观察的 failure mode**定义。
