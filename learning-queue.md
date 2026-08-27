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

## Deferred / Condition-triggered

### Lightweight GitHub Text MCP content search

**Status:** CANDIDATE / defer until retrieval friction

优先考虑的未来能力不是 Vector DB，而是最小文本检索，例如：

- `search_text` / keyword search
- 路径过滤
- line-range read
- 返回匹配文件 + 少量上下文

触发条件：

- duplicate / retrieval miss 重复发生；
- 为定位一个已有节点需要读取越来越多无关文件；
- 知识文件数量明显增长后出现 read amplification；
- 当前 `list_directory + read_text_file` 已经成为可观察瓶颈。

如果触发，先实现轻量 grep/content search，再用真实数据判断是否需要 semantic retrieval。

### Minimal typed frontmatter

**Status:** CANDIDATE / defer

候选字段：

- `id`
- `aliases`
- `epistemic_status`
- `last_verified`
- `tags`

当前不做全库 migration。只有出现以下真实问题时才实施：

- aliases / stable id 能显著减少 duplicate node；
- staleness review 需要可机器读取的 `last_verified`；
- metadata filtering 能明显减少全文读取成本。

避免为了“结构化”把 Markdown 知识库提前变成数据库 schema。

### Semantic retrieval / RAG / GraphRAG

**Status:** DEFER

只有轻量文本搜索已经被真实检索失败证明不足时再评估。当前不引入 Vector DB、GraphRAG 或独立数据库作为第二 Source of Truth。

## Queue Rule

新主题进入队列前检查：

1. 是否解决当前真实问题？
2. 是否连接多个已有知识节点？
3. 是否提升高频决策质量？
4. 是否能通过项目验证？
5. 学习成本与长期杠杆是否匹配？

若多数答案为否，默认不进入高优先级队列。
