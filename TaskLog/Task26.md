- # Task26 SaveSystem接入CropSystem

  ## Task目标

  完成基础SaveSystem框架，并让CropSystem支持数据保存和恢复。

  ------

  # 1. ISaveable设计

  创建：

  ```
  Village.Framework.Save.ISaveable
  ```

  接口：

  ```csharp
  public interface ISaveable
  {
      string SaveKey { get; }
  
      string GetSaveData();
  
      void LoadFromData(string json);
  
      void Reset();
  }
  ```

  设计目标：

  每个业务System负责自己的数据转换。

  SaveSystem不参与业务逻辑。

  ------

  # 2. Crop存档数据设计

  创建：

  ```
  CropSaveData
  
  CropData
  ```

  当前字段：

  ```csharp
  public class CropData
  {
      public long EntityId;
  
      public long ConfigId;
  
      public int GrowthStage;
  
      public GameTime PlantTime;
  }
  ```

  ------

  ## 未保存字段

  以下字段不进入存档：

  ```
  GrowTaskId
  
  WaterTaskId
  
  ReapTaskId
  ```

  原因：

  这些属于运行时调度数据。

  加载后由CropSystem重新创建。

  ------

  # 3. CropSystem存档实现

  CropSystem实现：

  ```csharp
  ISystem
  
  ISaveable
  ```

  实现：

  ## 保存流程

  ```
  CropSystem
  
  ↓
  
  Crop.ToSaveData()
  
  ↓
  
  CropData
  
  ↓
  
  CropSaveData
  
  ↓
  
  Json
  ```

  ------

  ## 加载流程

  ```
  Json
  
  ↓
  
  CropSaveData
  
  ↓
  
  CropData
  
  ↓
  
  CreateCropInternal()
  
  ↓
  
  恢复Crop
  
  ↓
  
  重新注册任务
  ```

  ------

  # 4. Create流程调整

  原：

  ```
  CreateCrop()
  
  直接创建Crop
  ```

  调整：

  ```
  CreateCrop()
  
  ↓
  
  创建CropData
  
  ↓
  
  CreateCropInternal()
  
  ↓
  
  生成Crop
  ```

  Load和Create统一经过：

  ```
  CropData -> Crop
  ```

  避免创建逻辑分裂。

  ------

  # 5. Reset流程调整

  发现：

  多个System和基础管理类需要清理运行时数据。

  最终设计：

  GameStart负责整体初始化流程。

  流程：

  ```
  EntityManager.Reset()
  
  ↓
  
  ISaveable.Reset()
  
  ↓
  
  ISaveable.LoadFromData()
  ```

  ------

  CropSystem：

  ```csharp
  public void Reset()
  {
      _entities.Clear();
  }
  ```

  只清理自身拥有的数据。

  EntityManager：

  负责：

  - EntityId
  - Entity注册

  不由CropSystem控制。

  ------

  # 6. 设计总结

  完成基础存档闭环：

  ```
  Simulation State
  
  ↓
  
  Json
  
  ↓
  
  Restore State
  
  ↓
  
  Recreate Runtime Behavior
  ```

  确定规则：

  > 系统负责恢复自己的状态和运行行为。

  > SaveSystem只负责存取流程。

  ------

  # 后续优化

  - 完善SaveSlot。
  - 完善多System存档管理。
  - 优化EntityId恢复流程。
  - 根据未来Crop成长模型调整行为恢复。