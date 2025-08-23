# AssetRipper 扩展开发指南

## 概述
本指南介绍如何扩展 AssetRipper 的功能，包括添加新的资产类型支持、自定义处理器、导出器和其他扩展点。

## 扩展点概述

AssetRipper 设计了多个扩展点，允许开发者自定义和扩展功能：

1. **资产处理器** - 处理导入后的资产
2. **导出器** - 控制资产如何导出
3. **后处理导出器** - 导出完成后的额外处理
4. **自定义资产类型** - 支持新的 Unity 资产类型

## 自定义资产处理器

### 创建处理器类
```csharp
using AssetRipper.Processing;
using AssetRipper.Assets;

public class CustomAssetProcessor : IAssetProcessor
{
    public void Process(GameData gameData)
    {
        // 处理逻辑
        foreach (IUnityObjectBase asset in gameData.GameBundle.FetchAssets())
        {
            // 处理特定类型的资产
            if (asset is ICustomAssetType customAsset)
            {
                // 自定义处理逻辑
                ProcessCustomAsset(customAsset);
            }
        }
    }
    
    private void ProcessCustomAsset(ICustomAssetType asset)
    {
        // 具体处理逻辑
    }
}
```

### 注册处理器
通过继承 `ExportHandler` 并重写 `GetProcessors` 方法：

```csharp
public class CustomExportHandler : ExportHandler
{
    public CustomExportHandler(LibraryConfiguration settings) : base(settings)
    {
    }
    
    protected override IEnumerable<IAssetProcessor> GetProcessors()
    {
        // 添加自定义处理器
        yield return new CustomAssetProcessor();
        
        // 包含默认处理器
        foreach (var processor in base.GetProcessors())
        {
            yield return processor;
        }
    }
}
```

## 自定义导出器

### 创建导出集合
```csharp
using AssetRipper.Export.UnityProjects;

public class CustomExportCollection : IExportCollection
{
    public AssetCollection Collection { get; }
    
    public CustomExportCollection(AssetCollection collection)
    {
        Collection = collection;
    }
    
    public bool Contains(IUnityObjectBase asset)
    {
        // 判断资产是否属于此集合
        return asset is ICustomAssetType;
    }
    
    public bool Export(ProjectAssetContainer container, IUnityObjectBase asset)
    {
        // 导出逻辑
        if (asset is ICustomAssetType customAsset)
        {
            return ExportCustomAsset(container, customAsset);
        }
        return false;
    }
    
    private bool ExportCustomAsset(ProjectAssetContainer container, ICustomAssetType asset)
    {
        // 具体导出实现
        return true;
    }
    
    public void ExportMiscellaneousFiles(ProjectAssetContainer container)
    {
        // 导出额外文件
    }
    
    public IAssetExporter CreateExporter()
    {
        return new CustomAssetExporter();
    }
}
```

### 创建导出器
```csharp
using AssetRipper.Export.UnityProjects;

public class CustomAssetExporter : IAssetExporter
{
    public bool TryExport(ProjectAssetContainer container, IUnityObjectBase asset, out string path)
    {
        path = string.Empty;
        
        if (asset is ICustomAssetType customAsset)
        {
            // 导出逻辑
            path = ExportCustomAsset(container, customAsset);
            return !string.IsNullOrEmpty(path);
        }
        
        return false;
    }
    
    private string ExportCustomAsset(ProjectAssetContainer container, ICustomAssetType asset)
    {
        // 实现具体的导出逻辑
        return "exported_path";
    }
    
    public void Dispose()
    {
        // 清理资源
    }
}
```

## 添加新的资产类型支持

### 1. 定义资产接口
```csharp
using AssetRipper.Assets;

public interface ICustomAssetType : IUnityObjectBase
{
    // 定义资产特有的属性和方法
    string CustomProperty { get; set; }
}
```

### 2. 实现资产类
```csharp
using AssetRipper.Assets;

public class CustomAssetType : UnityObjectBase, ICustomAssetType
{
    public override int ClassID => 9999; // 自定义类 ID
    public override string ClassName => "CustomAssetType";
    
    public string CustomProperty { get; set; } = string.Empty;
    
    public CustomAssetType(AssetInfo assetInfo) : base(assetInfo)
    {
    }
    
    public override void Reset()
    {
        CustomProperty = string.Empty;
    }
    
    public override void CopyValues(IUnityObjectBase? source, PPtrConverter converter)
    {
        if (source is ICustomAssetType customSource)
        {
            CustomProperty = customSource.CustomProperty;
        }
    }
}
```

