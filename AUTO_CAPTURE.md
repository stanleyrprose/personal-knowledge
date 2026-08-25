# Automatic Knowledge Capture Policy v0.3

## Purpose

本协议定义 ChatGPT / AI 在真实对话中何时应主动把内容沉淀到 `stanleyrprose/personal-knowledge`，以及何时不应写入。

目标不是保存聊天，而是持续维护用户的长期知识模型。

## Trigger

当对话中出现以下任一类高价值内容时，AI 应主动进行 capture 判断：

- 可迁移的机制或因果结构
- 心智模型或决策框架
- 经纠错后的稳定结论
- 能限制模型适用范围的重要边界条件
- 能推翻或削弱原判断的重要反例
- 真实项目对既有知识的验证、修正或证伪
- 新暴露出的长期学习缺口

在新对话中，如触发 capture 判断，应先读取：

1. `GOAL.md`
2. `MAINTENANCE.md`
3. 必要时再读 `knowledge-map/master-map.md`、`mental-models/core-models.md`、`learning-queue.md` 与最近 commits / PRs

## Capture Gate

默认满足以下条件中的至少 2 项，才进入长期知识库：

1. 可跨多个场景复用
2. 会改变或提升真实决策质量
3. 解释机制，而不只是给出定义或事实
4. 修正、强化或推翻了旧认识
5. 明确了重要前提、边界或失效条件
6. 提供了有价值的反例或竞争解释
7. 被真实项目或实验验证
8. 暴露出值得长期跟踪的知识缺口

不要求机械计分。若某一项本身价值极高（例如真实项目直接证伪核心模型），可直接 capture。

## Three Outcomes

### 1. AUTO-CAPTURE

适用于：

- 稳定机制
- 成熟模型
- 清晰边界条件
- 已验证的项目经验
- 经纠错后明显更可靠的结论

处理：

`定位已有节点 → 优先更新而非新建 → 写入证据/边界/连接 → atomic commit`

默认不需要因普通、可逆的知识维护再次询问用户。

### 2. CANDIDATE

适用于：

- 商业或市场假设
- 有争议的理论
- 证据不足的推论
- 尚未验证但值得持续追踪的判断

处理优先级：

1. 若已有对应节点，明确标注 `Inference` / `Judgment` / `Unknown` 与 confidence/证据缺口；
2. 若尚不值得成为正式节点，进入 `learning-queue.md`；
3. 不把候选假设写成 Fact。

### 3. REJECT

默认不进入长期知识库：

- 普通聊天记录或逐字转录
- 一次性查询
- 很快失效、且无长期机制价值的新闻本身
- 临时操作步骤
- 单纯定义、没有新增理解的内容
- 无证据且无后续价值的即时观点
- 某项目的日常 task log

## Update vs New File

优先顺序：

1. 更新现有知识节点
2. 更新已有 Mental Model
3. 更新 Project Learning Record
4. 更新 Learning Queue
5. 只有当已有结构无法自然容纳且内容已形成稳定主题时，才新增文件

避免为每次讨论制造孤立 Markdown 文件。

## Knowledge Quality

Capture 后的内容应尽可能保留：

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

根据内容实际需要填写，不为模板完整而制造文字。

## Epistemic Status

必要时明确区分：

- `Fact`
- `Estimate`
- `Inference`
- `Judgment`
- `Unknown`

对于依赖市场、法规、产品版本、价格、软件状态等变化的事实，应记录其时效性或后续 evidence refresh 需求。

## Commit Policy

知识维护使用可读、可搜索的 commit 前缀：

- `knowledge:` 新增或实质更新长期知识
- `model:` 新增或修正 Mental Model / 决策框架
- `hypothesis:` 记录或调整待验证假设
- `project:` 沉淀真实项目验证经验
- `queue:` 调整长期学习缺口
- `review:` 定期复盘、证据刷新或认知重构
- `chore:` 结构、模板、维护协议等非知识内容

一个 commit 应尽量对应一个认知变化或一个紧密相关的主题。

## Conflict and Safety

- 不覆盖用户明确要求保留的重要旧判断；应记录“旧判断 → 新证据 → 修正后判断”。
- 如知识内容存在明显争议，保留 competing explanations，而不是强行统一。
- 涉及敏感私密信息、凭证、账户密钥等内容，不进入知识库。
- 不因为“可以自动写”而扩大仓库 scope。

## Review Loop

Review 不按固定高频运行。当前阶段采用事件驱动：

- 累积若干真实 capture 后
- 重大项目结束后
- 核心模型被反例挑战后
- 某领域准备进入重要实际决策前

当知识规模明显扩大后，再考虑月度 review、staleness、confidence 与 GitHub Actions 自动检查。

## Success Criterion

自动捕获机制成功的标准不是 commit 数量，而是：

- 后续对话能复用已有模型
- 新证据能修正旧知识而非重复堆积
- 项目经验能反哺通用认知
- 长期知识缺口越来越清晰
- Git history 能解释知识如何演化
