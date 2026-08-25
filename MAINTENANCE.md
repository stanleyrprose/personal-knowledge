# Knowledge System Maintenance Protocol v0.1

## 1. Source of Truth

GitHub 是长期 Source of Truth。聊天记录是输入，不是最终知识资产。

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

## 3. 每次重要学习的处理协议

1. 回答现实问题。
2. 定位 Knowledge Map 节点。
3. 连接已有知识。
4. 判断能否抽象为 Mental Model。
5. 检查前提、反例和失效条件。
6. 若有真实项目，记录验证方式。
7. 发现的新缺口进入 Learning Queue。
8. 只有长期有效的信息进入 Source of Truth。

## 4. 变更原则

- 小型内容补充：直接更新对应文件并留下清晰 commit。
- 跨目录结构调整或大规模重构：feature branch → PR → review → merge。
- 不为整理而整理，不追求形式上的完整。
- 保留历史，不无理由覆盖原有重要判断。

## 5. Fact / Estimate / Inference / Judgment / Unknown

知识条目在必要时明确区分：

- **Fact**：有可靠证据支持。
- **Estimate**：基于模型或有限数据估算。
- **Inference**：由事实推导，但并非直接观测。
- **Judgment**：带有目标、权重或价值取舍。
- **Unknown**：当前证据不足。

## 6. 掌握度管理

- M0：陌生
- M1：理解概念
- M2：理解机制
- M3：能实际使用
- M4：能设计与判断
- M5：能迁移与创新

学习投入依据用途决定，不追求所有节点满级。

## 7. 定期 Review

建议每月或在重大项目完成后检查：

- 哪些模型被现实证伪或需要修正？
- 哪些知识已经从 M1/M2 提升到 M3/M4？
- 哪些 Learning Queue 条目长期没有价值，应删除？
- 是否出现新的跨域连接？
- 哪些项目经验值得沉淀为通用模型？

## 8. AI Handoff

其他 AI 接手时优先读取：

1. `README.md`
2. `knowledge-map/master-map.md`
3. `mental-models/core-models.md`
4. `learning-queue.md`
5. `MAINTENANCE.md`
6. 最近 commits / PRs

先恢复当前知识结构和变更历史，再继续维护，避免重新建立另一套分类体系。
