# Personal Knowledge System

这是一个长期演化的个人知识体系（Personal Knowledge System, PKS）。目标不是积累孤立笔记，而是把学习、实践、模型、决策与复盘组织成可持续增长的知识网络。

**人类阅读入口：** [HOME.md](HOME.md)

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
├── HOME.md                     # Android / Obsidian 友好的人类阅读入口
├── GOAL.md                     # 当前长期目标、checkpoint、next priorities
├── AUTO_CAPTURE.md             # 自动知识捕获 Gate、三态与 commit policy
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
├── MAINTENANCE.md
└── .gitignore                  # 忽略 Obsidian / OS 设备状态
```

## Source of Truth

GitHub repository 是该知识体系的长期 Source of Truth。重要更新应保留可追踪的 commit history；较大结构调整使用 branch/PR，确保其他 AI 或未来会话可以从仓库状态继续工作。

聊天内容本身不自动成为知识。只有经整理、具有长期价值、能够连接现有知识结构的内容才进入仓库。

## Android / Obsidian Human View Layer

Obsidian 被定义为 **View / Edit Layer**，不是新的 canonical storage。

推荐同步结构：

```text
GitHub Source of Truth
        ↕ Git
      GitSync
        ↕
Android local folder
        ↕
     Obsidian
```

原则：

- 使用 GitSync clone / pull / commit / push 当前 repository；
- 在 Obsidian 中直接把该本地 repository folder 打开为 Vault；
- `.obsidian/`、`.trash/` 等设备/UI 状态通过 `.gitignore` 排除；
- 手机编辑前先 sync，编辑后尽快 sync；
- 同一 Vault 不同时让 GitSync 与 Obsidian Git plugin 管理 Git；
- 内部链接优先使用标准 Markdown 相对链接，保持 GitHub 和 Obsidian 双端兼容；
- conflict 应显式解决，不使用 `merge=union` 静默拼接语义冲突。

## Automatic Capture

当对话中出现机制、模型、决策框架、经过纠错的稳定结论、重要反例、项目验证经验或长期知识缺口时，AI 应主动判断是否值得沉淀，并按 `AUTO_CAPTURE.md` 执行。

自动维护有三种结果：

- **AUTO-CAPTURE**：稳定、高价值内容，更新 Source of Truth。
- **CANDIDATE**：值得保留但证据不足，明确 hypothesis / inference / unknown 或进入 Learning Queue。
- **REJECT**：普通聊天、一次性查询、短期事实等低长期价值内容，不写入仓库。

## Continue / Handoff

当用户要求“继续长期知识库”或“继续 /goal”，或新对话触发长期知识沉淀时：

1. 读取 `GOAL.md`；
2. 读取 `MAINTENANCE.md` 与 `AUTO_CAPTURE.md`；
3. 检查 main 最近 commits 与 open PR；
4. 按需要读取 `knowledge-map/master-map.md`、`mental-models/core-models.md`、`learning-queue.md`；
5. 从 `GOAL.md` 的 Current State / Next Priorities 接着做，不从头重新设计体系。
