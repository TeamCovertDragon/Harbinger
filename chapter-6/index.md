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

方块模型一般方块状态定义文件中来指定的。前文中提到了最基本的方块状态的写法。

### 附加篇：空模型
定义一个完全透明啥都没有的空模型其实也不是难事：

```json
{}
```

就是这样。两个字节。
