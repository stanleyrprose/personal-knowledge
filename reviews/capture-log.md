# Capture Decision Log

## Purpose

这是 PKS v0.3 实验期的最小 operational observability log，用于回答：Capture Gate 到底如何工作，而不是靠数月后的印象回忆。

它不是知识节点，不是第二 Source of Truth，也不是要求每次普通聊天都写日志。

只有一个主题真正进入正式 Capture Gate 判断时才记录。

## Fields

`date | outcome | topic | epistemic status | gate/reason | target | commit | correction`

## Historical Baseline

| Date | Outcome | Topic | Epistemic status | Gate / Reason | Target | Commit | Correction |
|---|---|---|---|---|---|---|---|
| 2026-08-25 | AUTO-CAPTURE | Git-backed durable knowledge model | not recorded historically | reusable mechanism + long-term AI continuity | `mental-models/core-models.md` | `4147220a` | none recorded |
| 2026-08-25 | CANDIDATE / DEFER | Human-facing PKM frontend | Judgment / Unknown | potential value but no real friction yet | `learning-queue.md` | `c28bbca2` | later triggered by Android reading friction |
| 2026-08-25 | REJECT | short-lived quotas / install-sync steps | not applicable | stale operational facts; no durable mechanism | — | — | none |
| 2026-08-27 | AUTO-CAPTURE | single-consumer request-body handoff | Fact + Inference | reusable mechanism + boundary + project validation | `mental-models/core-models.md` | `e8d4c305` | none recorded |

## Runtime Logging Rule

未来正式 Gate 事件追加一行即可。

- AUTO-CAPTURE：记录 target + commit + status。
- CANDIDATE：记录 evidence gap / trigger condition。
- REJECT：不创建知识节点，但允许记录为什么拒绝。
- 同一对话多个事件可批量写，避免每行单独 commit。
- ordinary chat 不进入 Gate，因此不记录。

## Friction Counters

发生时在 Notes / Correction 中记录：

- `duplicate-miss`
- `retrieval-miss`
- `trigger-miss`
- `capture-correction`
- `stale-sha`
- `merge-conflict`
- `safety-block`
- `human-sync-friction`

当前不追求复杂统计。等真实样本积累后再从本 log + Git history 做 Review。
