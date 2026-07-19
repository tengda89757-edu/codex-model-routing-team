# 路由策略

## 是否创建后台任务

出现下列信号时评估并行；单个信号不强制触发：

- 至少 2 条互不依赖的工作流。
- 输入可按来源、章节、模块或主题拆分。
- 独立验证能降低主 Agent 的自证偏差。
- 任务跨调研、写作、编码、设计、测试或审查多个领域。
- 预计节省的时间或质量增益明显高于协调成本。

典型任务创建 2–3 个 Worker；广泛调研或多模块任务创建 4–6 个。简单问答、状态查询、单文件小改、强顺序流程直接由主 Agent 完成。

## 模型与推理强度

本表是 Worker 模型选择的唯一事实源。`model` 必须使用精确 ID，不能用 Codex App 工具描述中的示例模型替换。

| 路由名 | `model` | `thinking` | 适用工作 |
| --- | --- | --- | --- |
| Luna High | `gpt-5.6-luna` | `high` | 机械提取、格式整理、分类、简单验证 |
| Luna Max | `gpt-5.6-luna` | `max` | 边界清晰、难度高、时效不敏感的深度执行 |
| Terra Max | `gpt-5.6-terra` | `max` | 默认 Worker；调研、初稿、方案扩展、常规编码与审查 |
| Sol X High | `gpt-5.6-sol` | `xhigh` | 非默认的关键审查与方案裁决；只在明确时延约束下使用 |
| Sol Max | `gpt-5.6-sol` | `max` | 默认关键任务与升级路由；高歧义规划、架构、困难调试、高风险判断和关键审查 |

默认路由组合是 `gpt-5.6-terra / max` 与 `gpt-5.6-sol / max`。`gpt-5.6-luna` 仅保留 Luna High 和 Luna Max 两个专项路由，不再作为默认 Worker；禁止继续使用已被替换的 `gpt-5.6-luna / xhigh` 与 `gpt-5.6-sol / high`。禁止因工具元数据遗漏策略选定模型而改用旧模型；运行时拒绝 Terra、Sol 或专项 Luna 时停止并报告冲突。

Ultra 永久禁止，因为它会引入不可控的下级 Agent 扩张。不得把成本比例写成未经验证的固定事实。

## 数量与失败升级

- 并发上限 6；累计创建上限 8，替换任务也计数。
- 创建前计算 `planned_workers + reserved_slots <= 8`。reserved slots 用于上游后续阶段和失败恢复，不能被前期并行任务占用。
- Deep Research 默认预算为 `2-4 researcher + 1 verifier + 1 reviewer + 2 retry reserve`。需要 5-6 个 researcher 时必须显式减少重试预留，禁止挤掉验证阶段。
- 同一子任务先追问一次；Terra 或专项 Luna 任务最多再切换一次到 Sol Max。Sol Max 任务失败后由主 Agent 接管；只有上游明确要求独立复审时才可使用 retry reserve 新建一次 Sol Max。
- 同一子任务最多两次执行机会，禁止无条件重复创建。
- 主 Agent 可自由组合默认的 Terra Max、Sol Max 与专项 Luna，不设置僵硬配额。

## 工作区与冲突

- 默认把可写任务按互斥文件或目录分配到同一项目的 local 环境。
- 只要任务包声明工作区输出路径，就必须使用匹配 project local；主题通用不构成 projectless 理由。
- projectless 只用于纯聊天交付且没有任何工作区产物的任务。
- 共享同一文件时实行单写者规则，其他 Worker 只提供建议或补丁说明。
- 跨模块且有合并风险的工程任务可使用 worktree；主 Agent 负责比较、移植和验证。
- 无法确认项目、起始状态或合并路径时，留在主任务执行。
