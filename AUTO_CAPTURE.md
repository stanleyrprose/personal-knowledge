# Automatic Knowledge Capture Policy v0.3

## Purpose

本协议定义 ChatGPT / AI 在真实对话中何时应主动把内容沉淀到 `stanleyrprose/personal-knowledge`，以及何时不应写入。

目标不是保存聊天，而是持续维护用户的长期知识模型。

当前状态：**Capture / Maintenance Protocol v0.3 已验证并冻结；长期 PKS System 仍处于真实运行实验期。**

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

如果无法读取当前 canonical Git state，**fail closed：不得仅凭聊天记忆或旧上下文执行 PKS write。**

## Repository Visibility Gate

PKS 必须先考虑 repository visibility，再决定哪些内容可以进入 Git history。

在 repository 未被明确验证为 private 前，默认按 **PUBLIC_SAFE** 模式处理。

PUBLIC_SAFE 下硬性 REJECT：

- credential、API key、token、password、private key、cookie、secret
- 可识别的私人敏感信息
- 财务/账户类敏感信息
- 未公开的第三方信息或私人通信原文
- 未公开商业、客户、合同、人事、竞争或策略判断
- 任何一旦进入公开 Git history 会造成不可逆暴露风险的内容

如果这些材料中存在长期价值，只允许保存**去标识、去敏后的机制、模型、边界和通用经验**。

即使 repository 未来改为 private，credentials / secrets 仍然硬性 REJECT；private 不等于可以无条件保存敏感信息。

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

### Novelty / Duplicate Exclusion Gate

通过价值 Gate 后，在 `AUTO-CAPTURE` 前必须再做一次已有知识检查。该 Gate 不使用没有可靠测量基础的“80% 语义相似度”等伪精确阈值，而检查拟写入内容是否带来 **material delta**。

处理规则：

- 如果已有节点已经包含同一核心结论，且新内容没有新增 evidence、boundary、counterexample、纠错、decision impact 或 project validation，则结果为 **REJECT / no-op**；
- 如果主题相同但存在 material delta，则 **UPDATE existing node**，而不是创建平行节点；
- 只有已有结构无法自然容纳、且内容形成稳定独立主题时，才新增文件。

`material delta` 的典型形式：

- 新证据显著提高或降低原结论可信度；
- 新增重要适用前提或失效条件；
- 出现能够挑战旧模型的反例；
- 项目实践验证、修正或证伪原模型；
- 原结论被纠正；
- 新知识会改变真实决策方式。

## Three Outcomes

### 1. AUTO-CAPTURE

适用于：

- 稳定机制
- 成熟模型
- 清晰边界条件
- 已验证的项目经验
- 经纠错后明显更可靠的结论

处理：

`读取最新 canonical state → 定位已有节点 → Novelty / Duplicate Gate → Epistemic Status → safety guard → latest SHA → atomic commit / PR → capture log → user receipt`

默认不需要因普通、可逆的知识维护在写入前再次询问用户，但必须在写入后给出可纠正回执。

### 2. CANDIDATE

适用于：

- 商业或市场假设
- 有争议的理论
- 证据不足的推论
- 尚未验证但值得持续追踪的判断

处理优先级：

1. 若已有对应节点，明确标注 `Inference` / `Judgment` / `Unknown` 与证据缺口；
2. 若尚不值得成为正式节点，进入 `learning-queue.md`；
3. 不把候选假设写成 Fact。

### 3. REJECT

默认不进入长期知识节点：

- 普通聊天记录或逐字转录
- 一次性查询
- 很快失效、且无长期机制价值的新闻本身
- 临时操作步骤
- 单纯定义、没有新增理解的内容
- 无证据且无后续价值的即时观点
- 某项目的日常 task log
- 与已有节点核心结论重复、且没有 material delta 的内容
- Visibility Gate 禁止的敏感或非公开内容

REJECT 不产生知识节点；如果该主题已经进入正式 Capture Gate 评估，则允许只在 `reviews/capture-log.md` 留一条最小 operational record，用于评估 Gate 行为。

## Update vs New File

优先顺序：

1. 更新现有知识节点
2. 更新已有 Mental Model
3. 更新 Project Learning Record
4. 更新 Learning Queue
5. 只有当已有结构无法自然容纳且内容已形成稳定主题时，才新增文件

避免为每次讨论制造孤立 Markdown 文件。

## Mandatory Epistemic Status

对**新增或实质修改的知识性主张**，Epistemic Status 不再是“必要时可选”，而是必须明确。

可使用：

- `Fact`
- `Estimate`
- `Inference`
- `Judgment`
- `Unknown`

规则：

