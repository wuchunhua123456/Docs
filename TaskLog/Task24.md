# Task24 Crop 集成 CropConfig ConfigId

## 任务目标

建立 Crop 实例与 CropConfig 的正式关联。

## 完成内容

将 CropConfig 的 ConfigId 集成到 Crop 中。

Crop 保存对应配置唯一标识。

## 主要实现

调整 Crop 数据结构：

Crop
包含：
- ConfigId
- 运行时状态数据

通过 ConfigId 查询对应 CropConfig。

## 涉及脚本

- Crop.cs
- CropConfig.cs

## 设计说明

通过 ID 关联配置，而不是直接保存配置对象。

优势：

- 方便序列化。
- 方便存档。
- 减少对象引用依赖。
- 支持配置热更新。

## 最终成果

完成作物实例与配置数据绑定。

## 后续衔接

继续开发 CropSystem，实现播种、生长、收获等具体逻辑。