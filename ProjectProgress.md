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

# Current Progress

## Current Status

当前已完成 Simulation Framework 基础建设。

已完成：

- Core 基础框架
- GameSystem 管理体系
- EventSystem
- GameTime
- TimeSystem
- SchedulerSystem
- Entity 基础体系
- EntityManager
- Crop 基础模拟体系
- CropConfig 配置体系

## Current Task

当前任务：

Task25 - SaveSystem

## Next Goal

完成基础存档系统，使 Simulation 中的 Entity、System 状态可以被保存和恢复。

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

## Phase 2 - World Simulation

目标：

建立村庄世界模拟。

包含：

- 地图系统
- 建筑系统
- 资源系统
- NPC 基础系统
- 动物系统

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