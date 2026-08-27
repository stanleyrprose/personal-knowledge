# Personal Knowledge System（PKS）v0.3 Stable
## 搭建方法、架构复盘与下一阶段同行评审材料

**Repository:** `stanleyrprose/personal-knowledge`  
**当前状态:** PKS v0.3 Stable  
**评审目的:** 让独立 AI / 同行评审者理解 PKS 是如何被搭建、验证和迭代的，并判断下一步是否应该继续优化，以及如果优化，优先优化什么。  
**文档日期:** 2026-08-27

---

# 0. 给同行评审 AI 的说明

请不要把这份文档当作“已经证明正确的架构说明书”。

本 PKS 的核心目标不是做一个功能最多的第二大脑，而是建立一个：

> **可跨对话、跨 AI、跨项目持续演化，并以 Git history 保留认知变化的长期知识系统。**

请重点挑战：

1. 当前系统是否真的解决了长期知识连续性，而不仅仅是“AI 自动写 Markdown”；
2. `AUTO-CAPTURE / CANDIDATE / REJECT` 三态机制是否足够可靠；
3. GitHub 作为唯一 Source of Truth 是否合理；
4. 当前 read/write plane separation 是否过度工程化或恰到好处；
5. 什么时候应该引入搜索、RAG、GraphRAG、Knowledge Graph、Obsidian/GitSync 或 automated review；
6. 当前架构最可能先出现的 failure mode 是什么；
7. 下一步应继续优化，还是应停止架构开发、转入真实使用观测。

请把建议分为：

- **Must Fix**
- **Should Improve**
- **Defer**
- **Do Not Add Yet**

并明确说明判断依据。

---

# 1. PKS 要解决的核心问题

普通 ChatGPT 对话有几个长期问题：

- 对话天然是 session-oriented；
- 重要认知分散在大量聊天里；
- 新对话 / 新 AI 不一定知道旧结论；
- 模型 memory 不等于可审计的知识库；
- 一次性总结很容易丢掉“为什么观点发生变化”；
- 如果自动保存一切，又会形成另一个低质量信息垃圾场。

因此 PKS 从一开始就没有把目标定义为：

> “保存更多内容”

而是定义为：

> **维护长期知识模型。**

核心要求：

```text
Conversation ≠ Source of Truth
Model memory ≠ Source of Truth
UI / PKM frontend ≠ Source of Truth

Git repository = Source of Truth
```

聊天、网页、项目实践、AI 分析只是输入。

长期稳定知识必须最终进入 Git repository，并留下 Git history。

---

# 2. 总体设计原则

## 2.1 Git-backed Durable Knowledge

GitHub repository 是 canonical storage。

原因不是因为 GitHub 是最好的笔记软件，而是因为它提供：

- Markdown 文本；
- version history；
- commit；
- diff；
- branch；
- PR；
- 可回滚；
- 跨 AI 读取；
- 自动化能力；
- 与真实项目工程工作流一致。

因此：

```text
Human / AI conversation
        ↓
Knowledge evaluation
        ↓
Markdown model
        ↓
Git commit
        ↓
Durable knowledge state
```

而不是：

```text
Conversation
   ↓
Copy everything
   ↓
Huge note archive
```

## 2.2 保存“认知变化”，不是保存“聊天记录”

长期知识优先包括：

- 可迁移机制；
- Mental Model；
- 决策框架；
- 经验证结论；
- 经纠错后的稳定结论；
- 前提与边界；
- 重要反例；
- 项目实践对模型的验证或证伪；
- 长期学习缺口。

默认不保存：

- 普通聊天记录；
- 新闻本身；
- 一次性查询；
- 临时操作步骤；
- 产品短期额度；
- 无长期复用价值的事实；
- 项目日常 task log。

## 2.3 真实项目是知识验证层

项目不是知识库之外的附件。

设计关系：

```text
Mental Model
     ↓
Project / Decision
     ↓
Real-world evidence
     ↓
Model validated / corrected / rejected
     ↓
PKS update
```

因此 PKS 希望最终形成：

> 理论 → 使用 → 结果 → 修正 → 更强模型

而不是静态 Wiki。

---

# 3. 仓库结构

当前核心结构：

