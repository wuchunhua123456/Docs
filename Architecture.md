# Architecture.md

> Project：我要当村长
> Engine：Unity 6.3 LTS（6000.3 LTS）
> Architecture Version：v1.0
> Last Update：2026-07-09

------

# 一、项目目标

《我要当村长》是一款以**村庄生态模拟**为核心的单机模拟经营游戏。

玩家扮演村长，不直接操控每一个 NPC，而是通过规划、建设、资源分配和制定策略影响整个村庄的发展。

整个游戏世界应具备自主运行能力，即使玩家不进行任何操作，村民、动物、植物和环境也会按照各自规则持续演化。

------

# 二、总体架构

项目采用 **Manager → System → Entity** 三层架构。

```text
GameStart
        │
        ▼
GameSystemManager
        │
        ├───────────────┬───────────────┬───────────────┐
        ▼               ▼               ▼
 TimeSystem     SchedulerSystem     CropSystem ...
                                        │
                                        ▼
                                 EntityManager
                                        │
                                        ▼
                                      Entity
                                        │
                        ┌───────────────┴───────────────┐
                        ▼                               ▼
                      Crop                            NPC
```

原则：

- GameStart 负责启动。
- Manager 管理生命周期。
- System 负责业务逻辑。
- Entity 保存运行时数据。
- Config 保存静态数据。

------

# 三、当前目录结构

```text
Assets
└── Scripts
    ├── AI
    ├── Config
    ├── Core
    │   ├── Event
    │   ├── GameLog
    │   └── GameTime
    │
    ├── Editor
    ├── Framework
	│   ├── GameSystemManager
    │   ├── ISystem
    │   └── Save
    │       ├── ISaveable
    │       ├── SaveSystem
    │       ├── SaveData
    │       └── SaveVersion
    │
    ├── Launcher
    │   └── GameStart
    │
    ├── NPC
    │
    ├── Simulation
    │   ├── Config
    │   │   ├── CropConfig
    │   │   └── CropConfigManager
    │   │
    │   ├── Crop
    │   │   ├── Crop
    │   │   └── CropSystem
    │   │
    │   ├── Entity
    │   │   ├── Entity
    │   │   └── EntityManager
    │   │
    │   ├── Scheduler
    │   │   ├── SchedulerSystem
    │   │   ├── SchedulerTask
    │   │   └── SchedulerTaskData
    │   │
    │   ├── Time
    │   │   └── TimeSystem
    │   │
    │   ├── Animal（预留）
    │   ├── Building（预留）
    │   ├── Item（预留）
    │   ├── Season（预留）
    │   ├── Tree（预留）
    │   ├── Weather（预留）
    │   └── World（预留）
    │
    ├── Tools
    ├── UI
    └── World
```

目录遵循：

> **一个业务模块对应一个 System，一个模块一个文件夹。**

------

# 四、Manager

## GameStart

负责：

- 创建 Manager
- 创建 System
- 初始化 Config
- 启动游戏

禁止：

- 编写业务逻辑
- 保存运行时数据

------

## GameSystemManager

负责：

- Register()
- GetSystem()
- Update()

不负责：

- Crop
- NPC
- Tree
- Save
- UI

只负责生命周期管理。

------

- ## EntityManager

  负责：

  EntityId 分配。

  Register。

  Remove。

  Get。

  Contains。

  不负责：

  Entity 行为。

  Scheduler。

  Config。

  Save。

  EntityManager 当前属于 Simulation 基础管理类。

  不实现：

  ISystem。

  ISaveable。

------

## ConfigManager（各业务模块）

例如：

- CropConfigManager
- NPCConfigManager
- AnimalConfigManager

负责：

- Init
- Register
- Get
- Contains

不负责：

- 创建 Entity
- 创建 Task
- 保存运行时数据

------

# 五、System

一个 System 负责一种业务。

例如：

```
CropSystem
```

负责：

- 创建 Crop。
- 删除 Crop。
- Crop 生命周期。
- Crop 数据恢复。

例如：

```
SchedulerSystem
```

负责：

- SchedulerTask 生命周期。
- 时间调度。

例如：

```
TimeSystem
```

负责：

- 游戏时间。
- 分钟事件。
- 小时事件。
- 日期推进。

原则：

System 可以依赖其它 System。

System 可以通过 ISaveable 接入 SaveSystem。

SaveSystem 不属于业务 System，只负责统一存档生命周期管理。

