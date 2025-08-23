# AssetRipper 系统架构文档

## 概述

AssetRipper 是一个用于从 Unity 序列化文件和资源包中提取资产的工具，能够将这些资产转换为原生 Unity 引擎格式。该工具支持从 Unity 3.5.0 到 6000.2.X 的多个版本。

## 整体架构

AssetRipper 采用分层模块化架构，主要包含以下几个核心层：

### 1. IO 层 (AssetRipper.IO.Files)
- 负责文件系统的抽象和文件操作
- 处理不同平台的文件路径和命名规范
- 提供虚拟文件系统支持

### 2. 导入层 (AssetRipper.Import)
- 负责读取和解析 Unity 文件格式
- 包括序列化文件、资源包、资源文件等
- 检测游戏平台和结构
- 管理程序集（Assembly）加载

### 3. 资产层 (AssetRipper.Assets)
- 定义 Unity 对象的内存表示
- 提供统一的资产接口
- 处理资产元数据和引用关系

### 4. 处理层 (AssetRipper.Processing)
- 对导入的资产进行后处理
- 包括程序集处理、场景处理、预制件处理等
- 建立资产间的关联关系

### 5. 导出层 (AssetRipper.Export)
- 将处理后的资产导出为目标格式
- 支持 Unity 项目结构导出
- 支持原始内容导出

### 6. GUI 层 (AssetRipper.GUI)
- 提供 Web 界面用于用户交互
- 基于 ASP.NET Core 构建
- 支持 Native AOT 编译

## 核心模块详解

### AssetRipper.IO.Files
该模块提供文件系统抽象，支持本地文件系统和虚拟文件系统。主要功能包括：
- 文件路径处理和验证
- 文件名冲突解决
- 特殊字符处理
- 跨平台文件系统兼容性

### AssetRipper.Import
导入模块是整个系统的核心，负责解析 Unity 文件格式：

#### Structure 子模块
- `GameStructure`: 游戏结构表示和检测
- `PlatformGameStructure`: 特定平台的游戏结构
- `GameInitializer`: 游戏初始化和版本处理

#### Assembly 子模块
- `IAssemblyManager`: 程序集管理器接口
- `MonoManager`: Mono 程序集管理
- `IL2CppManager`: IL2Cpp 程序集管理
- `BaseManager`: 基础程序集管理

### AssetRipper.Assets
资产模块定义了 Unity 对象的内存表示：

#### 核心接口
- `IUnityObjectBase`: Unity 对象基础接口
- `IUnityAssetBase`: Unity 资产基础接口
- `AssetInfo`: 资产信息元数据

#### 集合管理
- `AssetCollection`: 资产集合
- `ProcessedAssetCollection`: 处理后的资产集合
- `BundleCollection`: 资源包集合

### AssetRipper.Processing
处理模块负责对导入的资产进行后处理：

#### 处理器接口
- `IAssetProcessor`: 资产处理器接口
- `MainAssetProcessor`: 主资产处理器
- `SceneDefinitionProcessor`: 场景定义处理器

#### 特定类型处理
- 动画控制器处理
- 音频混合器处理
- 预制件处理
- 脚本对象处理

### AssetRipper.Export
导出模块负责将处理后的资产导出：

#### 导出处理器
- `ExportHandler`: 导出处理器
- `ProjectExporter`: 项目导出器
- `PrimaryContentExporter`: 原始内容导出器

#### 导出集合
- `IExportCollection`: 导出集合接口
- `AssetExportCollection`: 资产导出集合
- `ExportCollection`: 基础导出集合

### AssetRipper.GUI.Web
Web GUI 模块提供用户界面：

#### 核心组件
- `WebApplicationLauncher`: Web 应用启动器
- `GameFileLoader`: 游戏文件加载器
- `RoutingExtensions`: 路由扩展

#### 页面系统
- 基于 ASP.NET Core 的页面路由
- RESTful API 接口
- 静态文件服务

## 数据流

1. **文件加载**: GUI 层接收用户输入路径
2. **结构检测**: Import 层检测游戏平台和结构
3. **文件解析**: IO 层读取并解析 Unity 文件
4. **资产创建**: Assets 层创建内存中的资产对象
5. **资产处理**: Processing 层对资产进行后处理
6. **资产导出**: Export 层将资产导出为目标格式

## 设计原则

1. **模块化设计**: 各层职责明确，依赖关系清晰
2. **接口驱动**: 核心功能通过接口定义，便于扩展
3. **可测试性**: 各模块设计考虑了单元测试需求
4. **性能优化**: 支持 Native AOT 编译，优化运行时性能
5. **跨平台**: 支持 Windows、Linux、macOS 等多个平台