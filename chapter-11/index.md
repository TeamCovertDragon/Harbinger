## Minecraft 的方块及物品模型

你看到的 Minecraft 中所有的方块和物品基本都是一个个静态模型贴上合适的纹理后的产物。本章专门讨论这个。

### 紫黑块

你应该已经注意到了：你的新物品现在全是紫黑色的方块。虽说有观点认为这是世界上最好的模型，但几乎所有时候这绝对不是你想看到的模型。为此我们需要给它一个模型。  

### JSON

是的，这些模型是用一种基于 JSON 的 DSL 描述的。  
Minecraft 本身使用的 JSON 格式的模型定义在[中文 Minecraft Wiki][link-mw-model] 上有比较详细的解释，有问题的时候可以参考。简单来说，Minecraft 的方块/物品模型系统可以表达只有立方体组成的模型，每个立方体都可以旋转，但旋转的角度有限制（旋转的基本单位是 1/16 圆周，即 22.5 度，不支持任意角度旋转），且只能绕一个轴转。即便如此，在这样一个到处都是方块的世界里，这样的模型也足够用了。  

[link-mw-model]: https://minecraft-zh.gamepedia.com/%E6%A8%A1%E5%9E%8B#.E6.96.B9.E5.9D.97.E6.A8.A1.E5.9E.8B

### 模型的定义

在 `resources` 目录下依次建立 `assets/[modid]/models/item` 目录，然后新建 `example_item_model.json`：

```json
{
    "parent": "item/generated",
    "textures": {
        "layer0": "example_mod:item/example_item_texture"
    }
}
```

可以说这是最简单的物品模型了——你只需要指定纹理（texture）的位置就可以了。`example_mod:example_item_texture` 意味着你的纹理应当位于 `assets/[modid]/textures/item/example_item_texture.png`，格式自然是 png，没得商量。  
完成这些后，订阅 `ModelRegistryEvent`：

```java
@SubscribeEvent
public static void onModelRegistration(ModelRegistryEvent event) {
    // 因为一些奇怪的原因，原版不会主动去搜索 Mod 的物品的模型的位置。我们需要手动指定一个。
    ModelLoader.setCustomModelResourceLocation(myItem, 0, new ModelResourceLocation(new ResourceLocation("example_mod", "example_item_model"), "inventory"));
}
```

重启游戏，若一切操作无误，此时应该能看到紫黑块消失了。

### 方块模型

现在物品不是紫黑块了，但方块还是。方块模型一般是在方块状态定义文件中指定的。实际上方块状态定义文件更像是一个建立方块状态和方块模型之间的映射的文件，通过这个文件，Minecraft 可以知道这个方块在某个状态下应该使用什么模型。  
所有的方块状态定义都应放在 `assets/[modid]/blockstates` 目录下。在这个目录下新建一个 `example_block.json`，注意文件名和方块的注册名一致：

```json
{
    "variants": {
        "default": "example_mod:example_block_model"
    }
}
```

因为这个方块没什么特别的所以它只有一种状态：`default`。对于那些有各种奇怪状态的方块，这里会稍微麻烦一些，但这是后话。和物品的模型类似，方块模型统一放在 `assets/[modid]/models/block` 目录下。也就是说，上面我们给出的 `example_mod:example_block_model` 代表 Minecraft 会去寻找 `assets/example_mod/models/block/example_block_model.json` 这个文件。切换到目标目录下新建这个文件：

```json
{
    "parent": "block/cube",
    "textures": {
        "up": "example_mod:blocks/example_block_texture_up",
        "down": "example_mod:blocks/example_block_texture_down",
        "west": "example_mod:blocks/example_block_texture_west",
        "north": "example_mod:blocks/example_block_texture_north",
        "east": "example_mod:blocks/example_block_texture_east",
        "south": "example_mod:blocks/example_block_texture_south"
    }
}
```

和物品模型类似，这里指定的纹理会去 `assets/[moodid]/textures/blocks` 下查找，要求有 `.png` 后缀，且是 PNG 格式的。是的，这里为了示范，使用的文件名很长——但实际上只要不带大写字母的文件名就可以了，随便什么 `foo`、`bar` 这样的都可以。能分清楚就行。加一堆目录来整理也可以。  
一切准备就绪后，重启游戏，此时方块应当不再是紫黑块了。

### 方块的物品

但这个时候方块的物品可能还是紫黑块。毕竟方块的物品形式终究是个物品，和普通的物品没有本质上的区别。它的处理方式和普通物品模型的处理方式没有太大差别，但 `ModelLoader.setCustomModelResourceLocation` 只接受物品，需要通过某种方式拿到方块的物品形式才行。

```java
Item.getItemFromBlock(myBlock);
```

其实，注册方块的物品形式的时候，如果你自己保留了一个对它的引用的字段，那你可以直接用那个引用。  
然后就是模型了。这里可以走一个捷径，因为物品模型可以直接套用方块模型：

```json
{
    "parent": "example_mod:block/example_block_model"
}
```

这样就够了。事实上，任何物品都可以用这样的方式拥有一个方块的模型，对于那些设定上外观是方块的合成用物品来说这样做相当省事。

### 附加篇：空模型

定义一个完全透明啥都没有的空模型其实也不是难事：

```json
{}
```

就是这样。两个字节。
