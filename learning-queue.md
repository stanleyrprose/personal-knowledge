# Learning Queue v0.1

Learning Queue 不是课程表，而是按杠杆率排序的知识缺口列表。

## Priority A — Core / 目标 M4

### Systems Thinking & Decision
- 因果推理与反事实
- Bayesian 更新与概率判断
- 系统反馈、延迟与非线性
- Decision under uncertainty
- Game theory / incentive design

### Telecom
- 5G radio capacity model
- TDD synchronization / timing architecture
- Spectrum economics
- FWA vs FTTH economics
- Operator CAPEX → revenue → ROI bridge

### AI / Agent
- Agent state / memory / tool architecture
- Multi-agent orchestration
- MCP architecture and trust boundaries
- Evaluation / observability / failure recovery
- Long-running autonomous workflows

### Business / Investment
- Unit economics
- Cash-flow modeling
- Scenario / sensitivity analysis
- Competitive advantage and market structure
- Small-bet experimentation

## Priority B — Working Knowledge / 目标 M2–M3

- FastAPI / backend architecture
- REST / HTTP
- SQL / database fundamentals
- Git / GitHub advanced workflow
- GitHub Actions / CI/CD
- Docker
- Linux operations
- Cloudflare / reverse proxy / tunneling
- OAuth / Device Flow
- Queue / scheduler / webhook concepts

## Priority C — Reference / 按需学习

- Compiler internals
- Linux kernel internals
- CUDA kernel optimization
- Deep database engine internals
- Low-level distributed consensus implementation

## Activated / Under Validation

### Human-facing PKM frontend for the Git knowledge base

**Status:** TRIGGERED / IMPLEMENTING — Obsidian + GitSync

2026-08-27 出现真实使用摩擦：GitHub repository 作为 Source of Truth 工作正常，但 Android 手机上直接阅读 GitHub Markdown 不方便，已经影响日常知识浏览体验。

因此之前的条件触发候选正式进入实施：

- GitHub 继续作为唯一 canonical Source of Truth；
- Android 使用 GitSync 同步 repository；
- Obsidian 作为 Human View / Edit Layer；
- `.obsidian/` 等设备/UI 状态不写入知识库；
- 先观察移动阅读收益、编辑冲突与维护成本，再决定是否进一步引入 backlink / graph / plugins。

### Validation questions

- 手机上通过 `HOME.md` 的导航是否明显改善阅读效率？
- GitSync 在真实使用中是否稳定？
- Human edit 与 AI edit 是否出现频繁 merge conflict？
- Obsidian backlink / local search 是否产生真实收益？
- 是否需要同步少量 vault-level 配置，还是继续完全忽略 `.obsidian/`？

只有真实数据证明需要时，才继续扩大 frontend/sync scope。

## Queue Rule

新主题进入队列前检查：

1. 是否解决当前真实问题？
2. 是否连接多个已有知识节点？
3. 是否提升高频决策质量？
4. 是否能通过项目验证？
5. 学习成本与长期杠杆是否匹配？

若多数答案为否，默认不进入高优先级队列。
