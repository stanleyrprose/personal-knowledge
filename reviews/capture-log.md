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
| 2026-09-01 | AUTO-CAPTURE | operational observability output minimization | Fact + Inference | reusable security mechanism + output-surface boundary + real CI/systemd project validation | `mental-models/operational-observability-output-minimization.md` | `000877b8` | none |

## Runtime Logging Rule

未来正式 Gate 事件追加一行即可。

- AUTO-CAPTURE：记录 target + commit + status。
- CANDIDATE：记录 evidence gap / trigger condition。
- REJECT：不创建知识节点，但允许记录为什么拒绝。
- 如果 REJECT 是因为 visibility / sensitive-data / secret safety gate，只写去敏类别（例如 `sensitive-content-blocked`），**不得把被阻断的原始敏感内容复制进本 log**。
- 同一对话多个事件可批量写，避免每行单独 commit。
- ordinary chat 不进入 Gate，因此不记录。

## What This Log Can and Cannot Measure

可以帮助观察：

- Gate outcome distribution
- duplicate / correction / conflict / trigger events
- non-meta vs meta capture mix
- 已进入 Gate 的 precision proxy

不能单独测量：

- **False negatives / recall**：如果一个重要主题根本没有触发 Capture Gate，它不会出现在这里。

因此 recall 只能在后续 Review 中通过**抽样回看真实对话**估计，例如：随机选一组事后确认重要的观点，检查当时是否触发、是否写入、是否正确定位。

## Friction Counters

发生时在 Correction / notes 中记录：

- `duplicate-miss`
- `retrieval-miss`
- `trigger-miss`
- `capture-correction`
- `stale-sha`
- `merge-conflict`
- `safety-block`
- `human-sync-friction`

当前不追求复杂统计。等真实样本积累后再从本 log + Git history + 抽样对话做 Review。