```text
personal-knowledge/
│
├── GOAL.md
├── MAINTENANCE.md
├── AUTO_CAPTURE.md
├── learning-queue.md
│
├── knowledge-map/
│   └── master-map.md
│
├── mental-models/
│   ├── core-models.md
│   └── mcp-content-transport-boundary.md
│
├── projects/
│   └── github-text-mcp-bridge.md
│
└── reviews/
    ├── 2026-08-25-auto-capture-validation-01.md
    ├── 2026-08-27-cross-conversation-validation-v03-stable.md
    └── ...
```

各层职责：

| 层 | 作用 |
|---|---|
| `GOAL.md` | 当前 milestone、checkpoint、Next Priorities、跨 AI handoff |
| `MAINTENANCE.md` | 系统维护协议 |
| `AUTO_CAPTURE.md` | 自动知识捕获 Gate |
| `knowledge-map/` | 知识领域与连接地图 |
| `mental-models/` | 可跨场景复用的模型 |
| `projects/` | 实际项目验证记录 |
| `learning-queue.md` | 长期知识缺口 |
| `reviews/` | 对知识模型和系统机制做复盘 |

---

# 4. 版本演进

## v0.1 — 建立最小知识骨架

关键 commit：

`2389b1dc0fd0c29117e72cfc9bd66d2c7e61733f`

Commit：

`chore: initialize personal knowledge system v0.1`

建立：

- Master Knowledge Map；
- Mental Model Library；
- Learning Queue；
- Maintenance protocol。

### v0.1 解决的问题

先回答：

> “知识放在哪里？”

而不是立即做自动化。

## v0.2 — 建立跨会话 Continuity

关键 commit：

`76d5437a08f65b0d430e47fbe6af67485729bac7`

Commit：

`PKS v0.2: add continuity and capture workflow`

加入：

- `/goal` checkpoint；
- project template；
- review template；
- knowledge capture template；
- AI handoff；
- 跨会话恢复流程。

### v0.2 解决的问题

从：

```text
一堆结构化 Markdown
```

提升到：

```text
另一个 AI 可以知道：
现在做到哪里？
下一步是什么？
哪些规则不能重新设计？
```

## v0.3 — Automatic Knowledge Capture

v0.3 的目标是：

> 用户不需要每次说“把这个保存到知识库”。

AI 应主动判断内容是否值得长期保存。

核心文件：

`AUTO_CAPTURE.md`

关键 commit：

`1598147e38aa532defd8fc8da05cb5ba9f0cf61b`

---

# 5. Capture Gate

PKS 定义 8 个判断维度。

默认满足其中至少 2 项，才进入长期知识库：

1. 可跨多个场景复用；
2. 会改变或提升真实决策质量；
3. 解释机制，而不只是定义 / 事实；
4. 修正、强化或推翻旧认识；
5. 明确重要前提、边界或失效条件；
6. 提供重要反例或竞争解释；
7. 被真实项目或实验验证；
8. 暴露值得长期跟踪的知识缺口。

不是机械打分。

如果单一证据极强，例如真实项目直接证伪核心模型，可以直接 capture。

---

# 6. 三态知识判断

## 6.1 AUTO-CAPTURE

适用于：

- 稳定机制；
- 成熟 Mental Model；
- 清晰边界；
- 已验证项目经验；
- 经纠错后的稳定结论。

处理：

```text
定位已有节点
    ↓
优先更新已有节点
    ↓
补 evidence / boundary / connection
    ↓
atomic commit
```

## 6.2 CANDIDATE

适用于：

- 商业假设；
- 市场假设；
- 有争议理论；
- 证据不足推论；
- 尚未验证但值得追踪的判断。

必须保留 epistemic status：

- Inference
- Judgment
- Unknown

必要时进入 `learning-queue.md`，而不是伪装成 Fact。

## 6.3 REJECT

拒绝写入本身也是成功结果。

典型 REJECT：

- 临时操作步骤；
- 短期新闻；
- 一次性查询；
- 普通聊天；
- 很快 stale 的产品事实；
- 没有机制价值的定义。

目标是防止：

> Auto Capture → Auto Garbage Collection Problem

即“因为能自动保存，所以什么都保存”。

