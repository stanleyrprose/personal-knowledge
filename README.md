# Personal Knowledge System

这是一个长期演化的个人知识体系（Personal Knowledge System, PKS）。目标不是积累孤立笔记，而是把学习、实践、模型、决策与复盘组织成可持续增长的知识网络。

## 核心结构

1. **Thinking & Epistemology** — 系统思维、逻辑、因果、概率、决策与批判性思维。
2. **Telecom & Digital Infrastructure** — 5G、频谱、RAN、传输、FWA/FTTH、网络经济学与运营商战略。
3. **AI, Agents & Software Engineering** — LLM、Agent、MCP、API、数据库、Git/GitHub、CI/CD、云与自动化。
4. **Business, Strategy & Investment** — 市场、客户、竞争、商业模式、单位经济、现金流、ROI 与风险决策。
5. **Projects & Experiments** — 用真实项目验证知识和模型。
6. **Knowledge Compounding** — 知识地图、心智模型、学习队列、复盘与持续迭代。

## 学习闭环

`现实问题 → 理解机制 → 定位知识地图 → 连接已有知识 → 抽象模型 → 检查边界/反例 → 实践验证 → 修正模型 → 发现知识缺口 → Learning Queue`

## 掌握度

- **M0** 陌生
- **M1** 理解概念
- **M2** 理解机制
- **M3** 能实际使用
- **M4** 能设计与判断
- **M5** 能迁移与创新

不同知识节点不追求同样深度。核心领域以 M4 为目标；工具型知识通常达到 M2–M3 即可；低杠杆知识保持 Reference 即可。

## Repository Layout

```text
personal-knowledge/
├── GOAL.md                     # 当前长期目标、checkpoint、next priorities
├── README.md
├── knowledge-map/
│   ├── master-map.md
│   └── _entry-template.md
├── mental-models/
│   └── core-models.md
├── projects/
│   ├── README.md
│   └── _template.md
├── reviews/
│   ├── README.md
│   └── _template.md
├── learning-queue.md
└── MAINTENANCE.md
```

## Source of Truth

GitHub repository 是该知识体系的长期 Source of Truth。重要更新应保留可追踪的 commit history；较大结构调整使用 branch/PR，确保其他 AI 或未来会话可以从仓库状态继续工作。

聊天内容本身不自动成为知识。只有经整理、具有长期价值、能够连接现有知识结构的内容才进入仓库。

## Continue / Handoff

当用户要求“继续长期知识库”或“继续 /goal”时：

1. 读取 `GOAL.md`；
2. 检查 main 最近 commits 与 open PR；
3. 再读取 `knowledge-map/master-map.md`、`mental-models/core-models.md`、`learning-queue.md` 和 `MAINTENANCE.md`；
4. 从 `GOAL.md` 的 Current State / Next Priorities 接着做，不从头重新设计体系。
