# AnyPS5：二进制重链接、系统兼容层与能力预检

**Domain:** Software Engineering / Binary Compatibility / Agent Workflow  
**Status:** reference（源码研究案例；非本人项目实测）  
**Mastery:** M2（已分析机制，未动手验证）  
**Epistemic Status:** Fact（仓库文档/代码可复核） + Inference（跨领域迁移） + Judgment（采用建议） + Unknown（未验证项）  
**As of:** 2026-10-10  
**Case:** [boykopovar/AnyPS5](https://github.com/boykopovar/AnyPS5)

## 核心结论

**Fact：** AnyPS5 试图将 PS5 的 ELF 可执行程序及其模块转换为宿主 OS 可加载的原生二进制，并以自建 PRX 兼容库提供游戏所需系统接口；在已阅读的实现中，图形路径通过 RDNA Shader 重编译生成 SPIR-V、使用 Vulkan。其方法不是完整模拟一台 PS5，也不代表无需进行系统行为适配。

**Inference：** 对源和目标共享 CPU ISA 的系统，`二进制重链接 + ABI/API 兼容 + 设备指令转换`可以避免完整 CPU 指令解释器的部分开销；但总体难点通常会转移到系统调用语义、内存/异常模型、GPU 和其他设备行为。

**Judgment：** 更值得跨项目借鉴的是“转换与运行分离、显式兼容层、执行前能力审计、未实现状态不伪装成功”，而非直接复用 PS5 专用代码。不要以 GitHub 热度或个别游戏帧率推断通用成熟度。

## 机制：从 PS5 程序到宿主运行

```text
合法获得的 PS5 ELF + 游戏自带模块
         ↓
Relinker：读取导入 NID、检查 syscall、修正重定位/动态链接、
          转换输出格式；部分 AMD 专用 CPU 指令可转换给 Intel
         ↓
Linux ELF / Windows PE；文档另列实验性 macOS x86-64 Mach-O 输出
         ↓
宿主 OS Loader 按 NID 加载/链接 AnyPS5 提供的 PRX 兼容库
         ↓
游戏 CPU 代码在兼容的 x86-64 CPU 上执行
         +
PS5 图形命令与 RDNA Shader → 解码 → 控制流 → IR → 优化
         → SPIR-V → Vulkan 图形/计算管线
```

### 分层职责

1. **Relinker / format patcher（Fact）**：重构导入和动态链接区，处理游戏模块，生成宿主 OS 所需文件格式。源码入口：[`core/relinker/main.cpp`](https://github.com/boykopovar/AnyPS5/blob/main/core/relinker/main.cpp)；架构见 [ARCHITECTURE.md](https://github.com/boykopovar/AnyPS5/blob/main/docs/dev/ARCHITECTURE.md)。
2. **PRX 兼容层（Fact）**：用 `core/libs/prx` 的宿主实现替代 PS5 系统库接口。NID 让导入函数与提供者对接；符号可被链接不代表函数行为完整。
3. **Shader Compiler（Fact）**：`RdnaDecoder → ControlFlow → Translation/IR → Optimization → SpirvBackend`；编译结果及 Vulkan Pipeline 可缓存，但正确复用需要覆盖影响结果的设备能力、Shader 状态及资源布局。源码：[`core/shader/recompiler`](https://github.com/boykopovar/AnyPS5/tree/main/core/shader/recompiler)。
4. **Runtime（Fact）**：依赖 SDL/Vulkan/宿主线程、内存和图形驱动，而非独立完整的虚拟 PS5。

### “No emulation”的准确边界

- **Fact：** 项目 README 自述“不进行模拟且没有单独的 runtime process”；其意义偏向不构建完整主机模拟器。
- **Inference：** 这不等于“PS5 的 GPU/API/设备行为可以直接在 PC 原样执行”。兼容库、Shader 重编译和图形命令解释/转换仍然承担等效行为适配工作。
- **Counterexample：** CPU 架构相同却仍可能因不同 ABI、内存模型、未实现系统服务或 Shader 指令而无法启动游戏；静态转换成功不等于兼容成功。

## 成熟度：区分静态分析、加载成功、功能正确与性能

**Fact（2026-10-10 仓库文档快照）：**

- [官方兼容表](https://github.com/boykopovar/AnyPS5/blob/main/docs/user/COMPATIBILITY.md)仅列一款经验证可玩的游戏 `Dreaming Sarah`：Windows 状态 `In game, playable`，Linux 为 `?`；GTX 1050 Ti/i5-7500 记录 60 FPS，Intel HD 620/i5-7200 记录 36 FPS。特定环境的个例，不能外推。
- [README](https://github.com/boykopovar/AnyPS5/blob/main/README.md)称未支持或未预期状态严格抛出 `std::runtime_error` 并终止；[TechnicalDebt.md](https://github.com/boykopovar/AnyPS5/blob/main/docs/dev/TechnicalDebt.md)披露部分 API 为 Silent Stub、部分设备/音频能力不完整。
- [USAGE.md](https://github.com/boykopovar/AnyPS5/blob/main/docs/user/USAGE.md)说明 `--macos` 可输出 x86-64 Mach-O；[BUILD.md](https://github.com/boykopovar/AnyPS5/blob/main/docs/dev/BUILD.md)同时明确独立 Relinker 可在 Apple Silicon 构建，但完整游戏运行不等于已支持。应将“转换格式能力”与“完整 macOS Runtime”分开。
- 项目许可证是 GPL-2.0-only（[LICENSE](https://github.com/boykopovar/AnyPS5/blob/main/LICENSE)）；代码复用前需独立评估许可证兼容性。

**Judgment：** 此项目目前适合底层编译/兼容性工程研究，不宜当成成熟通用 PS5 游戏平台；安卓 ARM64 平板亦不能直接使用当前面向 x86-64 的转换后游戏运行路径。

**Unknown：** 本记录没有对目标系统实施完整构建、游戏合法镜像加载、GPU 驱动或性能回归的 E2E 验证；未来兼容列表、macOS 支持、API 覆盖率和性能均可能变化，需重新核对上游仓库。

## 可迁移的工程模式：Capability Preflight

**Fact：** AnyPS5 的 [`tools/import_audit.py`](https://github.com/boykopovar/AnyPS5/blob/main/tools/import_audit.py)依据导入注册表和构建产物，将依赖按 `implemented`（库导出且非直接抛异常 Stub）、`stub`（导出但未实现）、`absent`（无导出）、`module`（由游戏自有模块提供）分类；[USAGE.md](https://github.com/boykopovar/AnyPS5/blob/main/docs/user/USAGE.md#import-audit)明确此为加载前分析。

**Inference（抽象工作流）：**

```text
输入任务与目标环境
→ 静态解析依赖 / 需求
→ Capability Inventory（版本、权限、API、设备、文件）
→ 分类：ready / partial-stub / missing / unknown
→ 阻断必需能力缺失；其他差异进入降级策略或人工决策
→ 执行
→ 收集真实结果；更新兼容判断
```

**重要限制：** `implemented` 不意味着语义完全正确，`no absent imports` 不意味着任务成功。能力预检是一道前置筛选，而非功能、性能或安全验收的替代品。

### 对 Agent / Android HostBridge 的启发（尚未实施）

**Inference：** 对“Mac 内容处理 → Android 执行”类工作流，可在执行前检查输入文件/媒体编码、目标设备 CPU/OS、存储、网络可达性、应用安装状态、所需权限与 UI/发布步骤能力；返回结构化 `ready / degraded / blocked / unknown`，而不是模糊地“应该能运行”。

**Judgment：** 不必引入新的服务或常驻进程；可以在现有执行器前增加一个轻量 preflight 函数。缺能力时应区分：
- 缺少硬性前提：fail closed，并报告具体缺失项；
- 可降级能力：显式选择受控 fallback；
- 临时外部故障：有限重试 + 幂等防重复；
- 不确定/高风险 UI 状态：进入可恢复 checkpoint 或人工确认。

与 AnyPS5 直接抛异常终止相比，无人值守 Agent 往往需要**结构化错误、状态持久化、重试/恢复、人工接管**。此处是设计类比，**不是** HostBridge 已完成集成的证据。

## 使用时的决策检查

1. **同 ISA？** 是则考虑二进制重链接；跨 ISA（如 x86-64 → ARM64）不能假设原生执行。
2. **API/ABI 语义可替代？** 如果系统调用、内存、异常、设备行为差异过大，直接移植可能比兼容层更合适。
3. **图形/专用指令可转换？** Shader IR、驱动能力、资源语义和缓存键必须正确。
4. **兼容性证据是什么级别？** 分开记录转换、装载、进入主界面、可玩、稳定、性能；不越级宣称。
5. **需要运行时修复还是前置转换？** 稳定、确定性的变换可以前移和缓存；依赖动态状态的兼容行为仍须 runtime 处理。
6. **失败如何暴露？** 不支持功能优先显式报错；如果使用 Stub，必须被标识且有可观测边界。

## 连接与验证边界

- **连接：** [Cloud-to-Local Capability Bridge Patterns](cloud-to-local-capability-bridge-patterns.md)（目标环境能力与权限边界）；[Agent Skill Composition / Context-Budget Model](core-models.md#11-agent-skill-composition--context-budget-model)（薄编排、明确定义成功条件）。
- **本次证据层级：** 上游 README、ARCHITECTURE、USAGE、BUILD、COMPATIBILITY、TechnicalDebt 与源码的静态阅读；未执行代码，未形成本人 E2E 项目验证。
- **后续验证触发：** 只有真实 Agent 执行时出现“已规划但运行环境缺能力”的故障，才比较增设 preflight 前后的失败定位成本及误报/漏报；不为这份笔记启动额外开发工作。