---

# 7. Epistemic Status

需要时明确区分：

### Fact
有可靠证据支持。

### Estimate
来自模型或有限数据估算。

### Inference
由事实推导，但不是直接观察。

### Judgment
包含目标、权重、偏好或价值取舍。

### Unknown
当前证据不足。

这个机制的目的，是防止：

```text
AI inference
   ↓
Markdown
   ↓
几个月后被新 AI 当成 Fact
```

这是长期 AI 知识系统一个非常重要的风险。

---

# 8. Knowledge Quality Standard

长期条目优先回答：

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

不是要求每篇机械填模板，而是避免只留下：

> “结论：X 是对的。”

长期真正需要保存的是：

> 为什么对、什么时候对、什么时候会错。

---

# 9. Update Existing Node First

PKS 的一个重要 anti-pattern 是：

```text
每次聊天
   ↓
新建 Markdown
   ↓
几百个孤立文件
```

因此更新顺序是：

1. 已有 Knowledge Node；
2. 已有 Mental Model；
3. Project Learning Record；
4. Learning Queue；
5. 只有无法自然容纳时才新增文件。

这可以减少：

- duplicate nodes；
- semantic fragmentation；
- retrieval ambiguity。

---

# 10. Git History = Knowledge Evolution History

PKS 不只使用 Git 做存储。

Git commit 本身是认知变化日志。

Commit prefix：

```text
knowledge:
model:
hypothesis:
project:
queue:
review:
chore:
```

例如：

`model: add single-consumer request body handoff model`

表示发生的是 Mental Model 更新，而不是普通文件修改。

小型知识更新：

```text
atomic commit
```

结构变化：

```text
branch → PR → review → merge
```

原则：

> 一个 commit 尽量表达一个清晰的认知变化。

---

# 11. Event-driven Review，而不是高频定时整理

当前没有设置“每周自动整理整个知识库”。

因为这种机制非常容易产生：

- 无意义改写；
- 模型 churn；
- false changes；
- AI 自己“整理自己”。

当前采用 **Event-driven Review**。

触发条件：

- 已积累若干真实 capture；
- 重大项目结束；
- 核心模型被现实反例挑战；
- 某领域即将进入重大决策。

Review 要回答：

- 哪些模型被证伪？
- 哪些 Knowledge Gap 已补齐？
- 哪些内容 stale？
- 哪些 Mental Model 可跨域连接？
- 哪些 Learning Queue 应删除？
- 哪些项目经验应升级成通用模型？

---

# 12. Master Knowledge Map

当前 Knowledge Map 主要分为：

## Thinking & Epistemology

- Logic
- Causality
- Probability
- Systems Thinking
- Decision Theory
- Game Theory
- Critical Thinking
- Epistemology

## Telecom & Digital Infrastructure

从：

```text
Spectrum
↓
Radio Capacity
↓
Site Count
↓
CAPEX
↓
Cost/User
↓
ARPU
↓
Payback
↓
Investment Decision
```

建立技术 → 商业连接。

## AI / Agents / Software Engineering

关注：

- Agent architecture
- Memory
- Tool use
- MCP
- orchestration
- Git / CI/CD
- infrastructure
- observability / recovery

## Business / Strategy / Investment

关注：

- Demand
- Competition
- Business model
- Unit economics
- Cash flow
- ROI
- Scenario analysis

## Projects & Experiments

作为模型验证层。

---

# 13. 为什么没有立刻使用 Obsidian / Notion / SiYuan

早期评估过人类 PKM frontend。

最终判断：

**CANDIDATE / DEFER**

原因：Git repository 已经能解决：

- AI durable state；
- history；
- audit；
- version；
- cross-conversation continuity。

目前还没有真实摩擦证明需要：

- backlink；
- graph；
- mobile editor；
- sync engine；
- conflict resolution。

因此采用原则：

> Frontend should be added because of proven friction, not architectural aesthetics.

只有出现：

- 高频手机端编辑；
- GitHub UI 成为明显瓶颈；
- backlink / graph 有真实收益；
- human + AI 双向编辑需要同步；

才评估 Obsidian + GitSync 等方案。

---

# 14. 最关键的基础设施问题：GitHub 文件 Materialization

