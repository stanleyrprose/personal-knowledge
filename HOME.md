# PKS Home

这是 `stanleyrprose/personal-knowledge` 的人类阅读入口，优先为 Android / Obsidian 使用设计，同时保持 GitHub 可直接浏览。

> GitHub repository 仍是唯一长期 Source of Truth。Obsidian 只是 View / Edit Layer。

## Start Here

- [Current Goal / Checkpoint](GOAL.md)
- [Repository Overview](README.md)
- [Maintenance Protocol](MAINTENANCE.md)
- [Automatic Capture Policy](AUTO_CAPTURE.md)
- [Learning Queue](learning-queue.md)

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
- [Auto Capture Validation 01](reviews/2026-08-25-auto-capture-validation-01.md)
- [PKS v0.3 Cross-Conversation Validation / Stable](reviews/2026-08-27-cross-conversation-validation-v03-stable.md)
- [PKS v0.3 Architecture Peer-Review Brief](reviews/PKS-v0.3-build-architecture-peer-review-brief.md)

## Android / Obsidian Operating Rules

1. 打开 Obsidian 前先通过 GitSync pull / sync。
2. 在 Obsidian 中阅读或编辑 Markdown。
3. 编辑结束后尽快通过 GitSync commit + push。
4. `.obsidian/` 属于设备/UI 状态，不进入 PKS Source of Truth。
5. 同一 Vault 不同时启用 GitSync 和 Obsidian Git plugin 管理 Git。
6. Git conflict 不使用 `merge=union` 静默拼接；应保留冲突并判断哪一版知识正确。
7. 内部导航优先使用标准 Markdown 相对链接，而不是只使用 `[[Wiki Links]]`，保证 GitHub 与 Obsidian 双端可读。

## Architecture

```text
                    GitHub
             Personal Knowledge Repo
               Source of Truth
                      │
          ┌───────────┴───────────┐
          │                       │
   GitHub Text MCP             GitSync
      AI read plane                │
          │                    Android local
      Github MCP                   │
     AI write plane             Obsidian
          │                   Human View/Edit
          └────────────┬───────────┘
                       │
                    Markdown
```

## If Something Conflicts

优先级：

`Git history + current repository state > stale local copy > chat memory`

如果 Android 本地修改与 AI 的 GitHub commit 冲突，不自动覆盖。先比较语义差异，再决定保留、合并或修正。
