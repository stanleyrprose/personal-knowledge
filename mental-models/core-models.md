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

## 10. Single-Consumer Request Body / Parsed-Body Handoff Model

对 HTTP request body、socket stream、file stream 等一次性/有状态输入，首先要明确“谁拥有消费权”。同一份底层 stream 不应由多个中间件或 handler 在没有显式协调的情况下重复读取。

核心原则：

`Raw stream has one consumption lifecycle; downstream components should receive either ownership of the stream or the already-parsed representation, not assume both.`

### Why it matters

许多集成故障表面看起来像 OAuth、协议格式、Content-Type 或服务端参数错误，真实根因却是上游 middleware 已经消费并解析了输入，而下游 library 仍假设自己能从原始 stream 再读一次。这样会产生空 body、unexpected EOF、invalid JSON、400 Bad Request 等次生症状，并把排障方向错误地引向认证或协议层。

### Mechanism

以 Node/Express 为例：

`IncomingMessage stream → express.json() consumes stream → req.body becomes parsed object → downstream MCP handler tries to read IncomingMessage again → no original bytes remain`

如果下游 handler 支持显式传入已解析 body，则正确交接应变为：

`IncomingMessage stream → express.json() → req.body → downstream handler(parsedBody)`

本质不是“Express 与 MCP 不兼容”，而是两个组件对同一输入生命周期做了冲突假设。

### How to use

当一个请求经过 middleware、framework adapter、SDK handler 或 protocol bridge 时，逐层检查：

1. 这一层接收的是 raw stream、buffer、string，还是 parsed object？
2. 这一层是否会消费/关闭/rewind 输入？
3. 下游 API 期待自己读取 raw request，还是允许传入已经解析的 body？
4. 如果上游已经解析，是否存在显式 handoff 参数把解析结果交给下游？
5. 如果必须保留 raw bytes，是否应在最上游先 buffer/capture，再分别提供 raw 与 parsed representation？

调试时不要只检查“token 是否有效”和“JSON 是否正确”，还应检查 body 在进入目标 handler 前是否仍然存在。

### Preconditions

该模型尤其适用于：

- Node.js / Express / Fastify / Koa 等 HTTP middleware pipeline；
- MCP Streamable HTTP、webhook、REST/GraphQL gateway 等协议适配层；
- signature verification 需要 raw body，但业务层又需要 parsed JSON 的场景；
- proxy / API gateway / serverless adapter 把请求转换后继续交给框架的场景；
- socket、stdin、multipart、上传流、压缩流、解码流等一次性输入；
- SDK 或 framework wrapper 同时存在“自动解析”和“底层 handler 自读请求”的场景。

### Boundaries / Failure Conditions

- 不是所有 body 都严格只能读取一次：如果框架已经完整 buffer 并支持 replay/clone，多个消费者可以读取不同副本；此时约束从“单消费者”变成“单一底层读取 + 显式复制”。
- WHATWG `Request.clone()`、缓存后的 Buffer、某些 serverless runtime 会提供可重复读取语义，不能机械假设 body 一定消失。
- 对超大 body、文件上传或 streaming protocol，简单 buffer 整个请求可能导致高内存占用、延迟和 DoS 风险；这时应优先明确 stream ownership，而不是为了兼容而复制全部数据。
- HMAC/webhook signature verification 常要求 exact raw bytes；如果先 JSON parse 再 stringify，字节序列可能变化，签名会失败。此时必须在解析前保留 raw body。
- 如果 400 来自 schema、protocol version、Content-Type 或 JSON-RPC 本身错误，本模型不能替代协议级验证；它只解释“数据在进入 handler 之前已被消费/变形”的一类问题。

### Counterexamples / Alternatives

可选设计有三种：

- **Single owner**：让最终 protocol handler 独占 raw request，上游不解析 body。适合 handler 自己完整处理协议的场景。
- **Parse then hand off**：由 framework middleware 解析一次，再把 `req.body` 显式传给下游。适合下游 API 支持 parsed body 的场景。
- **Capture raw + parsed dual view**：最上游保存 raw bytes，同时生成 parsed object，分别交给签名校验和业务逻辑。适合 webhook/signature 等必须同时需要两种表示的场景。

选择标准不是“哪种写法更简洁”，而是谁需要 raw bytes、是否需要 streaming、body 大小、是否需要验签，以及下游 library 的输入契约。

### Project validation

在一个 Node/Express + MCP Streamable HTTP 服务中，OAuth 已成功且 token 有效，但 `POST /mcp initialize` 持续返回 400。最终验证根因是 `express.json()` 已提前消费 request body，而 MCP `toNodeHandler` 再次尝试读取原始 body。将 `req.body` 显式传给 `toNodeHandler` 后恢复正常。

该案例说明：当认证层已被证实成功，而协议入口仍出现无法解释的 4xx 时，应把“middleware 是否改变或消费了请求”提升为一级排障假设，而不是继续围绕 OAuth 重复排查。