在实际使用 Github MCP 时发现：

即使 GitHub tool invocation 已被授权，读取 repository file 后仍可能出现：

`Allow file materialization?`

这暴露一个重要模型：

```text
Action Authorization
       ≠
Result Handling Authorization
```

一个 Agent 工具调用至少存在：

```text
Tool Permission
    ↓
Tool Execution
    ↓
Result Representation
    ↓
Client Handling Policy
    ↓
Potential Human Gate
```

因此，仅把工具设置为 Never Ask，并不意味着完全 autonomous。

---

# 15. GitHub Text MCP Bridge

为解决这个问题，创建：

`stanleyrprose/github-text-mcp-bridge`

目标非常有限：只做 read-only：

- `read_text_file`
- `list_directory`

并返回：

```json
{
  "content": [
    {
      "type": "text",
      "text": "..."
    }
  ]
}
```

不返回：

- EmbeddedResource；
- ResourceLink；
- attachment；
- connector file reference。

---

# 16. Read / Write Plane Separation

最终 PKS 使用：

```text
                 ┌─────────────────────┐
                 │       ChatGPT       │
                 └──────────┬──────────┘
                            │
              ┌─────────────┴─────────────┐
              │                           │
        GitHub Text MCP              Github MCP
              │                           │
         READ PLANE                   WRITE PLANE
              │                           │
     read_text_file                 create/update
     list_directory                 commit
              │                     branch
              │                     PR / merge
              │                     Actions
              └─────────────┬─────────────┘
                            │
                       GitHub Repo
```

原因：

> 不为了修一个 read-path friction 重写完整 GitHub integration。

这是整个工程里的重要 Scope-Control 决策。

---

# 17. GitHub Text MCP 实施中的失败与教训

## 17.1 不要只看 OAuth discovery 成功

完整 E2E 必须逐层验证：

```text
OAuth Discovery
↓
Authorization
↓
Callback
↓
Token Exchange
↓
MCP Initialize
↓
tools/list
↓
Tool Call
↓
Result Representation
↓
ChatGPT UI Behavior
```

某一层成功，不代表系统成功。

## 17.2 自建 OAuth Authorization Server 增加了过多兼容面

最初设计：

```text
ChatGPT
↓
Bridge OAuth
↓
GitHub OAuth
↓
Bridge callback
↓
ChatGPT
↓
Bridge /token
```

遇到：

- DCR compatibility；
- CIMD；
- callback handling；
- private_key_jwt；
- metadata capability mismatch。

最终简化为：

```text
ChatGPT
↓
GitHub OAuth
↓
GitHub access token
↓
GitHub Text MCP Bridge
↓
GitHub API
```

经验：

> 如果上游 OAuth provider 已经能满足 client，Bridge 不应无必要再做 Authorization Server。

## 17.3 Advertised Capability 必须与实现一致

曾经 Bridge 宣告 CIMD support。

但 ChatGPT CIMD metadata 使用：

- `private_key_jwt`
- JWKS
- RS256

Bridge 实际未支持完整链路。

这导致：

> Discovery 看起来支持，但实际不可用。

长期原则：

> Capability advertisement is part of the protocol contract.

## 17.4 不要过早归因 Cloudflare / WAF

DCR 403 曾经怀疑是 Cloudflare。

后来用等价请求测试发现不是。

经验：

> Network infrastructure should become a root-cause Fact only after equivalent request reproduction.

---

# 18. Express + MCP 的关键工程 Bug

OAuth 已经完全成功，ChatGPT token 有效，但：

`POST /mcp initialize → 400`

真正根因：

```text
IncomingMessage
↓
express.json()
↓
raw body 被消费
↓
req.body 已存在
↓
MCP toNodeHandler 再尝试读取 raw stream
↓
empty body
↓
400
```

最终：

```ts
nodeMcpHandler(req, res, req.body)
```

修复。

这个经验后来被 PKS 自动抽象为：

**Single-Consumer Request Body / Parsed-Body Handoff Model**

并跨新对话自动 Capture。

这也是 PKS v0.3 的关键真实验证案例。

---

# 19. 由该 Bug 抽象出的长期模型

原则：

> Raw stream has one consumption lifecycle.

