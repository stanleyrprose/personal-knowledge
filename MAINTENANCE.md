# Knowledge System Maintenance Protocol v0.2

## 1. Source of Truth

GitHub 是长期 Source of Truth。聊天记录是输入，不是最终知识资产。

`GOAL.md` 是当前长期目标、checkpoint 与跨会话接力入口；若它与实际 Git history 冲突，以当前仓库状态和 Git history 为准，并修正 `GOAL.md`。

## 2. 什么值得进入知识库

优先记录：

- 可跨场景复用的机制
- 经验证的重要结论
- 心智模型及其边界
- 项目实践改变了原有认知的内容
- 高价值知识缺口

默认不记录：

- 短期新闻本身
- 一次性查询结果
- 无证据的临时观点
- 大量原始聊天转录
- 仅属于某项目日常执行的 task log

## 3. 每次重要学习的处理协议

1. 回答现实问题。
2. 判断是否具有长期沉淀价值。
3. 定位 Knowledge Map 节点。
4. 连接已有知识。
5. 判断能否抽象为 Mental Model。
6. 检查前提、反例和失效条件。
7. 若有真实项目，记录验证方式。
8. 发现的新缺口进入 Learning Queue。
9. 必要时更新 Mastery。
10. 只有长期有效的信息进入 Source of Truth。

## 4. 知识条目的最小质量标准

长期知识条目优先回答：

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

不要求每个条目机械填满所有字段，但不能只保存定义或结论。

## 5. 变更原则

- 小型内容补充：直接更新对应文件并留下清晰 atomic commit。
- 跨目录结构调整或大规模重构：feature branch → PR → review → merge。
- 不为整理而整理，不追求形式上的完整。
- 保留历史，不无理由覆盖原有重要判断。
- 先用真实内容验证模板，再增加自动化或复杂 metadata。

## 6. Fact / Estimate / Inference / Judgment / Unknown

知识条目在必要时明确区分：

- **Fact**：有可靠证据支持。
- **Estimate**：基于模型或有限数据估算。
- **Inference**：由事实推导，但并非直接观测。
- **Judgment**：带有目标、权重或价值取舍。
- **Unknown**：当前证据不足。

## 7. 掌握度管理

- M0：陌生
- M1：理解概念
- M2：理解机制
- M3：能实际使用
- M4：能设计与判断
- M5：能迁移与创新

学习投入依据用途决定，不追求所有节点满级。

## 8. 定期 Review

建议每月或在重大项目完成后检查：

- 哪些模型被现实证伪或需要修正？
- 哪些知识已经从 M1/M2 提升到 M3/M4？
- 哪些 Learning Queue 条目长期没有价值，应删除？
- 是否出现新的跨域连接？
- 哪些项目经验值得沉淀为通用模型？
- 哪些依赖法规、市场、软件版本等变化的 Fact 已经 stale？

## 9. AI Handoff

其他 AI 接手时优先读取：

1. `GOAL.md`
2. `README.md`
3. `knowledge-map/master-map.md`
4. `mental-models/core-models.md`
5. `learning-queue.md`
6. `MAINTENANCE.md`
7. 最近 commits / PRs

先恢复当前知识结构、目标和变更历史，再继续维护，避免重新建立另一套分类体系。
