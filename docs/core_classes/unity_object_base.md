# UnityObjectBase 类详细说明

## 概述
`UnityObjectBase` 是 AssetRipper 中所有 Unity 对象的基类实现，实现了 `IUnityObjectBase` 接口。它提供了 Unity 对象的基本功能和属性实现。

## 命名空间
```csharp
AssetRipper.Assets
```

## 类定义
```csharp
public abstract class UnityObjectBase : IUnityObjectBase
```

## 继承关系
```
UnityAssetBase -> UnityObjectBase -> 具体的 Unity 对象类
```

## 主要属性

### AssetInfo
```csharp
public AssetInfo AssetInfo { get; }
```
资产的关键信息，包括集合和路径 ID。

### ClassID
```csharp
public abstract int ClassID { get; }
```
抽象属性，需要子类实现具体的类 ID。

### ClassName
```csharp
public abstract string ClassName { get; }
```
抽象属性，需要子类实现具体的类名。

### Collection
```csharp
public AssetCollection Collection { get; }
```
此对象所属的资产集合。

### PathID
```csharp
public long PathID { get; }
```
对象在集合中的路径 ID。

### OriginalPath 和相关属性
```csharp
public string? OriginalPath { get; set; }
public string? OriginalDirectory { get; set; }
public string? OriginalName { get; set; }
public string? OriginalExtension { get; set; }
```
对象的原始路径信息。

### OverridePath 和相关属性
```csharp
public string? OverridePath { get; set; }
public string? OverrideDirectory { get; set; }
public string? OverrideName { get; set; }
public string? OverrideExtension { get; set; }
```
对象的覆盖路径信息。

### AssetBundleName
```csharp
public string? AssetBundleName { get; set; }
```
资产包名称。

### MainAsset
```csharp
public IUnityObjectBase? MainAsset { get; set; }
```
主要资产引用。

## 主要方法

### 构造函数
```csharp
protected UnityObjectBase(AssetInfo assetInfo)
```
使用指定的资产信息初始化 UnityObjectBase 实例。

### Reset
```csharp
public abstract void Reset()
```
抽象方法，用于重置对象到默认状态。

### CopyValues
```csharp
public abstract void CopyValues(IUnityObjectBase? source, PPtrConverter converter)
```
抽象方法，用于从源对象复制值。

### GetBestDirectory
```csharp
public string GetBestDirectory()
```
获取对象的最佳目录路径（密封方法，不可重写）。

### GetBestName
```csharp
public string GetBestName()
```
获取对象的最佳名称（密封方法，不可重写）。

### GetBestExtension
```csharp
public string? GetBestExtension()
```
获取对象的最佳扩展名（密封方法，不可重写）。

## 使用示例

### 创建自定义 Unity 对象
```csharp
public class CustomUnityObject : UnityObjectBase
{
    public override int ClassID => 9999;
    
    public override string ClassName => "CustomUnityObject";
    
    public CustomUnityObject(AssetInfo assetInfo) : base(assetInfo)
    {
    }
    
    public override void Reset()
    {
        // 重置对象到默认状态
    }
    
    public override void CopyValues(IUnityObjectBase? source, PPtrConverter converter)
    {
        if (source is CustomUnityObject customSource)
        {
            // 复制自定义属性
        }
    }
}
```

## 扩展性

UnityObjectBase 设计为可扩展的基类，开发者可以通过继承它来创建自定义的 Unity 对象类型。关键的抽象方法需要在子类中实现：

1. `ClassID` - 返回对象的类 ID
2. `ClassName` - 返回对象的类名
3. `Reset()` - 重置对象到默认状态
4. `CopyValues()` - 从源对象复制值

## 注意事项

1. UnityObjectBase 是抽象类，不能直接实例化
2. 所有属性都有适当的 getter 和 setter（部分为只读）
3. 路径相关方法使用密封实现，确保一致性
4. 对象的状态管理通过 Reset 和 CopyValues 方法处理