下游应该得到：

- raw stream ownership；
- 或 already-parsed representation；

而不能默认两者同时存在。

适用于：

- Express；
- Fastify；
- Koa；
- MCP；
- webhook；
- GraphQL gateway；
- signature verification；
- serverless adapters；
- socket / stdin；
- uploads；
- stream processing。

边界：

如果 runtime 已 buffer 并可 clone，则可以重复读取副本。

但：

```text
one underlying stream
→ should not be independently consumed twice
```

---

# 20. 其他实施经验

## Dependency Lock

加入 `package-lock.json`，生产和 CI 使用 `npm ci`。

原则：

> Dependency determinism is deployment correctness.

## Production Port Collision

8787 已被未知 Python 服务占用。

没有 kill 旧进程，改为：

`127.0.0.1:18787`

原则：

> 遇到未知生产资源时，优先隔离新服务，不破坏旧系统。

## Secret Handling

GitHub Client Secret：

- 不进入 Git；
- 不在对话展示；
- runtime env injection；
- permission 600；
- AI 只确认存在性和权限状态，不读取值。

原则：

> Secret is runtime concern, not AI knowledge concern.

---

# 21. v0.3 Capture Gate Validation

第一次真实 Gate 验证使用三个主题。

## Topic A — Git-backed durable knowledge model

结果：**AUTO-CAPTURE**

因为：

- 可跨场景复用；
- 改变长期 AI 协作；
- 有机制；
- 有架构边界。

## Topic B — 是否引入 Obsidian + GitSync

结果：**CANDIDATE / DEFER**

因为：有潜在收益，但没有真实 friction。

## Topic C — GitHub Actions 免费额度 / 安装步骤

结果：**REJECT**

因为：

- stale；
- 操作性；
- 低长期杠杆。

这次测试的重要结论：

> REJECT 也是成功输出。

系统不能为了证明 Auto Capture 工作，而让每个主题都产生 commit。

---

# 22. v0.3 跨会话盲测

2026-08-27 进行了真正的新对话测试。

用户只输入一个工程问题：

> Express + MCP request body 被重复消费的 bug，请抽象成通用工程原则。

没有说：

- 保存到知识库；
- 继续 PKS；
- 读取 GitHub；
- 使用 Capture Gate。

新 AI 主动完成：

```text
识别长期价值
↓
读取 PKS guidance
↓
读取 Maintenance
↓
读取 Auto Capture
↓
检查 Knowledge Map
↓
检查 Core Models
↓
判断 AUTO-CAPTURE
↓
定位已有 core-models.md
↓
写入 Mental Model
↓
atomic commit
```

最终 commit：

`e8d4c305f7e6046a3d2c49282adf16bc99036d32`

Commit：

`model: add single-consumer request body handoff model`

这次验证是 PKS v0.3 Stable 的主要证据。

---

# 23. 当前 Operating Loop

```text
真实问题
↓
先解决问题
↓
判断是否具有长期价值
↓
如果没有 → 不保存
↓
如果有 → GitHub Text MCP 读取当前知识状态
↓
读取 GOAL / Maintenance / Auto Capture
↓
找到已有 Knowledge Node
↓
Capture / Candidate / Reject
↓
检查 evidence / boundary / alternative
↓
Github MCP 写入
↓
atomic commit
↓
未来对话复用或修正
```

---

# 24. v0.3 已验证能力

目前已经有实际证据支持：

### Fact

- GitHub 是 canonical Source of Truth；
- 新 AI 可以通过 GitHub 恢复 PKS 状态；
- Capture Gate 可以输出不同结果；
- AUTO-CAPTURE 可以自动更新知识；
- CANDIDATE 可以保持为条件触发；
- REJECT 不产生无意义写入；
- GitHub Text MCP 可以读取 private repo；
- `read_text_file` 返回 inline TextContent；
- read plane 不依赖 file materialization；
- Github MCP 可以完成 atomic write；
- 全新对话可以自动恢复 protocol；
- 新对话能够优先更新已有 Mental Model；
- Git commit 可以记录认知演化。

---

# 25. 尚未被充分验证的能力

## 25.1 False Positive Rate

系统是否会随着使用增多逐渐“什么都觉得值得 Capture”？当前样本量还不够。

