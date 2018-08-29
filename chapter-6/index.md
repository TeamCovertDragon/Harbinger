## Minecraft 的方块及物品模型

你看到的 Minecraft 中所有的方块和物品基本都是一个个静态模型贴上合适的材质后的产物。本章专门讨论这个。

### 紫黑块

你应该已经注意到了：你的新物品现在全是紫黑色的方块。虽说有观点认为这是世界上最好的模型，但几乎所有时候这绝对不是你想看到的模型。为此我们需要给它一个模型。

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
    ModelLoader.setCustomModelResourceLocation(myItem, 0, new ModelResourceLocation(new ResourceLocation("example_mod", "example_item_model"), "inventory"));
}
```

重启游戏，若一切操作无误，此时应该能看到紫黑块消失了。

### 方块模型

现在物品不是紫黑块了，但方块还是。方块模型一般是在方块状态定义文件中指定的。实际上方块状态定义文件更像是一个建立方块状态和方块模型之间的映射的文件，通过这个文件，Minecraft 可以知道这个方块在某个状态下应该使用什么模型。  
所有的方块状态定义都应放在 `assets/[modid]/blockstates` 目录下。在这个目录下新建一个 `example_block.json`，注意文件名和方块的注册名一致：

```json
{
	"vairants": {
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

和物品模型类似，这里指定的纹理会去 `assets/[moodid]/textures/blocks` 下查找，要求有 `.png` 后缀，且是 PNG 格式的。是的，这里为了示范，使用的文件名很长——但实际上只要不带大写字母的文件名就可以了，随便什么 `foo`、`bar` 这样的都可以。  
一切准备就绪后，重启游戏，此时方块应当不再是紫黑块了。

### 附加篇：空模型
定义一个完全透明啥都没有的空模型其实也不是难事：

```json
{}
```

就是这样。两个字节。
