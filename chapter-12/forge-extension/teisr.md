## `TileEntityItemStackRenderer`

`TileEntitySpecialRenderer` 针对 `ItemStack` 的特化版，通常简称 `TEISR`。绝大多数时候你都不需要这个东西。  
使用 TEISR 需要确保对应物品使用的 `IBakedModel` 的 [`isBulitInRender` 返回 false](../../chapter-6/custom-model/baked.md)。

```java
public class SomeCrazyRenderingForMyItem extends TileEntityItemStackRenderer {
    // Forge patch 之后的结果
    @Override
    public void renderByItem(ItemStack itemStack) {
        // 这里就是具体的渲染操作了。
    }
}
```

### 将 TEISR 与 Item 进行绑定

在客户端侧的 `ClientProxy` 中，调用：

```java
MyItems.myRandomCrazyItem.setTileEntityItemStackRenderer(new SomeCrazyRenderingForMyItem());
```

即可。