## 25.2 False Negative Rate

是否会漏掉重要决策经验、经纠错后的关键结论、隐含但重要的 Mental Model？目前没有量化。

## 25.3 Duplicate Knowledge Nodes

知识规模变大后，AI 是否还能正确发现已有节点？当前知识库规模很小，所以容易。

## 25.4 Staleness

市场、法规、产品版本、价格等事实会 stale。目前有规则，但没有自动 refresh。

## 25.5 Search Scalability

现在主要依靠：

- directory listing；
- 文件读取；
- AI 自己定位。

当文件达到 100、1,000、10,000 时，当前策略是否还能工作，未知。

## 25.6 OAuth / MCP Long-term Stability

GitHub OAuth 的 refresh、session expiration、ChatGPT connector state、GitHub App lifecycle 尚未经过数月长期验证。

---

# 26. 当前明确没有实施的功能

目前没有：

- Vector DB；
- Embeddings；
- RAG；
- GraphRAG；
- Knowledge Graph database；
- automatic staleness scan；
- monthly review bot；
- Obsidian sync；
- Notion sync；
- semantic search service；
- recommendation engine；
- auto mastery scoring；
- autonomous web crawling。

这是刻意的 Scope Control，不是功能缺失。

---

# 27. 为什么现在没有立即继续升级

当前系统遵循：

> Friction-driven architecture evolution

而不是：

> Capability-driven architecture evolution

即：

```text
发现真实问题
↓
确认问题持续存在
↓
分析最小解决方式
↓
升级系统
```

而不是：

```text
某功能看起来先进
↓
加入 PKS
```

v0.3 stable 后，官方 Next Priority 是：

> 真实运行，不再制造测试主题。

---

# 28. 下一阶段可选方向

以下只是 Candidate，不代表应该实施。

## Option A — 保持 v0.3 不动

继续使用 1–3 个月。

收集：

- captures 数量；
- false positive；
- false negative；
- duplicates；
- stale facts；
- retrieval friction；
- 人工维护时间。

优点：获得真实需求、防止过度设计。

风险：一些结构性问题可能较晚暴露。

## Option B — 增加 Semantic Search

当知识文件增加后，可能需要：

```text
query
↓
semantic retrieval
↓
candidate nodes
↓
targeted read
```

但在当前小规模仓库可能是 premature optimization。

## Option C — Staleness / Confidence Metadata

可以为 Fact 增加：

```text
confidence
last_verified
source
refresh_after
```

然后使用 GitHub Actions 扫描 stale knowledge。

适合：

- Telecom policy；
- regulations；
- software product behavior；
- market data。

风险：过早把知识库变成数据库 schema。

## Option D — Review Automation

例如每月找：

- 30 天以上 CANDIDATE；
- stale facts；
- duplicate headings；
- unreferenced files；
- learning queue aging。

风险：AI review 可能制造无意义 churn。

## Option E — Obsidian / GitSync

只有在人类浏览和编辑出现明显摩擦后。

Git 仍保持 Source of Truth。

Obsidian 只能作为 **View / Edit Layer**，不能成为第二 canonical state。

## Option F — RAG / GraphRAG

当知识量超过 LLM 可有效浏览的规模后考虑。

真正 trigger 应该是：

> “AI 已经经常找不到已有知识。”

而不是：

> “GraphRAG 很先进。”

---

# 29. 对同行评审 AI 的核心问题

## Architecture

1. GitHub 作为唯一 Source of Truth 是否合理？
2. 是否应该引入 database？
3. read/write plane separation 是否值得保留？
4. GitHub Text MCP 是否应该扩展 range/search tools？

## Knowledge Model

5. Knowledge Map + Mental Models + Projects + Queue + Reviews 是否足够？
6. 是否缺少 Evidence layer？
7. 是否应该把 Fact 和 Model 分目录？
8. 是否应该引入 typed frontmatter？

## Capture

9. `2-of-8 Capture Gate` 是否太宽松？
10. AUTO-CAPTURE 是否应该增加 confidence threshold？
11. 是否应该加入“已有知识重复度”作为 Gate？
12. 是否需要用户 correction loop？

## Retrieval

