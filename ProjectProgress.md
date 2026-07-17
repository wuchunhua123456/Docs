# Project Progress

## Project Information

Project Name:

我要当村长

Version:

v0.1.0-alpha

Engine:

Unity 6.3 LTS

Development Stage:

Phase 1 - Simulation Framework

------

# Current Stage

## Completed

当前已完成：

- Core 框架
- EventCenter
- LogSystem
- TimeSystem
- SchedulerSystem
- EntityManager
- CropSystem
- CropConfig
- Save Framework

------

# Save Framework

完成第一版通用存档框架。

实现：

- ISaveable
- SaveSystem
- SaveData
- SaveVersion

设计：

- SaveSystem 负责统一存档流程。
- 业务 System 负责自身数据序列化。
- Framework 不保存具体业务数据。

当前支持：

- TimeSystem 存档验证。
- CropSystem 存档接入。

原则：

> Save 保存世界状态，不保存运行时对象。

SchedulerTask 等运行时任务由业务 System 根据状态重新创建。

------

# Completed Tasks

## Phase 1 - Simulation Framework

### Task01 - Task17

完成内容：

- 项目基础结构建立
- Core、Framework、Simulation 模块划分
- GameSystemManager
- ISystem 生命周期管理
- EventSystem
- GameTime
- TimeSystem
- SchedulerSystem


### Task18

完成 Entity 基础设计。

内容：

- Entity 基础数据结构
- Entity 生命周期设计


### Task19

完成 EntityManager。

内容：

- Entity 创建
- Entity 注册
- Entity 查询


### Task20

完成 Crop 接入 Entity 体系。

内容：

- Crop 作为 Simulation Entity
- Crop 生命周期基础结构


### Task21

完成 Crop 生命周期设计。

内容：

- 生长阶段
- 成长任务
- 浇水任务
- 收获任务


### Task22

完成 CropSystem 基础搭建。

内容：

- Crop 创建管理
- Crop 生命周期驱动


### Task23

完成 CropConfig 与 ConfigManager 接入。

内容：

- 配置数据独立管理
- Config 驱动 Crop 创建


### Task24

完成 Crop 集成 CropConfig ConfigId。

内容：

- Crop 保存 ConfigId
- 通过 ConfigId 获取对应配置
- 降低 Entity 与 Config 耦合
- 支持后续序列化与存档


### Task25

完成 Save Framework 基础建设。

内容：

- 创建 ISaveable
- 创建 SaveSystem
- 创建 SaveData
- 创建 SaveVersion
- 确定业务 System 自主管理存档数据方案


### Task26

完成 CropSystem 存档接入。

内容：

- CropSystem 实现 ISaveable
- 创建 CropSaveData
- 创建 CropData
- 实现 Crop 数据保存
- 实现 Crop 数据恢复
- Load 时重新创建运行时任务

调整：

- SchedulerTask 不进入存档。
- TaskId 作为运行时数据处理。
- EntityManager 生命周期由初始化流程管理。

------

# Development Roadmap

## Phase 1 - Simulation Framework

目标：

建立游戏模拟基础框架。

包含：

- 时间推进
- 任务调度
- Entity 管理
- 配置系统
- 存档系统


当前状态：

基础框架完成。

后续继续完善 Simulation 模块。

------

## Phase 2 - World Simulation

目标：

建立村庄世界模拟。

包含：

- 地图系统
- 建筑系统
- 资源系统
- NPC 基础系统
- 动物系统


------

## Phase 3 - AI Simulation

目标：

建立 NPC 智能行为。

包含：

- NPC 状态
- 需求系统
- 行为决策
- 对话系统
- AI 驱动

------

# Documentation Rules

项目文档分为四类：

## Architecture.md

记录：

- 项目整体架构
- 模块关系
- 开发规范


## ProjectProgress.md

记录：

- 当前开发阶段
- 当前任务
- 已完成任务
- 后续计划


## DesignNotes.md

记录：

- 重要设计决策
- 架构调整原因
- 后续优化方向



## TaskLog

每完成一个任务创建一个文件。

格式：

TaskXX.md

记录：

- 任务目标
- 完成内容
- 修改文件
- 设计原因
- 后续影响