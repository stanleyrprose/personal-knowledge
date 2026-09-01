# Operational Observability Output Minimization Model

**Status:** INSTANTIATED MODEL  
**Epistemic Status:** Fact（项目观测） + Inference（跨场景泛化）  
**Evidence:** 2026-09-01 的 systemd + GitHub Actions 控制面实测：人类可读的 verbose status 命令会附带 recent journal；改为白名单化的结构化 systemd properties 后，状态判断能力保留，真实 GitHub-hosted workflow gates 全部通过。

## Core principle

`Automation output should expose decision-required state, not human-debug context by default.`

控制面的最小权限不只包括“允许执行哪些命令”，还包括“这些命令允许把哪些数据带回自动化日志”：

`Control-plane exposure ≈ command capability × output/data surface`

## Why it matters

一个命令即使本身只读，也可能把日志、连接参数、用户输入、路径或其他上下文带到 stdout/stderr。CI/CD、远程执行器和 Agent orchestration 通常会长期保存这些输出，因此“只允许安全命令”并不等于“输出面安全”。

## Mechanism

许多人类诊断命令为了方便排障，会主动聚合上下文。例如 `systemctl status` 不只显示 unit state，还会附带最近的 journal。若被管理进程的日志里包含敏感连接参数或用户内容，则调用链可能变成：

`Agent log → human-friendly status command → CI stdout → durable/shared automation log`

这是一条二次数据外泄路径，根因不是命令写权限过大，而是输出面没有最小化。

更安全的自动化路径是：

`structured source → explicit property allowlist → decision → minimal log`

例如 systemd 状态检查优先使用：

`systemctl show <unit> --property=Id,LoadState,ActiveState,SubState,UnitFileState,MainPID,ExecMainStatus,NRestarts,MemoryCurrent`

而不是默认把 `systemctl status` 的 journal tail 带入 CI。

## How to use

1. 先区分消费者：interactive human diagnosis 还是 unattended automation。
2. 明确自动化真正需要做判断的字段，例如 `ActiveState`、`MainPID`、exit status。
3. 优先调用结构化接口，并显式白名单字段；不要默认输出完整对象。
4. 默认不把 recent logs、environment、full command line、request payload 带入 CI log。
5. 当基础状态不足以定位故障时，再进入显式 diagnostic escalation，使用受控的人类排障通道获取更多日志。
6. 把 stdout/stderr、artifact、annotation、workflow summary 都视为潜在的数据出口，而不仅是“调试信息”。

## Preconditions

该模型尤其适用于：

- GitHub Actions / GitLab CI / Jenkins 等持久化构建日志；
- SSH forced-command / remote admin control plane；
- systemd、Docker、Kubernetes、云 CLI 等会聚合状态与日志的运维工具；
- Agent / bot / gateway 等可能把外部消息、连接信息写入日志的服务；
- 多人可读或会被第三方 observability 系统采集的 stdout/stderr。

## Boundaries / failure conditions

- `systemctl show` 本身并不自动安全；如果请求 `Environment`、完整 `ExecStart` 等字段，仍可能暴露敏感数据。关键是**属性白名单**而不是命令名称。
- 某些故障必须查看 journal/log 才能定位；此时应把日志读取变成显式升级能力，而不是永久关闭诊断。
- 即使 CI 平台支持 secret masking，也不能假设所有敏感值都已注册为 mask pattern；未知 token、用户内容和临时连接参数可能漏过。
- 本地、短生命周期、只有单一可信管理员可见的交互式终端，其输出风险低于共享 CI，但仍应避免无必要暴露。
- 如果真正需要机器分析日志，应使用受控 log pipeline、访问控制与必要的 redaction，而不是把完整日志直接塞进普通 workflow output。

## Alternatives / trade-offs

- **Source minimization（优先）**：只查询需要的字段，最小暴露面。
- **Post-output redaction**：先获取完整输出再过滤，兼容性高但更脆弱，因为 secret 已经进入处理链。
- **Restricted diagnostic channel**：正常自动化保持精简；发生故障时由更高权限、短生命周期的人工/受控任务读取详细日志。
- **Dedicated observability store**：日志进入有访问控制的日志系统，CI 只保存查询结果或事件引用。

## Project validation

在一个双 VPS Agent 控制面中，GitHub Actions 使用 restricted root SSH + forced-command allowlist，只允许 health/status/restart。将 Agent 从 tmux 更新为 systemd 后，最初的 `agent-status` 使用 `systemctl status`。真实验证发现它会附带 recent journal，因此即使命令 allowlist 正确，GitHub Actions 日志仍可能获得不必要的运行上下文。

修正为 `systemctl show` 的明确 property allowlist 后：

- 状态判断仍能获得 `ActiveState / MainPID / NRestarts / MemoryCurrent` 等必要字段；
- restart/status 的控制逻辑保持不变；
- 未授权命令仍被 forced-command dispatcher 拒绝；
- 两个目标主机的真实 GitHub-hosted `health` 与 `agent-status` workflow 均通过。

因此该项目验证了一个可迁移结论：**least privilege 既要约束 action surface，也要约束 output surface。**

## Connections

- Principle of Least Privilege
- Data minimization
- Capability-based security
- Observability / logging hygiene
- CI/CD secret handling
