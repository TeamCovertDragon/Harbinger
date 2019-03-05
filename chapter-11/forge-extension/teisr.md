## `TileEntityItemStackRenderer`

`TileEntitySpecialRenderer` 针对 `ItemStack` 的特化版，通常简称 `TEISR`。  

### 写在前面

**几乎所有时候你都不需要这个东西**。没有动画效果的模型可以用普通的原版模型、带仿射变换的 Forge BlockState V1 或者直接使用 OBJ/B3D 模型达成。如果需要依赖特定 NBT 数据则可以使用 [`ItemOverrideList`](../custom-baked/item-override.md)，不过为此你需要一个新的 `IBakedModel`，关于这方面的内容在[本章相关章节](../custom-baked/index.md)有详细介绍。需要动画效果的话，[可以将动画状态机追加到物品上](animation.md#应用于物品)。使用 `TEISR` 有较大可能会引发各种奇怪问题，而且很可能会导致与某些 Mod 不兼容<!-- 说的就是你，OptiFine -->。再有，固定管线……  
总而言之，相当不推荐使用 TEISR。

### 用法

使用 TEISR 需要确保对应物品使用的 `IBakedModel` 的 [`isBulitInRender` 返回 true](../custom-baked/custom-model/baked.md)。一般来说，只需要这样写 JSON 模型即可：

```json
{
  "parent": "builtin/entity"
}
```

接下来的操作就和 `TileEntitySpecialRenderer` 没有太大区别了。毕竟都是直接用“底层”（`GL11` 系列固定管线）来完成渲染。

```java
public class SomeCrazyRenderingForMyItem extends TileEntityItemStackRenderer {
    // Forge patch 之后的结果
    @Override
    public void renderByItem(ItemStack itemStack) {
        // 这里就是具体的渲染操作了。
    }
}
```

### 将 TEISR 与 Item 绑定

透过客户端侧的 `ClientProxy` 调用：

```java
MyItems.myRandomCrazyItem.setTileEntityItemStackRenderer(new SomeCrazyRenderingForMyItem());
```

即可。