禁止：

System 保存其它 System 的运行时数据。

------

# 六、Entity

Entity 保存运行时状态。

例如：

```text
EntityId
ConfigId
Position
HP
GrowthStage
TaskId
```

Entity 不负责：

- Scheduler
- Config
- Save
- UI

Entity 永远不知道 System。

------

# 七、Config

Config 保存静态配置。

例如：

```text
Name
MoveSpeed
GrowIntervalMinute
WaterIntervalMinute
HarvestMinute
MaxGrowthStage
```

Config 不保存：

```text
CurrentHP
GrowthStage
TaskId
CurrentWater
```

运行时永远修改 Entity。

绝不修改 Config。

------

# 八、Scheduler

Scheduler 是整个 Simulation 的时间调度中心。

负责：

- Once Task
- Loop Task
- Pause
- Resume
- Remove

Scheduler 不负责：

- Crop
- NPC
- Animal
- Tree

Scheduler 只知道：

> **什么时候执行。**

不知道：

> **执行什么业务。**

------

## Once

执行一次。

执行完成自动删除。

------

## Loop

循环执行。

由业务主动终止。

例如：

Harvest：

```text
Harvest

↓

Remove GrowTask

↓

Remove WaterTask
```

------

## 删除原则

遍历过程中禁止直接 Remove。

统一：

```text
WaitRemove

↓

本轮遍历结束

↓

统一删除
```

适用于：

- SchedulerTask
- Entity
- NPC
- Animal

------

# 九、Entity 与 Config

每个 Entity 保存：

```text
EntityId
ConfigId
```

Config 通过 ConfigId 查询。

```text
Crop
    │
ConfigId
    │
    ▼
CropConfigManager
    │
    ▼
CropConfig
```

Entity 不缓存 Config 对象。

------

# 十、Crop 当前设计

Crop 保存：

```text
EntityId
ConfigId
GrowthStage
CropTime

GrowTaskId
WaterTaskId
HarvestTaskId
```

Crop 不保存：

```text
GrowIntervalMinute
WaterIntervalMinute
HarvestMinute
```

全部来自 Config。

------

# 十一、代码设计原则

## 单一职责

一个类只负责一个主要职责。

------

## 优先组合，谨慎继承

优先：

Has-A

例如：

```text
Crop
    │
    ├── ConfigId
    ├── CropTime
    └── （未来）Behavior
```

谨慎：

Is-A

合理：

```text
Crop : Entity
```

不合理：

```text
Crop : CropConfig
```

------

## 依赖方向

允许：

```text
CropSystem
    │
    ├── SchedulerSystem
    ├── TimeSystem
    ├── EntityManager
    └── CropConfigManager
```

禁止：

```text
Crop
    │
    ▼
SchedulerSystem
```

Entity 不依赖 System。

------

## Callback 原则

Scheduler 回调中尽量只调用业务方法。

推荐：

```text
HarvestTask

↓

HarvestCrop(crop)
```

避免：

Lambda 内编写大量业务逻辑。

------

## 删除原则

长期存在对象统一采用：

```text
WaitRemove
```

而不是立即销毁。

保证遍历安全。

------

## 数据驱动

业务优先读取 Config。

避免：

```text
120
60
500
```

等硬编码数据。

------

## 可保存性

所有长期存在的数据最终都应支持 Save。

包括：

- Entity
- NPC
- Animal
- Building
- SchedulerTask

Config 不参与存档。

SaveSystem 位于 Framework 层。 业务模块通过 ISaveable 接入存档。 Framework 不保存具体业务数据。

------

# 十二、未来架构演进

当前：

```text
Scheduler
    │
    ▼
HarvestTask
```

计划演进：

```text
GrowthProgress

↓

Grow()

↓

达到成熟条件

↓

Harvest()
```

最终支持：

- 天气
- 季节
- 土壤
- 肥料
- NPC 照料
- Buff / Debuff

成长速度动态计算。

------

# 十三、文档维护规范

项目仅维护以下文档：

```text
Docs
│
├── Architecture.md
├── ProjectProgress.md
├── DesignNotes.md
└── TaskLog/
```

维护规则：

- 架构变化 → 更新 Architecture.md
- Task 完成 → 更新 ProjectProgress.md，并新增对应 TaskLog
- 新的设计决策或未来规划 → 更新 DesignNotes.md

除以上文档外，不新增新的文档类型，保持文档体系简洁、长期可维护。