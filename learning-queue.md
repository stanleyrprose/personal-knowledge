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

## Deferred / Condition-triggered

### Human-facing PKM frontend for the Git knowledge base

**Status:** CANDIDATE / defer

当前先用 `ChatGPT + GitHub MCP + Markdown/Git` 验证长期知识维护。只有出现真实使用摩擦时，再评估 Obsidian + GitSync、SiYuan 或其他 frontend。

触发条件包括：

- 手机端阅读/编辑成为高频需求；
- GitHub UI 明显降低知识浏览效率；
- backlink / graph / local search 对实际学习产生明确收益；
- 人工修改与 AI 修改需要稳定双向同步。

在这些条件出现前，不为了“架构完整”提前增加同步、冲突处理和额外状态源。

## Queue Rule

新主题进入队列前检查：

1. 是否解决当前真实问题？
2. 是否连接多个已有知识节点？
3. 是否提升高频决策质量？
4. 是否能通过项目验证？
5. 学习成本与长期杠杆是否匹配？

若多数答案为否，默认不进入高优先级队列。