### 3. 创建资产工厂
```csharp
using AssetRipper.Import.AssetCreation;

public class CustomAssetFactory : IAssetFactory
{
    public IUnityObjectBase? CreateAsset(AssetInfo assetInfo, int classID)
    {
        return classID switch
        {
            9999 => new CustomAssetType(assetInfo),
            _ => null
        };
    }
}
```

## 自定义后处理导出器

### 创建后处理导出器
```csharp
using AssetRipper.Export.UnityProjects;

public class CustomPostExporter : IPostExporter
{
    public void DoPostExport(GameData gameData, LibraryConfiguration settings, FileSystem fileSystem)
    {
        // 导出完成后的处理逻辑
        string exportPath = settings.ExportRootPath;
        
        // 例如：生成自定义报告
        GenerateCustomReport(gameData, exportPath, fileSystem);
    }
    
    private void GenerateCustomReport(GameData gameData, string exportPath, FileSystem fileSystem)
    {
        // 实现自定义报告生成逻辑
    }
}
```

### 注册后处理导出器
```csharp
public class CustomExportHandler : ExportHandler
{
    public CustomExportHandler(LibraryConfiguration settings) : base(settings)
    {
    }
    
    protected override IEnumerable<IPostExporter> GetPostExporters()
    {
        yield return new CustomPostExporter();
        
        foreach (var postExporter in base.GetPostExporters())
        {
            yield return postExporter;
        }
    }
}
```

## 自定义文件系统

### 扩展 FileSystem
```csharp
using AssetRipper.IO.Files;

public class CustomFileSystem : FileSystem
{
    public override void CreateDirectory(string path)
    {
        // 自定义目录创建逻辑
        base.CreateDirectory(path);
    }
    
    public override Stream CreateFile(string path)
    {
        // 自定义文件创建逻辑
        return base.CreateFile(path);
    }
}
```

## 配置扩展

### 添加自定义配置选项
```csharp
public class CustomConfiguration
{
    public bool EnableCustomFeature { get; set; } = false;
    public string CustomSetting { get; set; } = string.Empty;
}

// 扩展现有配置
public static class ConfigurationExtensions
{
    public static CustomConfiguration CustomSettings { get; } = new CustomConfiguration();
}
```

## 集成示例

### 完整的自定义扩展实现
```csharp
// Program.cs 或启动代码
public class CustomApplication
{
    public static void Main(string[] args)
    {
        // 创建自定义配置
        LibraryConfiguration settings = new();
        settings.CustomSettings.EnableCustomFeature = true;
        
        // 使用自定义导出处理器
        CustomExportHandler exportHandler = new(settings);
        
        // 执行导出
        exportHandler.LoadProcessAndExport(inputPaths, outputPath);
    }
}
```

## 最佳实践

### 1. 性能考虑
- 避免在处理器中进行重复计算
- 使用缓存存储中间结果
- 考虑并行处理大量数据

### 2. 错误处理
- 使用日志系统记录错误和警告
- 提供有意义的错误消息
- 优雅地处理异常情况

### 3. 兼容性
- 确保扩展与不同 Unity 版本兼容
- 处理资产缺失或损坏的情况
- 提供向后兼容性

### 4. 测试
- 为扩展功能编写单元测试
- 测试不同场景和边界条件
- 验证与现有功能的集成

## 调试扩展

### 使用日志
```csharp
using AssetRipper.Import.Logging;

Logger.Info(LogCategory.Export, "自定义处理器开始执行");
Logger.Verbose("处理资产: " + asset.PathID);
```

### 调试配置
```csharp
#if DEBUG
    // 调试专用代码
    Debugger.Break();
#endif
```

## 发布扩展

### 打包扩展
1. 创建 NuGet 包
2. 发布到 NuGet 仓库
3. 提供文档和示例

### 版本管理
- 遵循语义化版本控制
- 保持与主项目版本兼容
- 提供迁移指南