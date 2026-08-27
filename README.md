# Personal Knowledge System

这是一个长期演化的个人知识体系（Personal Knowledge System, PKS）。目标不是积累孤立笔记，而是把学习、实践、模型、决策与复盘组织成可持续增长的知识网络。

**当前状态：Capture / Maintenance Protocol v0.3 已验证并冻结；Long-term PKS System 仍在真实使用实验期。**

**人类阅读入口：** [HOME.md](HOME.md)

## 核心结构

1. **Thinking & Epistemology** — 系统思维、逻辑、因果、概率、决策与批判性思维。
2. **Telecom & Digital Infrastructure** — 5G、频谱、RAN、传输、FWA/FTTH、网络经济学与运营商战略。
3. **AI, Agents & Software Engineering** — LLM、Agent、MCP、API、数据库、Git/GitHub、CI/CD、云与自动化。
4. **Business, Strategy & Investment** — 市场、客户、竞争、商业模式、单位经济、现金流、ROI 与风险决策。
5. **Projects & Experiments** — 用真实项目验证知识和模型。
6. **Knowledge Compounding** — 知识地图、心智模型、学习队列、复盘与持续迭代。

领域地图代表 **Intended Coverage**；只有已有条目、evidence 或真实使用验证的内容才属于 **Possessed / Instantiated Knowledge**。详见 `knowledge-map/master-map.md`。

## 学习闭环

`现实问题 → 理解机制 → 定位知识地图 → 连接已有知识 → 抽象模型 → 检查边界/反例 → 实践验证 → 修正模型 → 发现知识缺口 → Learning Queue`

## 掌握度

- **M0** 陌生
- **M1** 理解概念
- **M2** 理解机制
- **M3** 能实际使用
- **M4** 能设计与判断
- **M5** 能迁移与创新

Mastery 只用于真正 possessed / instantiated 的节点；Intended Coverage 不自动拥有 mastery。

## Repository Layout

```text
personal-knowledge/
├── HOME.md                     # Android / Obsidian 友好的人类阅读入口
├── GOAL.md                     # 当前目标、checkpoint、next priorities
├── AUTO_CAPTURE.md             # 自动知识捕获 Gate、三态、安全与 epistemic policy
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
│   ├── capture-log.md
│   ├── README.md
│   └── _template.md
├── learning-queue.md
├── MAINTENANCE.md
└── .gitignore
```

## Canonical Source of Truth

**Git repository 是 canonical knowledge store；GitHub 是当前托管与协作平台。**

重要更新应保留可追踪的 commit history；较大结构、高风险或协议调整使用 branch/PR，确保其他 AI 或未来会话可以从 repository state 继续工作。

聊天内容本身不自动成为知识。只有经 Capture Gate 整理、具有长期价值、能够连接现有知识结构的内容才进入 repository。

如果当前 AI 无法读取最新 canonical state，则不得凭聊天 memory 继续写入。

## Visibility & Safety

在 repository 未明确验证为 private 前，PKS 默认按 `PUBLIC_SAFE` 运行：私人敏感、财务/账户、未公开商业/客户/第三方信息和 secrets 不进入 Git history。长期价值只能以去敏后的机制/模型形式保存。

## Android / Obsidian Human View Layer

Obsidian 是 **View / Edit Layer**，不是新的 canonical storage。

```text
Git repo / GitHub
        ↕ Git
      GitSync
        ↕
Android local folder
        ↕
     Obsidian
```

原则：

- 使用 GitSync clone / pull / commit / push 当前 repository；
- 在 Obsidian 中把本地 repository folder 打开为 Vault；
- `.obsidian/`、`.trash/` 等设备/UI 状态通过 `.gitignore` 排除；
- 手机编辑前先 sync，编辑后尽快 sync；
- 同一 Vault 不同时让 GitSync 与 Obsidian Git plugin 管理 Git；
- 内部链接优先标准 Markdown 相对链接；
- conflict 显式解决，不使用 `merge=union` 静默拼接语义冲突。

## Automatic Capture

当对话中出现机制、模型、决策框架、经过纠错的稳定结论、重要反例、项目验证经验或长期知识缺口时，AI 应主动判断是否值得沉淀，并按 `AUTO_CAPTURE.md` 执行。

自动维护有三种结果：

- **AUTO-CAPTURE**：稳定、高价值内容，更新 canonical Git state。
- **CANDIDATE**：值得保留但证据不足，明确 epistemic status 或进入 Learning Queue。
- **REJECT**：低价值、重复、敏感或不适合长期保存的内容，不形成知识节点。

知识性主张必须区分 `Fact / Estimate / Inference / Judgment / Unknown`。

正式 Capture Gate 决策可记录到 `reviews/capture-log.md`，用于观察系统是否真实有效，而不是以 commit 数量衡量成功。

## Continue / Handoff

当用户要求“继续长期知识库”或新对话触发长期知识沉淀时：

1. 读取 `GOAL.md`；
2. 读取 `MAINTENANCE.md` 与 `AUTO_CAPTURE.md`；
3. 检查 main 最近 commits 与 open PR；
4. 按需要读取 Knowledge Map / Mental Models / Learning Queue / Capture Log；
5. 从 `GOAL.md` 当前状态继续，不从头重建分类；
6. 记住：**protocol v0.3 已冻结，system 仍 experimental；下一步是真实使用，不是继续加架构。**
