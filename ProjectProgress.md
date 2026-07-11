# ProjectProgress.md

> Project：我要当村长
> Version：v0.1.0-alpha
> Last Update：2026-07-09

------

# 一、项目当前状态

**开发阶段：Phase 1 - Simulation Framework**

目前目标不是开发游戏内容，而是建立整个模拟世界运行所需的基础框架。

当前框架已经能够支持：

- 游戏时间推进
- 定时任务调度
- Entity 管理
- Config 数据驱动
- Crop 生命周期模拟

后续所有系统（NPC、Animal、Tree、Building 等）都将在此框架基础上扩展。

------

# 二、开发路线图（RoadMap）

## Phase 1：Simulation Framework（进行中） 🟡

### Framework

状态：✅ 完成

完成内容：

- GameSystemManager
- ISystem
- ISaveable
- System 注册与获取

------

### Core

状态：✅ 完成

完成内容：

- EventSystem
- GameLog
- GameTime

------

### Time

状态：✅ 完成

完成内容：

- TimeSystem
- 游戏时间推进
- 分钟事件
- 小时事件（预留）
- 日期推进（基础）

------

### Scheduler

状态：✅ 完成

完成内容：

- SchedulerSystem
- Once Task
- Loop Task
- Pause / Resume
- WaitRemove 删除机制
- SchedulerTaskData

------

### Entity

状态：✅ 完成

完成内容：

- Entity
- EntityManager
- EntityId 分配
- Register
- Remove
- 查询接口

------

### Config

状态：🟢 第一阶段完成

完成内容：

- CropConfig
- CropConfigManager
- ConfigId
- Config 数据驱动

后续规划：

- Luban 自动生成配置
- Excel 导入
- 热更新配置

------

### Crop

状态：🟡 基础完成

完成内容：

- Crop Entity
- CropSystem
- 创建 Crop
- WaterTask
- GrowTask
- HarvestTask
- Config 驱动成长参数

当前实现已验证：

- Scheduler 与业务解耦
- Entity 与 Config 分离
- 多个 Task 协同工作

后续优化：

- GrowthProgress
- 动态成长速度
- 天气影响
- 土壤影响
- 肥料影响

------

### Save

状态：⚪ 未开始

计划：

- SaveSystem
- SaveManager
- SaveData
- 自动存档
- 加载恢复 Scheduler

------

## Phase 2：Simulation World

状态：⚪ 未开始

计划完成：

- TreeSystem
- AnimalSystem
- NPCSystem
- BuildingSystem
- ItemSystem

目标：

世界中的所有实体均能够：

- 创建
- 删除
- 保存
- 加载
- 自主运行

------

## Phase 3：World Simulation

状态：⚪ 未开始

计划：

- Weather
- Season
- DayNight
- Terrain
- WorldEvent

目标：

环境开始影响整个模拟世界。

------

## Phase 4：AI

状态：⚪ 未开始

计划：

NPC 自主决策系统。

候选方案：

- Utility AI
- GOAP（待评估）

后期目标：

支持本地 AI 驱动。

------

## Phase 5：Gameplay

状态：⚪ 未开始

计划：

- 建造
- 种植
- 采集
- 贸易
- 探索
- 防御

逐步形成完整玩法循环。

------

## Phase 6：Polish

状态：⚪ 未开始

包括：

- 性能优化
- 动画
- 特效
- 音效
- 平衡性调整
- Bug 修复

------

# 三、Task 完成情况

| Task            | 状态 | 说明                                                         |
| --------------- | ---- | ------------------------------------------------------------ |
| Task01 ~ Task17 | ✅    | Framework、Core、Time、Scheduler 等基础系统完成              |
| Task18          | ✅    | Entity 基础设计                                              |
| Task19          | ✅    | EntityManager 建立                                           |
| Task20          | ✅    | Crop 与 Entity 体系接入                                      |
| Task21          | ✅    | Crop 生命周期设计                                            |
| Task22          | ✅    | CropSystem 基础搭建                                          |
| Task23          | ✅    | CropConfig 与 ConfigManager 接入，实现 Config 驱动 Crop 创建 |

> 详细实现记录请查看 `Docs/TaskLog`。

------

# 四、当前系统关系

```text
GameStart
        │
        ▼
GameSystemManager
        │
        ├── TimeSystem
        ├── SchedulerSystem
        └── CropSystem
                │
                ├── EntityManager
                ├── CropConfigManager
                └── Crop
```

目前已经形成完整的数据流：

```text
CropConfig
        │
        ▼
CropSystem.CreateCrop()
        │
        ▼
Crop(Entity)
        │
        ▼
Scheduler 注册成长任务
        │
        ▼
时间驱动作物生命周期
```

------

# 五、当前实现能力

目前框架已经能够支持：

### 时间系统

- 游戏时间推进
- 时间事件通知

------

### 调度系统

- 定时执行
- 循环执行
- 暂停
- 恢复
- 安全删除

------

### Entity

- 创建
- 注册
- 查询
- 删除

------

### Config

- 查询配置
- 数据驱动创建对象

------

### Crop

支持完整生命周期：

```text
创建
    │
    ▼
浇水
    │
    ▼
成长
    │
    ▼
收获
    │
    ▼
结束全部任务
```

------

# 六、当前存在的已知限制

目前属于框架验证阶段，因此存在以下限制：

- 作物成长仍依赖固定时间。
- Scheduler 回调中仍包含少量业务逻辑。
- Harvest 采用定时触发，而非成长值判断。
- Config 暂为手写数据。
- SaveSystem 尚未实现。

以上属于预期设计，并非缺陷。

------

# 七、下一阶段目标

**Task24：SaveSystem（计划）**

目标：

建立基础存档框架。

预计包括：

- SaveSystem
- SaveManager
- SaveData
- ISaveable 管理
- 系统级存档入口

完成后，将能够保存并恢复整个 Simulation 世界状态，为后续 NPC、Animal、Tree 等系统提供统一的存档基础。

------

# 八、长期目标

最终 Simulation 世界将由多个独立系统共同驱动：

```text
TimeSystem
        │
        ▼
SchedulerSystem
        │
        ├── CropSystem
        ├── NPCSystem
        ├── AnimalSystem
        ├── TreeSystem
        ├── BuildingSystem
        ├── ItemSystem
        ├── WeatherSystem
        └── WorldSystem
```

各系统通过统一的时间驱动、实体管理和配置数据协同工作，形成一个能够持续自主运行的模拟世界。

------

# 九、维护规则

每完成一个 Task：

1. 更新当前 Task 状态。
2. 更新下一阶段目标（如有变化）。
3. 保持 RoadMap 与实际开发进度一致。
4. 在 `Docs/TaskLog` 中新增对应 Task 记录。

本文件用于反映项目**当前开发状态**，不记录具体实现细节，具体设计原因和未来演进请参考 `DesignNotes.md`。