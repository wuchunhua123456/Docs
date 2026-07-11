# Task22

## 任务目标

完善 CropSystem 与 SchedulerSystem 的协作设计。

## 完成内容

- Crop 保存自身 TaskId。
- CropSystem 创建并管理作物相关任务。
- 完善浇水、成长、收获任务关联。
- 优化 Crop 生命周期结束时的任务清理流程。

## 学习收获

理解了业务对象不直接控制基础系统，而是通过 System 管理生命周期，同时认识到 Task 与 Entity 生命周期绑定的重要性。