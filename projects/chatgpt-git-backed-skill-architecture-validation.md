# ChatGPT Git-backed Skill Architecture — Project Validation

**Status:** ACTIVE / project-validated  
**Epistemic Status:** Fact（项目证据） + Inference（跨任务泛化） + Judgment（当前采用策略）  
**As of:** 2026-09-08

## Why this matters

此前 PKS 的 Model 11 已经形成了 `Agent Skill Composition / Context-Budget Model`，但主要证据来自对 `mattpocock/skills` 的观察与架构推导。本次项目把它推进到一套真实可运行的 ChatGPT Git-backed Skill Architecture，并完成 repo implementation、Custom Instructions migration、CI、smoke 与真实工程 E2E。

这次 material delta 不在于“又提出了一套 Skill 理论”，而在于验证了：

`Constitution / Router + Small Global Skills + Project Git State + Tool/Runtime Evidence`

可以在真实工程上下文中工作，并且能处理 current-state authority、Primary/Secondary composition、degraded tool path 与测试环境限制。

## Architecture actually implemented

```text
Global Constitution
  → Global Reusable Skills
  → Project Git Constitution（AGENTS / ADR / CONTEXT）
  → Current Project State（GOAL / PRD / issue / Git / CI）
  → Authorized Tools / Runtime
  → Evidence / Verification
```

Canonical implementation repo: `stanleyrprose/chatgpt-skills`.

### Canonical ownership

- `CONSTITUTION.md`：跨项目行为、authority、执行语义、degraded-mode 规则。
- `SKILL.md`：每个 Skill 的 canonical metadata + reusable HOW。
- `REGISTRY.md`：由 Skill frontmatter 生成的 discovery index；不是第二事实源。
- `.agents/invocation.md`：跨 Skill invocation mechanics。
- Project `AGENTS/GOAL/PRD/ADR/CONTEXT`：项目规则与项目当前状态。
- Git/CI/runtime evidence：当前实现事实与验证证据。

核心区分：

`Constitution = intent semantics / global behavior`  
`Skill = reusable HOW`  
`Tool/MCP = Capability`  
`Runtime = Execution`

## Invocation model validated

初始 5 个 Global Skills：

| Skill | Invocation | Role |
|---|---|---|
| implement | user | 软件/repo 工程实施 orchestration |
| to-spec | user | review-first PRD/spec 生成 |
| handoff | user | reference-first project handoff |
| diagnose | model | defect/root-cause discipline |
| code-review | model | spec fidelity + engineering standards review |

### Composition invariant

最多一个 Primary；automatic Secondary 只能是 model-invoked，并必须服务同一顶层 goal。

已验证链路：

```text
implement Primary
  → code-review Secondary
  → pop
  → diagnose Secondary
  → pop
  → implement closure
```

这验证了一个重要设计：diagnosis/review 可以作为 bounded branch，而不重定义 implementation 的 top-level goal。

## Project validation evidence

### Phase 1–2 — implementation

`stanleyrprose/chatgpt-skills` 已实现：

- Global Constitution
- generated Registry
- five initial Skills
- invocation mechanics
- shared Git/testing references
- validator + GitHub Actions CI

PR #1 merge 到 `main`；合并后的 GOAL state correction 又通过独立 PR #2 完成。

### Phase 3 — lossless Custom Instructions migration

第一次 v1.5.1 过度追求字符压缩，实际丢失了多项长期行为语义，包括：

- Execution Mode 不得退化为只给步骤；
- 完整 YAGNI / Minimal Sufficient Architecture 语义；
- Mac mini 优先 CodexPro；
- traceable/recoverable/handoff Git workflow；
- “普通技术选择不是 Hard Stop”；
- 第二大脑 trigger 必须 CodexPro → `second-brain-write`，不能当 ChatGPT Memory。

最终策略改为 **lossless-first migration**：先完整保留旧 Custom Instructions 的行为语义，再只追加新 Skill Architecture 的必要机制。

v1.5.2 Constitution ≈ 3326 Unicode chars，仍处在 3500 总预算内；Router 的 800-char 约束被降为非阻断观测指标。

### Phase 4 — real engineering E2E

没有使用 toy repo，而选择真实活跃的 `stanleyrprose/mac-browser-plane`。

