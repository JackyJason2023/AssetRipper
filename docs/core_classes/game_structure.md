# GameStructure 类详细说明

## 概述
`GameStructure` 类负责检测和表示游戏的整体结构，包括文件收集、平台检测和程序集管理。它是 AssetRipper 导入流程的核心组件。

## 命名空间
```csharp
AssetRipper.Import.Structure
```

## 类定义
```csharp
public sealed class GameStructure : IDisposable
```

## 主要属性

### FileCollection
```csharp
public GameBundle FileCollection { get; private set; }
```
游戏的文件集合，包含所有加载的资产。

### PlatformStructure
```csharp
public PlatformGameStructure? PlatformStructure { get; private set; }
```
特定平台的游戏结构（如果检测到）。

### MixedStructure
```csharp
public PlatformGameStructure? MixedStructure { get; private set; }
```
混合平台的游戏结构（如果适用）。

### AssemblyManager
```csharp
public IAssemblyManager AssemblyManager { get; set; }
```
程序集管理器，用于处理 Mono 或 IL2Cpp 程序集。

### IsValid
```csharp
public bool IsValid => FileCollection.HasAnyAssetCollections();
```
指示游戏结构是否有效的只读属性。

### Name
```csharp
public string? Name => PlatformStructure?.Name ?? MixedStructure?.Name;
```
游戏结构的名称。

## 构造函数

### GameStructure
```csharp
private GameStructure(List<string> paths, CoreConfiguration configuration)
```
私有构造函数，通过 `Load` 静态方法创建实例。

参数：
- `paths`: 游戏文件路径列表
- `configuration`: 核心配置对象

## 主要方法

### Load
```csharp
public static GameStructure Load(IEnumerable<string> paths, CoreConfiguration configuration)
```
静态方法，从指定路径加载游戏结构。

参数：
- `paths`: 游戏文件路径枚举
- `configuration`: 核心配置对象

返回值：
- `GameStructure`: 加载的游戏结构实例

### RequestAssembly
```csharp
public string? RequestAssembly(string assembly)
```
请求指定名称的程序集路径。

参数：
- `assembly`: 程序集名称

返回值：
- `string?`: 程序集文件路径，如果未找到则返回 null

### Dispose
```csharp
public void Dispose()
```
释放游戏结构使用的资源。

## 内部方法

### InitializeGameCollection
```csharp
[MemberNotNull(nameof(FileCollection))]
private void InitializeGameCollection(UnityVersion defaultVersion, UnityVersion targetVersion)
```
初始化游戏文件集合。

参数：
- `defaultVersion`: 默认 Unity 版本
- `targetVersion`: 目标 Unity 版本

### InitializeAssemblyManager
```csharp
[MemberNotNull(nameof(AssemblyManager))]
private void InitializeAssemblyManager(CoreConfiguration configuration)
```
初始化程序集管理器。

参数：
- `configuration`: 核心配置对象

### GetScriptingBackend
```csharp
private ScriptingBackend GetScriptingBackend(bool disableScriptImport)
```
获取脚本后端类型。

参数：
- `disableScriptImport`: 是否禁用脚本导入

返回值：
- `ScriptingBackend`: 脚本后端类型

### OnRequestAssembly
```csharp
private void OnRequestAssembly(string assembly)
```
处理程序集请求的回调方法。

参数：
- `assembly`: 程序集名称

## 工作流程

1. **路径处理**: 接收输入路径并处理 ZIP 文件
2. **平台检测**: 检测游戏平台结构
3. **文件收集**: 收集所有相关文件
4. **程序集管理器初始化**: 根据检测到的脚本后端初始化相应的程序集管理器
5. **游戏集合初始化**: 创建包含所有资产的游戏集合

## 使用示例

### 基本使用
```csharp
// 创建核心配置
CoreConfiguration configuration = new();

// 加载游戏结构
GameStructure gameStructure = GameStructure.Load(paths, configuration);

// 检查有效性
if (gameStructure.IsValid)
{
    // 访问文件集合
    GameBundle fileCollection = gameStructure.FileCollection;
    
    // 访问程序集管理器
    IAssemblyManager assemblyManager = gameStructure.AssemblyManager;
}
```

### 自定义配置使用
```csharp
// 创建并配置核心配置
CoreConfiguration configuration = new();
configuration.ImportSettings.IgnoreStreamingAssets = true;
configuration.ImportSettings.DefaultVersion = new UnityVersion(2020, 3, 0);
configuration.DisableScriptImport = false;

// 加载游戏结构
GameStructure gameStructure = GameStructure.Load(paths, configuration);
```

## 扩展性

虽然 GameStructure 是密封类，但可以通过以下方式扩展功能：

### 1. 自定义 PlatformGameStructure
```csharp
// 创建自定义平台结构检测逻辑
public class CustomPlatformGameStructure : PlatformGameStructure
{
    // 实现自定义平台检测逻辑
}
```

### 2. 自定义 AssemblyManager
```csharp
// 创建自定义程序集管理器
public class CustomAssemblyManager : IAssemblyManager
{
    // 实现自定义程序集处理逻辑
}
```

## 注意事项

1. GameStructure 实现了 IDisposable 接口，使用后需要正确释放资源
2. 平台检测是自动进行的，但可能需要手动干预处理复杂情况
3. 程序集管理器会根据检测到的脚本后端自动选择
4. 文件集合包含所有加载的资产，是后续处理的基础
5. ZIP 文件会自动解压和处理
6. 无效的游戏结构可以通过 IsValid 属性检测