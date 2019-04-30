# 方块概论

## 你的第一个方块

我们首先从从一个没有任何功能的方块开始。和物品一样，方块也需要注册：

```java
@Mod.EventBusSubsriber(modid = "my_mod")
public final class BlockInitializer {
    @SubscribeEvent
    public static void registerBlock(RegistryEvent.Register<Block> event) {
        // 和物品一样，每一个方块都有唯一一个注册名，不能使用大写字母。
        event.getRegistry().register(
            new Block(Material.ROCK).setRegistryName("my_mod", "example_block")
        );
    }
}
```

你可以使用 `/setblock ~ ~-1 ~ my_mod:example_block` 命令来在你的脚下放置一个你的新方块。

## `ItemBlock`

如上一章所述，玩家背包里的都是 `ItemStack`。既然如此，方块也不会例外。只不过方块对应物品的 `Item` 类基本都继承了 `ItemBlock` 这个类。如此一来，如果我们想在创造模式物品栏里找到我们的方块，我们需要注册它对应的物品形式，同时（因为某些原因）设定方块所属的“创造模式标签页”：

```java
public static Block myBlock;

@SubscribeEvent
public static void registerBlock(RegistryEvent.Register<Block> event) {
    // 和物品一样，每一个方块都有唯一一个注册名，不能使用大写字母。
    event.getRegistry().register(
        (myBlock = new Block(Material.ROCK)
            .setCreativeTab(EXAMPLE_CREATIVE_TAB)
            .setRegistryName("my_mod", "example_block"))
    );
}

@SubscribeEvent
public static void registerItem(RegistryEvent.Register<Item> event) {
    // 注意这个 ItemBlock 使用了和它对应的方块一样的注册名。
    // 对于所有有物品形态的方块，其物品的注册名和它自己的注册名需要保持一致。
    event.getRegistry().register(
        new ItemBlock(myBlock).setRegistryName("my_mod", "example_block")
    );
}
```

## 方块的名字？

显然，我们平时是不用跟方块的名字打交道的，但我们需要跟它的物品形式打交道，这时我们会发现我们的方块叫 `tile.null.name`。解决方案和第四章中物品名称的本地化是基本一致的，除了这个本地化键是需要给方块的：

```java
public static Block myBlock = new Block()
    .setCreativeTab(EXAMPLE_CREATIVE_TAB)
    .setTranslationKey("my_mod.example_block")
    .setRegistryName("my_mod", "example_block");
```

## 紫黑块？

你可能会发现你的新方块的外观是……紫黑块。<!-- MISSING MODEL IS THE BEST MODEL -->
Minecraft 所使用的模型及渲染系统会[在第十一章有详细说明](../chapter-11/index.md)，这里我们在这里先从一个六个面有不一样的纹理的方块模型开始。

首先，我们需要声明它的 block state json。新建 `assets/my_mod/blockstates/example_block.json`：

```json
{
    "variants": {
        "default": {
            "model": "my_mod:example_block_model"
        }
    }
}
```

它将我们的方块<!-- 的默认状态 -->的模型映射到了 `assets/my_mod/models/block/example_block_model.json` 上。自然地，下一步便是这个 `example_block_model.json`：

```json
{
    "parent": "block/cube",
    "textures": {
        "particle": "my_mod:blocks/example_block_particle.png",
        "down": "my_mod:blocks/example_block_down.png",
        "up": "my_mod:blocks/example_block_up.png",
        "north": "my_mod:blocks/example_block_north.png",
        "south": "my_mod:blocks/example_block_south.png",
        "west": "my_mod:blocks/example_block_west.png",
        "east": "my_mod:blocks/example_block_east.png"
    }
}
```

其中，`particle` 指代的是方块被破坏时的粒子效果的纹理，剩下六个路径均代表各个方向上的纹理所在的位置。这里它们指向的都是 `assets/my_mod/textures/blocks/example_block_[方向].png`。把 PNG 格式的贴图放到指定位置即可。
