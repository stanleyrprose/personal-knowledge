# Mental Model Library v0.2

心智模型不是结论，而是可反复调用的分析框架。每个成熟模型都应记录：用途、机制、假设、边界、失效条件、evidence 和实例。

## Library Status

当前库必须区分：

- **REFERENCE STUB**：建库时用于表达 intended framework 的简要提纲；不是已验证、已掌握或可直接当 Fact 引用的个人 durable knowledge；
- **INSTANTIATED MODEL**：已经有机制、边界、evidence/project validation 或真实使用记录。

**Models 1–8 当前统一降级为 REFERENCE STUB。** 它们可以作为未来分析入口，但未来 AI 不得把这些短提纲当成“用户已经形成的稳定模型”。

Models 9–10 是当前真正 instantiated 的模型。

## 1. System Feedback Model

**Status:** REFERENCE STUB  
**Epistemic Status:** Unknown / not yet instantiated in PKS

`Input → Process → State → Output → Feedback`

适用候选：软件系统、Agent、企业运营、网络系统、学习系统。

待真实使用验证：反馈延迟、正/负反馈、状态不可见、局部优化、外部性如何改变决策。

## 2. Constraint / Bottleneck Model

**Status:** REFERENCE STUB  
**Epistemic Status:** Unknown / not yet instantiated in PKS

候选框架：系统表现可能受关键约束限制，而不是所有组成部分平均决定。

`System Throughput ≈ Bottleneck Capacity`

潜在应用：网络容量、销售漏斗、软件性能、组织流程。

待验证边界：多个动态瓶颈同时存在，或瓶颈随负载变化。

## 3. Expected Value Model

**Status:** REFERENCE STUB  
**Epistemic Status:** Unknown / not yet instantiated in PKS

`EV = Σ P(outcome) × Value(outcome)`

潜在应用：投资、商业实验、技术方案选择。

待实例化：downside、不可逆性、流动性、时间和机会成本如何进入真实决策。

## 4. Unit Economics Model

**Status:** REFERENCE STUB  
**Epistemic Status:** Unknown / not yet instantiated in PKS

`Contribution = Revenue per unit - Variable cost per unit`

潜在应用：电商、SaaS、FWA、AI 服务。

待真实项目实例化，而不是把一句公式当成已拥有的商业模型。

## 5. Telecom Capacity-Economics Bridge

**Status:** REFERENCE STUB  
**Epistemic Status:** Unknown / not yet instantiated in PKS

候选连接：

`Traffic Demand ↔ Spectrum × Spectral Efficiency × Sites × Utilization`

再连接：

`Capacity → Served Users → Revenue → CAPEX/OPEX → ROI`

潜在应用：频谱、5G站点、FWA、Massive MIMO投资判断。

只有真实网络/商业判断形成 evidence、边界和结果后，才升级为 instantiated model。

## 6. Agent Architecture Model

**Status:** REFERENCE STUB  
**Epistemic Status:** Unknown / not yet instantiated in PKS

候选框架：

`Agent = Model + Context + Memory + Tools + State + Control Loop`

多 Agent 系统可能再增加：

`Roles + Communication + Coordination + Evaluation + Synthesis`

待从真实 Agent 项目验证组成、边界和失效条件。

## 7. Reversible Decision Model

**Status:** REFERENCE STUB  
**Epistemic Status:** Unknown / not yet instantiated in PKS

候选区分：

- Reversible：低成本可撤销，可快速实验；
- Irreversible：高成本或难恢复，需要更多证据与审查。

潜在应用：工程变更、商业实验、资本投入。

待真实决策案例实例化。

## 8. Evidence Ladder

**Status:** REFERENCE STUB  
**Epistemic Status:** Unknown / not yet instantiated in PKS

候选启发式：

`直接观测/一手数据 > 可重复实验 > 高质量研究 > 多源一致证据 > 专家意见 > 个案/传闻`

实际权重高度依赖领域、测量、样本和来源质量，因此不能把这条顺序机械当成普适 Fact。

待真实判断案例实例化。

## 9. Git-backed Durable Knowledge Model

**Status:** INSTANTIATED MODEL  
**Epistemic Status:** Judgment + Project-validated architecture pattern  
**Evidence:** PKS v0.1–v0.3 Git history、跨对话 recovery、read/write adapter E2E

长期 AI 知识系统可以把“推理界面”和“持久知识状态”分层：

`Conversation / AI Reasoning → Capture Gate → Markdown Knowledge → Git History → Future AI Recovery`

### Why it matters

聊天上下文、模型 memory 和具体 UI 都可能变化；长期知识若只存在于对话或单一 UI 内，跨会话、跨 AI 和长期审计能力都较弱。Git-backed Markdown 把稳定知识状态放到外部、可版本化、可迁移的载体中。

### Mechanism

- Markdown 保存当前知识内容；
- Git commit 保存认知变化的时间序列；
- diff 显示“旧判断 → 新证据 → 新判断”；
- branch / PR 处理结构性或高影响变更；
- `GOAL.md` / maintenance protocol 保存当前目标和接力规则；
- AI 通过工具读取当前 state 后继续，而不是依赖完整聊天历史。

### How to use

适用于需要长期 AI 协作、跨会话恢复、知识演化可追踪的个人或项目知识系统。

核心原则：

`Git repository = canonical knowledge state`

GitHub、ChatGPT、Obsidian、Web UI、RAG 等是可替换 host / interaction / retrieval layer。

### Preconditions

