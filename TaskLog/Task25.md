# Task25 - SaveFramework基础搭建

## 任务目标

搭建游戏通用存档框架，为后续各个 Simulation 模块提供统一的保存与读取能力。

---

## 完成内容

### Framework

新增 Save 模块：

- ISaveable
- SaveData
- SaveSystem
- SaveVersion

实现统一存档框架。

### ISaveable

定义所有可存档系统统一接口。

每个业务系统只负责：

- 提供保存数据
- 根据读取数据恢复自身状态

SaveSystem 不关心具体业务数据类型。

### SaveSystem

实现：

- 模块注册
- 保存全部模块
- 加载全部模块
- JSON 序列化
- JSON 反序列化

使用 Newtonsoft.Json 完成对象序列化。

### SaveData

作为整个存档文件的数据入口。

负责：

- 存档版本
- 所有模块 JSON 数据

采用 Dictionary<string, string> 保存各模块数据。

### SaveVersion

新增统一存档版本管理。

当前版本：

Version = 1

为后续存档升级预留入口。

---

## 测试

使用 TimeSystem 作为第一个接入模块。

实现：

- 当前时间保存
- 当前时间读取恢复

并通过按键完成：

- Save
- Load

验证 Save Framework 全流程正常运行。

---

## 最终结果

成功完成第一版 Save Framework。

框架已具备：

- 模块注册
- 通用存档
- JSON 序列化
- JSON 读取
- 存档版本管理
- 多系统扩展能力

后续 CropSystem、NPCSystem、InventorySystem 等模块均可直接接入 ISaveable，无需修改 SaveSystem。