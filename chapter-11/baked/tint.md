# 模型染色

考虑下列几个情况：

  - 原版的草方块、草丛、树叶、蕨、水、甘蔗等方块在不同的生物群系中有不同的颜色。
  - 红石线的颜色会随着红石信号的强度变化而改变其“深浅”。
  - 可以通过西瓜梗和南瓜梗的颜色来判断它们熟没熟。
  - 原版皮革盔甲可以染色。
  - 原版药水瓶依靠药水的“颜色”令玩家能根据其外观区分种类。类似地，原版刷怪蛋也依靠颜色区分种类。

以及两个比较极端的，不是原版的情况：

  - 格雷科技（GregTech）的几千个有完全相同模型和纹理，惟独颜色不同的物品。
  - 匠魂（Tinkers' Construct）自身拥有十余种不同“材质”的工具零件，相同种类的零件基本共用一种模型<!-- 有一个例外，叫仙人掌 -->，和格雷科技一样，亦是仅有颜色上的差别。这些零件的模型最终会被“组装”成完整的工具模型。

这些奇奇怪怪的案例有一个共同特点：“给模型染色，而不是把基本相同的模型重复几百遍”。原版 Minecraft 正好有这么一个允许你给模型染色的机制。

## `tintIndex`

支持染色的方块模型中应当有 `tintIndex` 这个属性。原版所有用到这个属性的模型中，这个属性的值都是 0。下面给出一个改造后的原版 `minecraft:block/cube` 模型以说明 `tintIndex` 属性应当如何使用：

```json
{
    "parent": "block/block",
    "elements": [
        {  
            "from": [ 0, 0, 0 ],
            "to": [ 16, 16, 16 ],
            "faces": {
                "down":  { "texture": "#down", "tintindex": 0, "cullface": "down" },
                "up":    { "texture": "#up", "tintindex": 0, "cullface": "up" },
                "north": { "texture": "#north", "tintindex": 0, "cullface": "north" },
                "south": { "texture": "#south", "tintindex": 0, "cullface": "south" },
                "west":  { "texture": "#west", "tintindex": 0, "cullface": "west" },
                "east":  { "texture": "#east", "tintindex": 0, "cullface": "east" }
            }
        }
    ]
}
```

虽然 Minecraft Wiki 上[“模型”一文提到了这个属性][ref-mcw]，但它提供的信息并不多。  
此外，对于物品模型，只要最终使用的物品模型直接或间接继承自 `item/generated` 就能支持染色，不需要特别的操作。

[ref-mcw]: https://minecraft-zh.gamepedia.com/%E6%A8%A1%E5%9E%8B

## 方块模型染色

```java
import net.minecraft.client.renderer.color.IBlockColor;

public final class MyBlockTinter implements IBlockColor {
    @Override
    public int colorMultiplier(IBlockState state, @Nullable IBlockAccess worldIn, @Nullable BlockPos pos, int tintIndex) {
        // 在这里返回的颜色代码将决定最终方块模型的“颜色”。这里使用 RGB。
        // 第四个参数即是上文中提到的 `tintIndex` 属性的值。
        return 0;
    }
}
```

正确实现 `IBlockColor` 后，注册它即可。Forge 提供了一个事件，允许我们在 Minecraft 自己加载完它自用的 `IBlockColor` 后立即注册我们自己的 `IBlockColor`。

```java
@SubscribeEvent
public static void blockColors(ColorHandlerEvent.Block event) {
    // 第二个参数代表“所有需要使用此 IBlockColor 的物品”，是一个 var-arg Block。
    // 有鉴于第一个参数是一个只有一个方法的接口，我们也可以直接在这里使用 lambda 表达式。
    event.getBlockColors().registerBlockColorHandler(new MyBlockTinter(), block1, block2, block3, ...);
}
```

## 物品模型染色

```java
import net.minecraft.client.renderer.color.IItemColor;

public final class MyItemTinter implements IItemColor {
    @Override
    public int colorMultiplier(ItemStack stack, int tintIndex) {
        // 在这里返回的颜色代码将决定最终物品模型的“颜色”。这里使用 RGB。
        // 第二个参数即是上文中提到的 `tintIndex` 属性的值。
        return 0;
    }
}
```

正确实现 `IItemColor` 后，注册它即可。和 `IBlockColor` 一样，Forge 提供了一个事件，允许我们在 Minecraft 自己加载完它自用的 `IItemColor` 后立即注册我们自己的 `IItemColor`。相比

```java
@SubscribeEvent
public static void itemColors(ColorHandlerEvent.Item event) {
    // 第二个参数代表“所有需要使用此 IItemColor 的物品”，是一个 var-arg Item。
    // 有鉴于第一个参数是一个只有一个方法的接口，我们也可以直接在这里使用 lambda 表达式。
    event.getItemColors().registerItemColorHandler(new MyItemTinter(), item1, item2, item3, ...);

    // 出于某些原因，你还可以在这里拿到之前的 `BlockColors`。在某些时候这个玩意会很有用。
    BlockColors blockColorHandler = event.getBlockColors();
}
```
