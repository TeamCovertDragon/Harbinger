## `ItemOverrideList`

还记得物品模型中的 [Property Override][ref-property-override] 吗？这些 Override 实际上都存放在了这个名为 `ItemOverrideList` 的类的实例中。它的主要用途是根据当前物品的信息获取模型的 override，由 Forge patch 后的 `handleItemState` 方法控制。  
看上去没什么大不了的。但实际上，因为 `handleItemState` 中有一个 `ItemStack` 参数，加上 `handleItemState` 返回的是 `IBakedModel`，所以它实际上可以实现比 `ItemMeshDefinition` 更高级的物品模型控制。匠魂（Tinkers Construct）的工具及武器模型便是靠这个来实现的。

[ref-property-override]: ../vanilla/custom-mesh.md#property-override

```java
public class BrandNewModelItemOverrideList extends ItemOverrideList {

    @Override
    public IBakedModel handleItemState(IBakedModel originalModel, ItemStack stack, @Nullable World world, @Nullable EntityLivingBase entity) {
        // 在这个方法中你可以根据一开始的模型（originalModel）来生成适用于物品形态的模型。
        // 通常的做法是使用 Forge 提供的 PerspectiveMapWrapper，它能让你指定不同视角下需要的不同模型变换。
        return originalModel;
    }

    @Override
    public ImmutableList<ItemOverride> getOverrides() {
        // 重新实现的 ItemOverrideList 基本用不着这个。所以返回一个空表。
        return ImmutableList.of();
    }
}
```
