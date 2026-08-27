# PKS Home

这是 `stanleyrprose/personal-knowledge` 的人类阅读入口，优先为 Android / Obsidian 使用设计，同时保持 GitHub 可直接浏览。

> Git repository 是长期 canonical knowledge store；GitHub 是当前 host。Obsidian 只是 View / Edit Layer。

## Start Here

- [Current Goal / Checkpoint](GOAL.md)
- [Repository Overview](README.md)
- [Maintenance Protocol](MAINTENANCE.md)
- [Automatic Capture Policy](AUTO_CAPTURE.md)
- [Learning Queue](learning-queue.md)

## 如何显式要求 AI 存入 PKS

除了 Automatic Capture，用户也可以直接指定某段内容进入 PKS 处理流程。

常用说法：

- `把这个存入 PKS`
- `把上面的结论沉淀到长期知识库`
- `这个只作为 CANDIDATE 存入 PKS`
- `把这条更新到已有 Mental Model，不要新建文件`
- `这条作为 Fact / Inference / Judgment 存`
- `不要存原始敏感信息，只存去敏后的通用原则`

### 显式指定意味着什么

显式请求会主动触发 PKS 读取与写入流程，不需要等待 AI 自己判断是否应该启动 Automatic Capture。

但显式指定**不会绕过** PKS 的质量与安全边界。AI 仍应：

1. 读取最新 `GOAL.md`、`MAINTENANCE.md`、`AUTO_CAPTURE.md` 与必要的现有知识节点；
2. 确认当前 canonical Git state 可读；如果无法读取，fail closed，不写；
3. 执行 Repository Visibility / Safety Gate；
4. 检查是否已有相同知识，优先 UPDATE existing node，而不是创建重复文件；
5. 明确 `Fact / Estimate / Inference / Judgment / Unknown`；
6. 检查 evidence、边界、反例与 material delta；
7. 基于最新 SHA 写入，避免 stale overwrite；
8. 普通知识使用 atomic commit；协议、高风险或结构性修改走 branch → PR → review → merge；
9. 写入后给出 file + commit + epistemic status 的 correction receipt，用户可直接纠正。

### 显式指定仍可能被阻断

即使用户明确说“保存”，以下内容也不能直接进入 Git history：

- credentials、API key、token、password、private key 等 secrets；
- 当前 visibility policy 禁止的私人或未公开敏感信息；
- 与已有节点完全重复且没有 material delta 的内容；
- 当前无法读取最新 canonical state、存在 write-without-read 风险的情况。

如果敏感材料中存在长期价值，优先保存**去标识、去敏后的机制、模型、边界和通用经验**。

### 两种入口

```text
自动模式：
真实对话
  → AI 判断是否触发 Automatic Capture
  → Capture / Candidate / Reject

显式模式：
用户说“存入 PKS”
  → 立即启动 PKS 处理流程
  → 仍经过 Safety / Duplicate / Epistemic / Freshness Guard
  → 更新已有节点或创建合适的新节点
```

显式指定的作用是减少“是否要启动 PKS 流程”的不确定性，不是绕过 PKS 的安全、证据和并发规则。

## Knowledge Map

- [Master Knowledge Map](knowledge-map/master-map.md)

## Mental Models

- [Core Models](mental-models/core-models.md)
- [MCP Content Transport Boundary Model](mental-models/mcp-content-transport-boundary.md)

## Projects / Validation

- [Projects Index](projects/README.md)
- [GitHub Text MCP Bridge](projects/github-text-mcp-bridge.md)

## Reviews

- [Reviews Index](reviews/README.md)
- [Capture Decision Log](reviews/capture-log.md)
- [Auto Capture Validation 01](reviews/2026-08-25-auto-capture-validation-01.md)
- [PKS v0.3 Cross-Conversation Validation / Stable](reviews/2026-08-27-cross-conversation-validation-v03-stable.md)
- [PKS v0.3 Architecture Peer-Review Brief](reviews/PKS-v0.3-build-architecture-peer-review-brief.md)
- [Peer Review Response 01](reviews/2026-08-27-peer-review-response-01.md)
- [Peer Review Response 02 — Grok](reviews/2026-08-27-peer-review-response-02-grok.md)

## Android / Obsidian Operating Rules

1. 打开 Obsidian 前先通过 GitSync pull / sync。
2. 在 Obsidian 中阅读或编辑 Markdown。
3. 编辑结束后尽快通过 GitSync commit + push。
4. `.obsidian/` 属于设备/UI 状态，不进入 PKS canonical state。
5. 同一 Vault 不同时启用 GitSync 和 Obsidian Git plugin 管理 Git。
6. Git conflict 不使用 `merge=union` 静默拼接；应保留冲突并判断哪一版知识正确。
7. 内部导航优先使用标准 Markdown 相对链接，而不是只使用 `[[Wiki Links]]`，保证 GitHub 与 Obsidian 双端可读。

## Architecture

```text
                    GitHub
             Personal Knowledge Repo
              Canonical Git State
                      │
          ┌───────────┴───────────┐
          │                       │
   GitHub Text MCP             GitSync
   current AI read                 │
          │                    Android local
      Github MCP                   │
    current AI write            Obsidian
          │                   Human View/Edit
          └────────────┬───────────┘
                       │
                    Markdown
```

## If Something Conflicts

优先级：

`Git history + current repository state > stale local copy > chat/model memory`

如果 Android 本地修改与 AI 的 Git commit 冲突，不自动覆盖。先比较语义差异，再决定保留、合并或修正。
