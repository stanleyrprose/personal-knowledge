# Durability Follows Ownership and Lifecycle

**Status:** INSTANTIATED MODEL  
**Epistemic Status:** Judgment + Inference  
**Evidence:** 2026-09-04 Mac-centric Browser Execution Plane architecture decision; not yet production-validated as a general rule.

长期运行系统不能仅凭“当前 7×24 在线”判断某个节点是否适合作为 durable runtime。更可靠的判断是同时看：**ownership、lifecycle、replaceability、state recoverability 和 failure consequence**。

核心原则：

`Durability should follow ownership and lifecycle, not uptime appearance.`

## Why it matters

租赁 VPS、临时 cloud instance、spot/preemptible compute、CI runner 等节点可以长时间在线，但仍可能因为付费、供应商策略、实例回收、重建或人为释放而永久消失。若把这类节点误当成 durable infrastructure，就容易把 canonical state、scheduler、router、identity 或唯一 runtime 锁在一个实际上 disposable 的 failure domain 上。

## Mechanism

判断一个节点是否适合承担 durable responsibility，应区分：

- **Availability:** 现在是否在线；
- **Ownership / Control:** 是否长期由系统拥有和控制；
- **Lifecycle:** 是否可能被外部条件销毁或替换；
- **Recoverability:** 节点丢失后状态能否从其他事实源恢复；
- **Replaceability:** 替换节点是否等同于换一个 capability provider，而不是迁移整个系统；
- **Failure consequence:** 节点消失是 capability degradation，还是 system failure。

因此，一个节点即使持续在线，也不应因为 uptime 高就自动承担 durable identity。

## How to use

设计 runtime placement 时先问：

1. 这个节点明天永久消失，系统会丢什么？
2. 丢失的是“能力”还是“系统身份/事实状态”？
3. 是否能从 Git、数据库、backup 或其他 canonical source 重建？
4. 更换 provider / region / IP 时，上层 workflow 是否需要修改？
5. 能否把节点抽象成 capability identity，而不是 machine identity？

理想结果是：

`Disposable node loss → capability degradation, not system migration.`

## Example architecture pattern

对于区域网络出口，可把 VPS 定义为：

`china-egress / southeast-asia-egress`

而不是：

`server-A / server-B / fixed-IP`

上层 runtime 依赖区域网络能力，底层 VPS 可以被替换。长期 router、browser profile、canonical state 则放在生命周期和控制权更稳定的节点或 durable store。

## Preconditions

- 系统可以明确区分 durable state 与 replaceable compute/network capability；
- 至少存在一个可靠 canonical source / backup；
- replacement cost 可接受；
- routing/configuration 不把业务逻辑硬绑定到机器身份。

## Boundaries / Failure Conditions

- 自有硬件也不等于高可用；它仍可能断电、磁盘损坏或网络中断。该模型判断的是 durable placement，不等同于 SLA/HA 设计。
- 租赁云资源也可以成为 durable infrastructure，如果其生命周期、持久卷、自动重建、备份和控制面被明确工程化；“租赁”本身不是否决条件。
- 若业务要求高可用，单个长期自有节点仍可能不足，需要第二 runtime host 或 managed service。
- 某些 stateful managed services 虽不由用户拥有硬件，但其服务 contract 和持久化语义可能比本地节点更 durable；因此 ownership 只是变量之一，不应机械等同于物理所有权。

## Counterexample

一个临时 VPS 若只承担 SOCKS/HTTP egress，即使被 destroy 也只损失区域网络路径，此时它非常适合作为 disposable edge。相反，如果唯一 scheduler、browser profile 和 queue 只存在该 VPS，本质上就把 disposable compute 错当成 durable control plane。

## Decision heuristic

优先把系统职责分成：

- **Durable control/state:** 放在可恢复、生命周期稳定的节点或 store；
- **Disposable capability:** 尽量无状态、可替换、可重新注册；
- **Failure semantics:** 让 disposable 节点故障表现为 degradation，而不是 total failure。

## Open questions

- 在真实生产运行中，哪类能力最容易被错误提升为 durable responsibility？
- 何时 second durable host 的 HA 收益开始超过复杂度成本？
- capability identity 的注册、健康状态和 replacement 流程需要多自动化才合理？