**Fact / direct project evidence:**

- CodexPro 读取到 Mac 本地 `feat/lightpanda-engine-router` 的未提交修改，因此 local current state 正确压过远端旧假设；
- `tests/test_core.py`：26/26 PASS；
- 当前环境可运行的 core + launchd 测试：27/27 PASS；
- full pytest 因当前 Python 环境缺 optional `agent` extra `mcp==2.1.1` 在 collection 阶段失败；该失败被归类为 **environment coverage limitation**，没有被误写为 Lightpanda regression；
- CodexPro `show_changes` 出现 workspace-selection inconsistency 时，系统没有猜 diff，而是降级到明确 `read/test`；
- Lightpanda routing implementation / manifest / doctor 边界一致：ephemeral C1 与 read-only C3 可 Lightpanda → Chrome safe fallback；persistent profile、C2、interactive C3、screenshot/download/force 固定 Chrome。

### Phase 5 — real-use observation

上线后不继续添加架构，而观察接下来 10 个自然发生的真实任务：

- Primary routing 是否正确；
- false positive / false negative；
- Secondary 是否合理；
- sticky context；
- Project Discovery；
- degraded routing；
- user correction。

禁止人为制造 10 个测试任务“刷完成”。只有持续真实 friction 才能支持下一版 Future Work。

## Durable lessons

### 1. Freeze != implementation authorization

**Fact（项目纠错）：** 早期曾在没有明确 freeze 的情况下过早创建 repo/branch。

**Judgment:** PRD freeze 与 implementation authorization 应保持独立；前者固定设计基线，后者授权行为。

### 2. Lossless migration before compression

**Fact（项目纠错）：** v1.5.1 压缩导致长期执行语义丢失。

**Inference:** 对长期 Custom Instructions / Constitution 的迁移，字符预算只能作为优化约束，不能优先于行为等价。

### 3. Natural-language semantics are not all Skills

“继续 / 按你的建议 / Autonomous Mode”等更适合作为 Constitution intent semantics，而不是制造额外 Skills。

### 4. Derived indexes must stay derived

`SKILL.md` canonical；`REGISTRY.md` generated + CI drift check。否则 discovery index 会演化成第二 Source of Truth。

### 5. Real-state evidence beats remembered project state

本地 Git/runtime/CI evidence 应压过 stale docs / chat memory；Memory 只用于定位，不得用来覆盖 canonical project state。

### 6. Degradation should preserve epistemic honesty

Tool failure、workspace selection 异常、optional dependency 缺失不应被“补全成成功”或猜测代码状态；应缩小可验证范围并明确 limitation。

### 7. Do not test architecture by manufacturing work

真实 E2E 的价值来自真实 authority、dirty workspace、dependency limitation 和工具故障；toy workflow 很难验证这些边界。

## Updated connection to Model 11

本项目**强化但不完全证明** Model 11 的跨 Agent 泛化。

**Fact:** 当前 ChatGPT + GitHub MCP + CodexPro 环境中，该模式已经有真实实施和 E2E 证据。

**Inference:** `薄 Constitution/Router + 少量 small Skills + progressive disclosure + Git/CI/runtime feedback loop` 很可能比把完整工程 workflow 常驻全局 prompt 更能控制 context pollution 与 workflow coupling。

**Unknown:** 该路由模型跨不同 AI harness、长时间 Phase 5 真实任务、更多非工程任务后的 false-positive/false-negative 表现仍需观察。

## Reusable build sequence

```text
observe real friction
→ separate Global / HOW / Project Rules / Project State / Capability / Runtime
→ define authority + canonical homes
→ choose minimum reusable Skills
→ define user/model invocation + domain gates
→ one Primary + bounded model Secondary
→ no-skill by default / progressive disclosure
→ generate derived metadata + drift check
→ lossless migrate existing behavior
→ real-repo E2E
→ real-use observation before Future Work
```

## Connections

- `mental-models/core-models.md#11-agent-skill-composition--context-budget-model`
- `mental-models/git-backed-durable-knowledge-model` conceptually connects canonical state + Git history
- `stanleyrprose/chatgpt-skills`
- `stanleyrprose/mac-browser-plane`