- 知识主要能表示为文本或可链接资产；
- AI 具有可靠的 repository read/write 能力；
- 有最小维护协议，避免把聊天流水账全部提交；
- 关键变化通过有语义的 commit 保存。

### Boundaries / Failure Conditions

- Git 不适合作为高频 WYSIWYG 笔记体验本身；人类浏览体验可能需要额外 frontend。
- 大量二进制、实时协同数据库或复杂 block-object 数据不天然适合纯 Markdown + Git。
- canonical storage 在 Git 内，但 activation 依赖当前 AI 环境；如果 trigger 不生效，知识存在却不会被主动恢复。
- 若 read adapter 不可用却仍允许 write，会产生 write-without-read 风险；因此 PKS 必须 fail closed。
- Git history 只有在 commit 具有认知语义时才有价值；高频无意义 auto-commit 会降低可读性。

### Alternative

Notion、Anytype、SiYuan、Obsidian 等可以承担更好的编辑/浏览层，但不必因此替换 canonical Git state。是否增加 frontend 应由真实使用摩擦触发。

## 10. Single-Consumer Request Body / Parsed-Body Handoff Model

**Status:** INSTANTIATED MODEL  
**Epistemic Status:** Fact（项目观测） + Inference（跨框架泛化）  
**Evidence:** Node/Express + MCP Streamable HTTP 故障、production fix 与后续验证

对 HTTP request body、socket stream、file stream 等一次性/有状态输入，首先要明确“谁拥有消费权”。同一份底层 stream 不应由多个中间件或 handler 在没有显式协调的情况下重复读取。

核心原则：

`Raw stream has one consumption lifecycle; downstream components should receive either ownership of the stream or the already-parsed representation, not assume both.`

### Why it matters

许多集成故障表面看起来像 OAuth、协议格式、Content-Type 或服务端参数错误，真实根因却可能是上游 middleware 已经消费并解析了输入，而下游 library 仍假设自己能从原始 stream 再读一次。这样会产生空 body、unexpected EOF、invalid JSON、400 Bad Request 等次生症状，并把排障方向错误地引向认证或协议层。

### Mechanism

以已验证的 Node/Express 案例：

`IncomingMessage stream → express.json() consumes stream → req.body becomes parsed object → downstream MCP handler tries to read IncomingMessage again → no original bytes remain`

如果下游 handler 支持显式传入已解析 body，则正确交接为：

`IncomingMessage stream → express.json() → req.body → downstream handler(parsedBody)`

本质不是“Express 与 MCP 不兼容”，而是两个组件对同一输入生命周期做了冲突假设。

### How to use

当请求经过 middleware、framework adapter、SDK handler 或 protocol bridge 时，逐层检查：

1. 这一层接收 raw stream、buffer、string，还是 parsed object？
2. 这一层是否会消费/关闭/rewind 输入？
3. 下游 API 期待自己读取 raw request，还是允许传入已经解析的 body？
4. 如果上游已经解析，是否存在显式 handoff 参数把解析结果交给下游？
5. 如果必须保留 raw bytes，是否应在最上游先 buffer/capture，再分别提供 raw 与 parsed representation？

### Preconditions

该模型尤其适用于：

- Node.js / Express / Fastify / Koa 等 HTTP middleware pipeline；
- MCP Streamable HTTP、webhook、REST/GraphQL gateway 等协议适配层；
- signature verification 需要 raw body，但业务层又需要 parsed JSON 的场景；
- proxy / API gateway / serverless adapter 把请求转换后继续交给框架的场景；
- socket、stdin、multipart、上传流、压缩流、解码流等一次性输入；
- SDK 或 framework wrapper 同时存在“自动解析”和“底层 handler 自读请求”的场景。

### Boundaries / Failure Conditions

- 不是所有 body 都严格只能读取一次：如果框架已经完整 buffer 并支持 replay/clone，多个消费者可以读取不同副本；约束变成“单一底层读取 + 显式复制”。
- WHATWG `Request.clone()`、缓存后的 Buffer、某些 serverless runtime 会提供可重复读取语义，不能机械假设 body 一定消失。
- 对超大 body、文件上传或 streaming protocol，简单 buffer 整个请求可能导致高内存占用、延迟和 DoS 风险；这时应优先明确 stream ownership。
- HMAC/webhook signature verification 常要求 exact raw bytes；如果先 JSON parse 再 stringify，字节序列可能变化，签名会失败。
- 如果 400 来自 schema、protocol version、Content-Type 或 JSON-RPC 本身错误，本模型不能替代协议级验证。

### Counterexamples / Alternatives

- **Single owner**：最终 protocol handler 独占 raw request；
- **Parse then hand off**：framework 解析一次，再把 parsed body 显式交给下游；
- **Capture raw + parsed dual view**：保留 raw bytes，同时生成 parsed object，分别供验签与业务逻辑使用。

选择标准取决于谁需要 raw bytes、是否需要 streaming、body 大小、是否需要验签，以及下游 library 的输入契约。

### Project validation

在一个 Node/Express + MCP Streamable HTTP 服务中，OAuth 已成功且 token 有效，但 `POST /mcp initialize` 持续返回 400。最终验证根因是 `express.json()` 已提前消费 request body，而 MCP `toNodeHandler` 再次尝试读取原始 body。将 `req.body` 显式传给 `toNodeHandler` 后恢复正常。

因此，认证层已证实成功而协议入口仍出现无法解释的 4xx 时，应把“middleware 是否改变或消费请求”提升为一级排障假设。
