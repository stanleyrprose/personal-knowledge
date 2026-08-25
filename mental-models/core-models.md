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
