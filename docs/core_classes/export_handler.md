# ExportHandler 类详细说明

## 概述
`ExportHandler` 类是 AssetRipper 导出系统的核心，负责协调资产的加载、处理和导出流程。它是导出功能的主要入口点。

## 命名空间
```csharp
AssetRipper.Export.UnityProjects
```

## 类定义
```csharp
public class ExportHandler
```

## 主要属性

### Settings
```csharp
protected LibraryConfiguration Settings { get; }
```
库配置设置，包含导入、处理和导出的各种配置选项。

## 构造函数

### ExportHandler
```csharp
public ExportHandler(LibraryConfiguration settings)
```
使用指定的库配置初始化 ExportHandler 实例。

参数：
- `settings`: 库配置对象

## 主要方法

### Load
```csharp
public GameData Load(IReadOnlyList<string> paths)
```
从指定路径加载游戏数据。

参数：
- `paths`: 游戏文件路径列表

返回值：
- `GameData`: 加载的游戏数据

### Process
```csharp
public void Process(GameData gameData)
```
处理游戏数据，应用各种资产处理器。

参数：
- `gameData`: 要处理的游戏数据

### Export
```csharp
public void Export(GameData gameData, string outputPath)
public void Export(GameData gameData, string outputPath, FileSystem fileSystem)
```
将处理后的游戏数据导出到指定路径。

参数：
- `gameData`: 要导出的游戏数据
- `outputPath`: 输出路径
- `fileSystem`: 文件系统实现（可选）

### LoadAndProcess
```csharp
public GameData LoadAndProcess(IReadOnlyList<string> paths)
```
加载并处理游戏数据，是 Load 和 Process 方法的组合。

参数：
- `paths`: 游戏文件路径列表

返回值：
- `GameData`: 加载并处理后的游戏数据

### LoadProcessAndExport
```csharp
public void LoadProcessAndExport(IReadOnlyList<string> inputPaths, string outputPath)
```
加载、处理并导出游戏数据，是完整的导出流程。

参数：
- `inputPaths`: 输入文件路径列表
- `outputPath`: 输出路径

### GetProcessors
```csharp
protected virtual IEnumerable<IAssetProcessor> GetProcessors()
```
获取要应用的资产处理器列表。可以通过重写此方法来自定义处理器。

返回值：
- `IEnumerable<IAssetProcessor>`: 资产处理器枚举

### GetPostExporters
```csharp
protected virtual IEnumerable<IPostExporter> GetPostExporters()
```
获取导出后要执行的后处理导出器。可以通过重写此方法来自定义后处理。

返回值：
- `IEnumerable<IPostExporter>`: 后处理导出器枚举

### BeforeExport
```csharp
protected virtual void BeforeExport(ProjectExporter projectExporter)
```
在导出之前执行的钩子方法。可以通过重写此方法来添加自定义逻辑。

参数：
- `projectExporter`: 项目导出器实例

## 扩展性

ExportHandler 设计为可扩展的类，开发者可以通过以下方式自定义导出流程：

### 1. 继承和重写方法
```csharp
public class CustomExportHandler : ExportHandler
{
    public CustomExportHandler(LibraryConfiguration settings) : base(settings)
    {
    }
    
    protected override IEnumerable<IAssetProcessor> GetProcessors()
    {
        // 添加自定义处理器
        foreach (var processor in base.GetProcessors())
        {
            yield return processor;
        }
        yield return new CustomAssetProcessor();
    }
    
    protected override void BeforeExport(ProjectExporter projectExporter)
    {
        // 添加导出前的自定义逻辑
        base.BeforeExport(projectExporter);
    }
}
```

### 2. 自定义后处理导出器
```csharp
public class CustomPostExporter : IPostExporter
{
    public void DoPostExport(GameData gameData, LibraryConfiguration settings, FileSystem fileSystem)
    {
        // 自定义后处理逻辑
    }
}
```

## 使用示例

### 基本使用
```csharp
// 创建配置
LibraryConfiguration settings = new();

// 创建导出处理器
ExportHandler exportHandler = new(settings);

// 完整的导出流程
exportHandler.LoadProcessAndExport(inputPaths, outputPath);
```

### 分步使用
```csharp
// 创建配置
LibraryConfiguration settings = new();

// 创建导出处理器
ExportHandler exportHandler = new(settings);

// 加载数据
GameData gameData = exportHandler.Load(inputPaths);

// 处理数据
exportHandler.Process(gameData);

// 导出数据
exportHandler.Export(gameData, outputPath);
```

### 自定义配置使用
```csharp
// 创建并配置设置
LibraryConfiguration settings = new();
settings.ImportSettings.ScriptContentLevel = ScriptContentLevel.Level1;
settings.ProcessingSettings.PublicizeAssemblies = true;

// 创建导出处理器
ExportHandler exportHandler = new(settings);

// 执行导出
exportHandler.LoadProcessAndExport(inputPaths, outputPath);
```

## 注意事项

1. ExportHandler 不是线程安全的，不应在多线程环境中同时使用同一实例
2. 配置对象在构造时传递，之后不应修改
3. 导出过程可能需要大量内存和磁盘空间
4. 错误处理通过日志系统进行记录
5. 可以通过继承来完全自定义导出流程