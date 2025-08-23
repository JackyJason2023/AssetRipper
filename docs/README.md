# AssetRipper 技术文档

## 概述
本目录包含 AssetRipper 项目的完整技术文档，帮助开发者理解、使用和扩展该工具。

## 文档索引

### 1. 系统架构文档
- [系统架构文档](./architecture/system_architecture.md) - 整体系统设计和模块关系

### 2. 核心类详细说明
- [核心类概述](./core_classes/core_classes.md) - 主要接口和类的详细说明
- [UnityObjectBase 类](./core_classes/unity_object_base.md) - Unity 对象基类实现
- [ExportHandler 类](./core_classes/export_handler.md) - 导出处理器详细说明
- [GameStructure 类](./core_classes/game_structure.md) - 游戏结构类详细说明

### 3. 开发指南
- [开发指南](./development/development_guide.md) - 开发环境设置、构建流程和编码规范

### 4. 扩展指南
- [扩展开发指南](./extensions/extension_guide.md) - 如何扩展 AssetRipper 功能

## 使用说明

### 对于新开发者
1. 首先阅读[开发指南](./development/development_guide.md)设置开发环境
2. 了解[系统架构](./architecture/system_architecture.md)以理解整体设计
3. 学习[核心类](./core_classes/core_classes.md)以掌握关键组件

### 对于扩展开发者
1. 阅读[扩展开发指南](./extensions/extension_guide.md)了解扩展机制
2. 参考具体的[核心类文档](./core_classes/)实现自定义功能
3. 查看[系统架构](./architecture/system_architecture.md)理解数据流

## 贡献指南

如果您希望为 AssetRipper 项目做贡献，请参考以下文档：
- [开发指南](./development/development_guide.md)中的贡献指南部分
- [GitHub 仓库](https://github.com/AssetRipper/AssetRipper)中的 CONTRIBUTING.md 文件