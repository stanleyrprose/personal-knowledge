# Mental Model Library v0.1

心智模型不是结论，而是可反复调用的分析框架。每个模型都应记录：用途、机制、假设、边界、失效条件和实例。

## 1. System Feedback Model

`Input → Process → State → Output → Feedback`

适用：软件系统、Agent、企业运营、网络系统、学习系统。

重点检查：反馈延迟、正/负反馈、状态不可见、局部优化、外部性。

## 2. Constraint / Bottleneck Model

系统表现通常受最关键约束限制，而不是所有组成部分平均决定。

`System Throughput ≈ Bottleneck Capacity`

适用：网络容量、销售漏斗、软件性能、组织流程。

失效条件：多个动态瓶颈同时存在，或瓶颈随负载变化。

## 3. Expected Value Model

`EV = Σ P(outcome) × Value(outcome)`

必须同时考虑 downside、不可逆性、流动性、时间和机会成本。

适用：投资、商业实验、技术方案选择。

## 4. Unit Economics Model

`Contribution = Revenue per unit - Variable cost per unit`

规模化前必须理解单位经济是否成立；增长不能自动修复负单位经济。

适用：电商、SaaS、FWA、AI 服务。

## 5. Telecom Capacity-Economics Bridge

`Traffic Demand ↔ Spectrum × Spectral Efficiency × Sites × Utilization`

再连接：

`Capacity → Served Users → Revenue → CAPEX/OPEX → ROI`

适用：频谱、5G站点、FWA、Massive MIMO投资判断。

## 6. Agent Architecture Model

`Agent = Model + Context + Memory + Tools + State + Control Loop`

多 Agent 系统再增加：

`Roles + Communication + Coordination + Evaluation + Synthesis`

适用：AWR、OpenClaw、自动化 Agent 系统设计。

## 7. Reversible Decision Model

决策首先区分：

- Reversible：低成本可撤销，可快速实验。
- Irreversible：高成本或难恢复，需要更多证据与审查。

适用：工程变更、商业实验、资本投入。

## 8. Evidence Ladder

优先级通常为：

`直接观测/一手数据 > 可重复实验 > 高质量研究 > 多源一致证据 > 专家意见 > 个案/传闻`

实际权重仍取决于领域、样本与测量质量。

用于避免把“信息很多”误认为“证据很强”。

## 9. Git-backed Durable Knowledge Model

长期 AI 知识系统应把“推理界面”和“持久知识状态”分层：

`Conversation / AI Reasoning → Capture Gate → Markdown Knowledge → Git History → Future AI Recovery`

### Why it matters

聊天上下文、模型 memory 和具体 UI 都可能变化；长期知识若只存在于对话或单一 SaaS 内，跨会话、跨 AI 和长期审计能力都较弱。Git-backed Markdown 把稳定知识状态放到外部、可版本化、可迁移的载体中。

### Mechanism

- Markdown 保存当前知识内容；
- Git commit 保存认知变化的时间序列；
- diff 显示“旧判断 → 新证据 → 新判断”；
- branch / PR 处理结构性或高影响变更；
- `GOAL.md` / maintenance protocol 保存当前目标和接力规则；
- AI 通过工具读取当前状态后继续，而不是依赖完整聊天历史。

### How to use

适用于需要长期 AI 协作、跨会话恢复、知识演化可追踪的个人或项目知识系统。核心原则是：

`Git repository = canonical state`，而 ChatGPT、Obsidian、Web UI、RAG 等只是可替换的 interaction / retrieval layer。

### Preconditions

- 知识主要能表示为文本或可链接资产；
- AI 具有可靠的仓库读取/写入能力；
- 有最小维护协议，避免把聊天流水账全部提交；
- 关键变化通过有语义的 commit 保存。

### Boundaries / Failure Conditions

- GitHub/Git 不适合作为高频 WYSIWYG 笔记体验本身；人类浏览体验可能需要额外 frontend。
- 大量二进制、实时协同数据库或复杂 block-object 数据不天然适合纯 Markdown + Git。
- 若 AI 在新会话中没有全局触发规则，它不会因为仓库存在就自动读取；需要外部 trigger 或显式命令。
- Git history 只有在 commit 具有认知语义时才有价值；高频无意义 auto-commit 会降低可读性。

### Alternative

Notion、Anytype、SiYuan、Obsidian 等可以承担更好的编辑/浏览层，但不必因此替换 canonical storage。是否增加 frontend 应由真实使用摩擦触发，而不是为了架构完整提前复杂化。
