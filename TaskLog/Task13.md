# Task13

## 任务目标

统一 System 初始化接口，建立依赖注入方式。

## 完成内容

- 调整 `Init(GameSystemManager)` 初始化方式。
- System 通过 GameSystemManager 获取依赖。
- 减少 System 间直接传递对象。

## 学习收获

理解了依赖注入比直接持有对象更有利于系统解耦和后期扩展。

## 下一步

Task14：验证 Scheduler 实际业务流程。