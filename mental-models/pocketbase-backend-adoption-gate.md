# PocketBase / Backend-as-a-Box Adoption Gate

**Status:** INSTANTIATED MODEL  
**Epistemic Status:** Fact（当前项目/代码观察） + Inference（跨项目泛化） + Judgment（采用规则）  
**As of:** 2026-09-13  
**Evidence:** PocketBase `master` 的 `README.md`、`core/db_connect.go`、`apis/realtime.go`、`examples/base/main.go`；SignalForge 当前 `GOAL.md`、`db.py`、`engine.py`、`provider_queue.py`、`opportunities.py`。

## Core judgment

不要因为一个系统使用 SQLite，就把 PocketBase 视为“更高级的 SQLite”而默认升级。

更可靠的判断是：

`Persistence need → plain SQLite first`

`Product/Operator-plane need → evaluate PocketBase`

PocketBase 的主要增量价值来自 **Auth、REST-ish API、Admin UI、Realtime、File/User management**，而不是把已经成熟的本地业务状态机自动简化掉。

## Why it matters

Backend-as-a-Box 很容易制造一种错误的升级直觉：既然它同时提供数据库、API、Auth 和管理 UI，就应该替换项目里现有的 SQLite。现实中，系统复杂度可能主要存在于 domain semantics，而不是 CRUD。

如果核心复杂度已经是：

`acquisition → evidence → canonicalization → semantic change detection → queue/lease/idempotency → qualification → delivery`

那么把 `sqlite3` 换成 PocketBase API/SDK，并不会消除这些规则，反而可能增加新的 process、HTTP boundary、schema migration 和 failure modes。

## Adoption Gate

### Keep plain SQLite when

以下特征占主导时，默认继续使用 SQLite：

- 单机 / embedded runtime；
- 主要需求是本地持久化和事务；
- domain logic 明显重于 CRUD；
- 有自定义 atomic state transition、lease、idempotency、queue recovery；
- 没有真实的多用户 Auth / RBAC 需求；
- 没有 Web Admin / Review Console；
- 没有其他 App 需要稳定 remote CRUD API；
- 不需要 realtime human-facing dashboard。

### Re-evaluate PocketBase when

以下需求中出现 **2–3 个以上且已成为真实 friction** 时，进入 PocketBase evaluation，而不是自动采用：

1. 多用户访问；
2. Auth / RBAC；
3. Web Operator / Review Console；
4. 人工 annotation / approve / reject workflow；
5. 其他 App 需要 remote CRUD API；
6. realtime dashboard / subscription；
7. 文件/用户管理需要统一 backend surface。

**Judgment:** “2–3 个”只是触发重新评估的 heuristic，不是硬阈值。单个高价值需求也可能足以触发；多个很弱的需求也未必值得增加基础设施。

## Architecture boundary

推荐分层：

`Processing Plane = domain runtime + SQLite`

`Operator Plane = PocketBase candidate, only when Gate is triggered`

如果 PocketBase 被引入 Operator Plane，不应未经设计就成为第二个 canonical business state。必须先明确：

- 哪一侧拥有 canonical truth；
- 是 read-model projection、review metadata，还是 authoritative write；
- 同步失败如何恢复；
- 是否产生 dual-write consistency risk。

## SignalForge project validation

**Fact:** SignalForge 当前已经把 SQLite 用作成熟业务状态层，并包含 source state、discovery、canonical items、signals、scheduler runs、acquisition/evidence/processing lifecycle、provider queue、delivery receipts 等状态；Provider Queue 还有 `BEGIN IMMEDIATE`、claim token、lease expiry、retry/idempotency 语义。

**Inference:** 对这种 domain-heavy pipeline，PocketBase 替换 SQLite 不会显著删除 acquisition、evidence、canonical/signal semantics、provider queue、qualification、Telegram/translation 等核心复杂度；若通过 REST/SDK 访问，还会增加 runtime 和 network boundary。

**Judgment:** SignalForge 当前不引入 PocketBase，不迁移核心 SQLite。未来若出现真实的 Web Review Console、多用户、Auth/RBAC、人工 annotation、remote API 或 realtime dashboard 需求，再把 PocketBase作为 **Operator/Review Plane candidate** 重新评估。

## Boundaries / failure conditions

- 如果系统本来就需要完整 Web backend，PocketBase 可能比自己搭 FastAPI/Auth/Admin/Realtime 明显更简单。
- 如果需要多节点水平扩展、active-active、多 region 或强 HA，PocketBase 的单机/SQLite取向本身可能不合适，应优先评估 PostgreSQL/Supabase 或更适合的 distributed architecture。
- 如果 operator plane 只是一个静态/只读页面，直接生成 HTML 或现有 CLI/API 可能仍比 PocketBase 更简单。
- 如果 domain logic 很薄、CRUD/Auth 占开发量大头，则本模型的“保持 SQLite”默认会失效，PocketBase 的净收益会上升。

## Decision shortcut

先问两个问题：

1. **复杂度主要来自数据存储，还是来自业务语义？**
2. **我们需要的是 database，还是一个带 Auth/API/Admin/Realtime 的产品 backend？**

如果答案是“业务语义 + database”，优先保持 SQLite。  
如果答案开始变成“多用户产品 backend”，再进入 PocketBase Gate。