1. `Fact` 必须能指出 evidence：直接观测、项目结果、Git/运行证据、可靠一手资料、标准或可复核高质量来源之一；
2. 没有足够 evidence 的综合性机制或解释，默认使用 `Inference`；
3. 含目标权重、取舍或规范性选择的结论使用 `Judgment`；
4. 数据或证据不足时明确 `Unknown`，不能靠语气补全；
5. `Estimate` 应说明估算基础、时间点或关键假设；
6. 改变旧结论时保留 `旧判断 → 新证据 → 新判断`，禁止静默覆盖；
7. 项目验证是强证据之一，但不是 `Fact` 的唯一合法来源；已被可靠标准/一手资料支持的机制不必人为降级为 Inference。

导航、模板、纯协议指令不要求每一行加 status；只要它们在陈述事实性或知识性主张时遵守上述规则。

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

## Capture Decision Log

为了让 Capture precision / recall proxy、duplicate、correction 等指标可被复盘，正式 Capture Gate 的决策写入：

`reviews/capture-log.md`

最小字段：

`date | outcome | topic | epistemic status | gate/reason | target | commit | correction`

规则：

- 只记录真正进入 Capture Gate 的主题；普通聊天不记；
- AUTO-CAPTURE / CANDIDATE / REJECT 都可记；
- 同一会话多个事件可批量追加，避免无意义 commit churn；
- log 是 operational evidence，不是知识节点；
- capture log 不替代 Git history，也不作为第二 Source of Truth。

## User Correction Receipt

AUTO-CAPTURE 或 CANDIDATE 产生 Git write 后，AI 应在当前对话给出极简回执：

`已 AUTO-CAPTURE/CANDIDATE → <file> → <commit> → <epistemic status>。如判断不对，可直接纠正。`

目标是让用户拥有低摩擦 veto / correction loop，而不是增加写前审批流。

## Commit Policy

知识维护使用可读、可搜索的 commit 前缀：

- `knowledge:` 新增或实质更新长期知识
- `model:` 新增或修正 Mental Model / 决策框架
- `hypothesis:` 记录或调整待验证假设
- `project:` 沉淀真实项目验证经验
- `queue:` 调整长期学习缺口
- `review:` 定期复盘、证据刷新、认知重构、capture log
- `chore:` 结构、模板、维护协议等非知识内容

一个 commit 应尽量对应一个认知变化或一个紧密相关的主题。

## Conflict and Safety

- 不覆盖用户明确要求保留的重要旧判断；应记录“旧判断 → 新证据 → 修正后判断”。
- 如知识内容存在明显争议，保留 competing explanations，而不是强行统一。
- 涉及敏感私密信息、凭证、账户密钥等内容，不进入知识库。
- 业务敏感原始材料默认不直接沉淀；如其中存在长期价值，优先保存去敏后的机制、模型和结论。
- 不因为“可以自动写”而扩大仓库 scope。

### Pre-write Safety Gate

在任何 PKS write 前执行：

1. 检查拟提交内容是否包含 credential、API key、token、password、private key、cookie、secret 或其他明显敏感标识；
2. 若当前 GitHub write plane / repository backend 提供并真正启用了 secret scanning，对拟提交内容 / diff 执行扫描；
3. 一旦命中，**阻断写入**，先去敏或删除，再重新检查；
4. 不允许采用“先 commit，再从最新版本删除”的方式处理 secret，因为 Git history 仍会保留泄露内容；
5. Secret scanner 只能发现模式型 secrets，不能替代对商业敏感、私人或受限信息的语义判断。

当前 repository 的 GitHub Advanced Security secret scanning 未被验证为可用，因此不能宣称 scanner 是强制执行层；协议级 safety gate 才是当前有效边界。

## Protocol Self-Modification Rule

AUTO-CAPTURE **不得自动修改自己的宪法**。

以下属于 high-risk protocol mutation：

- `AUTO_CAPTURE.md`
- `MAINTENANCE.md`
- 对 Capture Gate、Safety Gate、Epistemic Status、Source of Truth、写入权限边界的规则修改

这类修改必须：

`explicit review → branch → PR → diff review → merge`

普通知识 Capture 不得顺便放宽 Capture Gate 或安全规则。

## Review Loop

Review 不按固定高频运行。当前阶段采用事件驱动：

- 累积若干真实 capture 后
- 重大项目结束后
- 核心模型被反例挑战后
- 某领域准备进入重要实际决策前
- capture log 显示重复 miss / correction / trigger miss / conflict 等持续摩擦时

当知识规模明显扩大后，再考虑 staleness、confidence 与自动检查；不为了日历而自动重写知识库。

## Success Criterion

自动捕获机制成功的标准不是 commit 数量，而是：

- 后续对话能复用已有模型
- 新证据能修正旧知识而非重复堆积
- 项目经验能反哺通用认知
- 非 PKS/MCP 的真实领域知识逐渐出现并被复用
- 长期知识缺口越来越清晰
- Git history 能解释知识如何演化
- 用户纠正能被系统吸收，而不是被后续 AI 再次恢复成旧错误
