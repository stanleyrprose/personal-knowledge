# Master Knowledge Map v0.2

## Status Semantics

这张地图同时承担“我想长期建立的知识结构”和“当前已经拥有的 durable knowledge”两种用途，因此必须显式区分：

- **Possessed / Instantiated**：已经有独立/明确知识条目，并具有 evidence、project use、边界或真实复用记录；
- **Intended Coverage**：希望学习、连接或未来验证的领域地图；**不代表已经拥有、掌握或验证。**

未来 AI 不得把 Intended Coverage 当成用户已掌握知识，也不得因为地图里有一个标签就假装 existing node 已经完整存在。

## A. Possessed / Instantiated Knowledge

### AI / Agents / Software Engineering

- [Git-backed Durable Knowledge Model](../mental-models/core-models.md#9-git-backed-durable-knowledge-model)
  - Status: Instantiated
  - Evidence: PKS repository evolution / cross-conversation recovery
- [Single-Consumer Request Body / Parsed-Body Handoff Model](../mental-models/core-models.md#10-single-consumer-request-body--parsed-body-handoff-model)
  - Status: Instantiated
  - Evidence: Node/Express + MCP production bug and fix
- [MCP Content Transport Boundary](../mental-models/mcp-content-transport-boundary.md)
  - Status: Instantiated
  - Evidence: ChatGPT file materialization behavior + Text MCP validation
- [GitHub Text MCP Bridge Project](../projects/github-text-mcp-bridge.md)
  - Status: Project validation record

### PKS / Knowledge Compounding

- Automatic Capture / Candidate / Reject protocol
- Novelty / Duplicate Exclusion Gate
- Epistemic-status discipline
- Git-backed canonical-state + Git-history evolution model
- Event-driven review / friction-driven architecture evolution

这些属于当前已经有 repository evidence 的 PKS 元知识。当前应特别防止它们占据全部 capture diet。

## B. Intended Coverage — Thinking & Epistemology

目标：建立跨领域可迁移的认知与决策操作系统。

> 以下是领域地图，不代表已有 durable node 或 mastery。

- Logic：命题、推理、有效性、谬误
- Causality：相关与因果、反事实、混杂变量
- Probability：不确定性、Bayesian 更新、期望值
- Systems Thinking：边界、反馈、延迟、非线性、涌现
- Decision Theory：目标、约束、机会成本、风险与可逆性
- Game Theory：激励、博弈结构、信息不对称
- Critical Thinking：前提检查、反例、证伪、替代解释
- Epistemology：知识来源、证据等级、置信度、Unknown 管理

## C. Intended Coverage — Telecom & Digital Infrastructure

目标：从技术机制连接到网络经济学、客户价值与投资决策。

> 以下是领域地图，不代表已有 durable node 或 mastery。

### Radio
- LTE / 5G NR
- Spectrum
- TDD / FDD
- Massive MIMO / Beamforming
- Coverage / Capacity / Interference
- Timing & Synchronization

### Transport
- IP / MPLS
- Microwave
- DWDM / Optical Transport
- Backhaul / Fronthaul

### Fixed Access
- FTTH
- FWA
- Access economics

### Core / Cloud
- EPC / 5GC
- Virtualization / Cloud-native telecom

### Network Economics
- CAPEX / OPEX / TCO
- Cost per site / cost per user / cost per bit
- ARPU / utilization / payback / ROI

### Operator Strategy
- Spectrum strategy
- Coverage vs capacity investment
- Vendor strategy
- Monetization
- Regulatory constraints

## D. Intended Coverage — AI, Agents & Software Engineering

目标：理解并能设计 AI Agent 系统，而不是以成为全栈程序员为目的。

> 除 A 节列出的 instantiated knowledge 外，以下大部分仍只是 intended coverage。

### LLM Foundation
- Tokens / Context
- Transformer intuition
- Inference
- Reasoning
- Structured outputs
- Tool use

### Agent Architecture
- Model
- Context
- Memory
- Tools
- State
- Planning
- Feedback loop
- Human-in-the-loop

### Multi-Agent / Orchestration
- Role decomposition
- Parallel vs sequential deliberation
- Synthesizer / moderator
- Consensus and disagreement
- Evaluation

### Integration
- MCP
- REST API
- OAuth / Device Flow
- Webhooks

### Software Engineering
- Git / GitHub
- Branch / Commit / PR
- Testing
- CI/CD
- Observability
- Rollback

### Infrastructure
- Linux
- Docker
- Cloud / VPS
- Networking
- Security basics

## E. Intended Coverage — Business, Strategy & Investment

目标：把技术、市场与资本决策放进同一套经济模型中。

> 以下是领域地图，不代表已有 durable node 或 mastery。

### Opportunity
- Problem / demand
- Timing
- Structural advantage

### Market
- Market size
- Segmentation
- Growth
- Regulation

### Customer
- Job-to-be-done
- Willingness to pay
- Buying process

### Competition
- Differentiation
- Switching cost
- Moat
- Competitive response

### Business Model
- Revenue model
- Gross margin
- CAC / retention
- Unit economics
- Working capital

### Investment
- Cash flow
- ROI / IRR / payback
- Scenario analysis
- Expected value
- Downside / optionality / opportunity cost

## F. Projects & Experiments

真实项目是验证知识和模型的实验场。每个重要项目优先记录：

- Problem / Goal
- Hypothesis
- Relevant knowledge nodes
- Decision model
- Evidence
- Result
- What changed in the model
- New knowledge gaps

Project existence 不自动代表某个模型已经被验证；必须写清验证结果。

## G. Knowledge Compounding

- Knowledge Map：区分 possessed 与 intended
- Mental Model Library：跨问题复用的模型；stub 不等于 validated model
- Learning Queue：下一步最值得补的知识缺口
- Reviews：检查哪些认知发生变化
- Project feedback：用实践校正理论
- Capture Log：观察 Gate 与连续性是否真实工作

## Cross-domain Connection Examples

以下是**连接假设 / intended analysis paths**，不是自动成立的事实：

`5G Spectrum → Capacity → Site Count → CAPEX → Cost/User → ARPU → Payback → Investment Decision`

`AI Agent → Tool Use → API → State → Automation → Labor Leverage → Unit Economics → Business Model`

`Market Demand → Acquisition → Conversion → Retention → Gross Margin → Cash Flow → ROI`

当真实项目产生 evidence 时，再把对应连接升级为 instantiated knowledge。