13. 当前 directory/read 模型能扩展到多大？
14. 什么时候应该引入 semantic search？
15. 是否应该先做 grep/search MCP，而不是 RAG？

## Review

16. Event-driven Review 是否合理？
17. 是否应该设 minimum cadence？
18. stale Fact 应如何管理？
19. 如何防止 AI 自动 review 导致 knowledge churn？

## Human Interface

20. GitHub UI 是否足够？
21. 什么时候 Obsidian 会真正产生 ROI？

## Safety

22. 如何防止 AI 把 private / sensitive 信息写入 PKS？
23. GitHub write plane 是否应该增加 branch/PR gate？
24. 哪些 knowledge mutation 属于 high-risk change？

## Long-term Evolution

25. PKS 下一版本真正值得称为 v0.4 的能力是什么？
26. 是否应该先运行 v0.3 一段时间而完全不开发新架构？
27. 什么可观测指标最能证明 PKS 有价值？

---

# 30. 建议同行评审输出格式

建议每个 AI 使用下面格式：

```markdown
# Overall Verdict

Keep v0.3 / Minor Improve / Build v0.4 / Redesign

# Strongest Design Decisions

...

# Weakest Assumptions

...

# Failure Modes Most Likely to Appear First

1.
2.
3.

# Must Fix

...

# Should Improve

...

# Defer

...

# Do Not Add Yet

...

# Recommended v0.4 Scope

...

# Evidence / Metrics Needed Before v0.4

...

# 90-Day Experiment Plan

...
```

---

# 31. 当前 provisional judgment

这是当前架构基于已有证据形成的暂定判断，不应作为同行评审的先验结论。

### Judgment

**暂时不应该立即做大规模 v0.4。**

原因：v0.3 最重要的技术风险——跨对话恢复、自动 Capture、GitHub read、GitHub write、materialization、atomic commit——已经刚刚打通，但长期真实数据仍不足。

此时最有信息价值的动作不是继续开发，而是：

```text
真实使用
↓
收集 failure evidence
↓
发现 recurring friction
↓
再决定 v0.4
```

最值得优先观察的四个指标：

1. **Capture precision** — 自动保存的内容后来是否真的有用？
2. **Capture recall** — 有没有明显漏掉长期重要内容？
3. **Retrieval success** — 新对话是否能找到已有知识，而不是重复生成？
4. **Knowledge maintenance cost** — 知识增长后维护是否开始超过收益？

只有这些指标出现真实问题，v0.4 才应该针对问题设计。

---

# 32. 关键 Git 历史

主要演进 commits：

```text
2389b1dc
chore: initialize personal knowledge system v0.1

76d5437a
PKS v0.2: add continuity and capture workflow

1598147e
PKS v0.3: automatic knowledge capture

4147220a
model: add git-backed durable knowledge model

c28bbca2
queue: defer PKM frontend until real friction appears

87aedd03
review: validate v0.3 capture gate on three real topics

85f74227
model: capture MCP content transport approval boundary

1dd162a1
project: record GitHub Text MCP Bridge validation

adac4dd0
project: validate GitHub Text MCP read plane

b39b3292
chore: adopt Text MCP as PKS read plane

a76a3934
model: consolidate GitHub Text MCP v3 lessons

e8d4c305
model: add single-consumer request body handoff model

826fd302
review: validate cross-conversation capture and release v0.3 stable
```

Git history 本身构成 PKS 如何从静态知识库演化为自动维护知识系统的证据链。

---

# 33. 当前系统一句话总结

PKS v0.3 不是：

> 一个 AI 自动记笔记工具。

更准确地说，它是：

> **一个以 GitHub 为 durable Source of Truth、以 Capture Gate 控制知识质量、以 Mental Models 为长期复用单位、以真实项目为验证层、并允许 AI 在跨会话中主动读取、修正和提交知识变化的 Git-backed Personal Knowledge System。**

---

# 34. 最终同行评审目标

同行评审最重要的不是回答：

> “还能加什么功能？”

而是回答：

> **“当前 PKS 在什么真实失败证据出现之前，不应该再增加哪些复杂度？”**

如果能够清楚回答这个问题，下一阶段路线就会比单纯功能规划更可靠。
