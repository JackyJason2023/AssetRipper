# AssetRipper 开发指南

## 概述
本指南旨在帮助开发者快速上手 AssetRipper 项目的开发工作，包括环境设置、构建流程、代码规范和调试技巧。

## 开发环境设置

### 系统要求
- Windows 10/11, Linux, 或 macOS
- .NET 9.0 SDK
- 支持 C# 的 IDE（Visual Studio, Visual Studio Code, Rider 等）

### 获取源代码
```bash
git clone https://github.com/AssetRipper/AssetRipper.git
cd AssetRipper
```

### 还原依赖项
```bash
dotnet restore
```

## 项目结构

AssetRipper 采用多项目解决方案结构：

```
AssetRipper/
├── Source/
│   ├── AssetRipper.Assets/
│   ├── AssetRipper.Import/
│   ├── AssetRipper.IO.Files/
│   ├── AssetRipper.Processing/
│   ├── AssetRipper.Export/
│   ├── AssetRipper.GUI.Web/
│   ├── AssetRipper.GUI.Free/
│   └── 其他模块...
├── docs/
├── tests/
└── CLAUDE.md
```

## 构建和运行

### 构建项目
```bash
dotnet build
```

### 运行测试
```bash
dotnet test
```

### 运行 GUI 应用
```bash
dotnet run --project Source/AssetRipper.GUI.Free/
```

### 发布应用（AOT）
```bash
dotnet publish -c Release -r <runtime> Source/AssetRipper.GUI.Free/
```
其中 `<runtime>` 可以是 `win-x64`, `linux-x64`, `osx-x64` 等。

## 代码规范

### 命名约定
- 类名使用 PascalCase
- 接口名以 I 开头
- 私有字段以下划线开头
- 常量使用 PascalCase

### 代码组织
- 每个类放在单独的文件中
- 文件名与类名一致
- 使用部分类（partial class）分离大型类的实现

### 注释规范
- 公共 API 使用 XML 文档注释
- 复杂逻辑添加内联注释
- 使用 `/// <summary>` 描述类和方法用途

## 调试技巧

### 日志系统
AssetRipper 使用内置的日志系统：
```csharp
using AssetRipper.Import.Logging;

Logger.Info(LogCategory.General, "日志消息");
Logger.Error(LogCategory.Import, "错误消息", exception);
```

### 调试配置
在 `Debug` 配置下启用更多调试信息：
```csharp
#if DEBUG
    // 调试专用代码
#endif
```

## 单元测试

### 测试框架
使用 NUnit 作为测试框架。

### 编写测试
```csharp
[TestFixture]
public class MyTestClass
{
    [Test]
    public void MyTestMethod()
    {
        // 测试逻辑
        Assert.AreEqual(expected, actual);
    }
}
```

### 运行特定测试
```bash
dotnet test --filter "Name=MyTestMethod"
```

## 常见开发任务

### 添加新的资产类型支持

1. 在 `AssetRipper.SourceGenerated.Extensions` 项目中添加类定义
2. 实现相应的处理器类
3. 在导出模块中添加导出逻辑

### 添加新的导出格式

1. 创建新的导出集合类实现 `IExportCollection`
2. 创建导出器类实现 `IAssetExporter`
3. 在 `ExportHandler` 中注册新的导出器

### 修改处理流程

1. 创建新的处理器类实现 `IAssetProcessor`
2. 在 `ExportHandler.GetProcessors()` 方法中添加处理器

## 性能优化

### 内存管理
- 及时释放 IDisposable 资源
- 使用对象池减少内存分配
- 避免不必要的字符串操作

### 并行处理
- 使用 `Parallel.ForEach` 处理大量数据
- 注意线程安全问题

## 贡献指南

### 提交 Pull Request
1. Fork 项目仓库
2. 创建功能分支
3. 提交更改
4. 推送到 fork 仓库
5. 创建 Pull Request

### 代码审查
- 遵循现有代码风格
- 添加适当的测试
- 更新相关文档

## 常见问题

### 构建失败
检查 .NET SDK 版本是否匹配：
```bash
dotnet --version
```

### 测试失败
查看详细的测试输出：
```bash
dotnet test --verbosity detailed
```

### 性能问题
启用详细日志查看瓶颈：
```csharp
Logger.Verbose = true;
```