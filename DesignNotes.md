# DesignNotes.md

> Project：我要当村长
> Version：v1.0
> Last Update：2026-07-09

------

# 设计说明

本文档用于记录《我要当村长》项目中的**重要设计思想、技术决策、未来演进方向和重构计划**。

它不记录开发进度，也不记录具体 Task，而是回答：

> **为什么这样设计？未来准备如何演进？**

所有重大设计调整均追加记录，不删除历史记录。

------

# 一、总体设计理念

项目采用 **渐进式架构（Evolutionary Architecture）**。

核心原则：

> **先建立稳定框架，再逐步提高模拟深度。**

不会为了未来可能出现的需求提前增加大量复杂架构。

例如：

- 当前 Crop 使用 SchedulerTask 驱动成长。
- 当成长系统变复杂时，再演进为 GrowthProgress。
- 当不同植物行为差异明显时，再引入 Behavior。

------

# 二、Simulation 的核心思想

整个游戏世界由多个 System 共同驱动。

```text
Time
    │
    ▼
Scheduler
    │
    ▼
System
    │
    ▼
Entity
```

时间负责推进。

Scheduler 负责调度。

System 负责业务。

Entity 保存状态。

每层职责保持单一。

------

# 三、Entity 与 Config 分离

## 当前方案

运行时对象：

```text
Crop
```

静态数据：

```text
CropConfig
```

两者通过：

```text
ConfigId
```

建立关联。

例如：

```text
Crop
│
├── EntityId
├── ConfigId
├── GrowthStage
└── CropTime
CropConfig
│
├── Name
├── HarvestMinute
├── GrowIntervalMinute
└── WaterIntervalMinute
```

------

## 为什么这样设计？

原因：

配置是共享数据。

Entity 是运行时数据。

例如：

1000 棵水稻。

它们共享：

```text
CropConfig
```

而不是复制：

```text
HarvestMinute

GrowIntervalMinute

...
```

减少内存占用。

同时方便热更新配置。

------

# 四、为什么 Scheduler 不知道 Crop？

Scheduler 属于基础设施层。

它只负责：

```text
什么时候执行
```

而不是：

```text
执行谁
```

例如：

Scheduler 不应该出现：

```text
Crop

NPC

Animal

Tree
```

这样：

Scheduler 可以被所有业务复用。

------

# 五、Scheduler 当前设计

目前：

```text
WaterTask

GrowTask

HarvestTask
```

全部注册到 Scheduler。

优点：

- 实现简单
- 易于验证框架
- 足够支撑第一阶段开发

不足：

Harvest 使用固定时间。

未来：

改为：

```text
GrowthProgress
        │
        ▼
达到成熟条件
        │
        ▼
Harvest
```

这样：

天气、肥料、技能等均可影响成长速度。

------

# 六、WaitRemove 设计

所有长期存在对象：

统一采用：

```text
WaitRemove
```

而不是：

```text
Dictionary.Remove()
```

原因：

避免遍历过程中修改集合。

当前已经应用：

- SchedulerTask

未来计划应用：

- Entity
- NPC
- Animal
- Building
- Item

统一生命周期管理。

------

# 七、为什么不用单例？

当前采用依赖注入。

例如：

```text
CropSystem

↓

EntityManager

↓

SchedulerSystem

↓

TimeSystem
```

由初始化阶段建立依赖。

优点：

- 降低耦合
- 易于测试
- 后期可替换实现
- 更符合大型项目结构

------

# 八、为什么优先组合？

组合：

```text
Crop
    │
    ├── ConfigId
    ├── CropTime
    └── （未来）Behavior
```

继承：

```text
Crop : Entity
```

只有：

真正的 **Is-A** 关系。

例如：

Crop 是 Entity。

NPC 是 Entity。

Tree 是 Entity。

而：

Crop 不是 Config。

Crop 不是 Scheduler。

因此：

统一采用组合。

------

# 九、Behavior 演进计划

当前：

所有 Crop 行为：

由：

```text
CropSystem
```

负责。

未来：

引入：

```text
ICropBehavior
```

例如：

```text
RiceBehavior

AppleTreeBehavior

CornBehavior

CactusBehavior
```

Crop 保存：

```text
Behavior
```

而不是继承各种行为。

这样：

新增植物：

只增加新的 Behavior。

无需修改 CropSystem。

------

# 十、GrowthProgress 演进计划

当前：

```text
HarvestMinute
```

固定时间收获。

未来：

```text
GrowthProgress
```

例如：

```text
0%

↓

20%

↓

45%

↓

70%

↓

100%
```

成长速度：

实时计算：

```text
天气

土壤

肥料

NPC照料

技能

Buff
```

Harvest：

不再由 Scheduler 决定。

而由：

```text
GrowthProgress >= 100%
```

决定。

------

# 十一、SaveSystem 规划

SaveSystem 将负责：

统一管理所有实现：

```text
ISaveable
```

的系统。

目标：

```text
SaveSystem
        │
        ├── TimeSystem
        ├── SchedulerSystem
        ├── CropSystem
        ├── NPCSystem
        └── ...
```

由 SaveSystem 完成：

- Save
- Load

业务系统无需互相调用保存逻辑。

------

# 十二、AI 规划

NPC 行为：

第一阶段：

规则驱动。

第二阶段：

Utility AI（优先）。

GOAP：

保留评估。

长期目标：

支持本地 AI。

NPC 能够：

- 工作
- 社交
- 吃饭
- 睡觉
- 自主安排一天生活

------

# 十三、长期优化计划

以下内容属于后期优化，不进入当前开发阶段。

## Scheduler

- 时间轮（Time Wheel）
- 优先队列（Priority Queue）
- Tick 优化

------

## Entity

- 对象池
- 延迟销毁
- Chunk 管理（视规模决定）

------

## Config

- Luban 自动生成
- Excel 导入
- 热更新

------

## Save

- 增量存档
- 自动存档
- 多存档槽

------

## Performance

- Unity Job System
- Burst
- ECS（仅评估，不预设采用）

------

# 十四、当前明确不做的事情

为了保证项目推进效率，以下内容暂不引入：

- 复杂行为树
- ECS 重构
- 网络同步
- 多人联机
- 大规模插件框架
- 过早拆分大量接口
- 为未来需求设计大量空类

遵循原则：

> **当前需求解决当前问题。**

------

# 十五、设计原则总结

项目长期坚持以下原则：

- 单一职责（Single Responsibility）
- 优先组合，谨慎继承
- 数据驱动（Config）
- Entity 与 Config 分离
- System 与业务解耦
- 基础设施不依赖业务
- 渐进式重构
- 避免过度设计

当系统复杂度真正增加时，再通过重构演进，而不是提前引入复杂架构。

------

# 十六、维护规则

本文档仅记录：

- 为什么这样设计。
- 为什么做出某项技术决策。
- 已确定的未来演进方向。
- 需要长期关注的架构规划。

不记录：

- Task 完成情况。
- 每日开发日志。
- 普通 Bug 修复。

所有新的设计讨论，以追加的方式记录，保留历史决策，方便后续回顾整个项目的架构演进过程。