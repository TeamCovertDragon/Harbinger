# 物品概论

## 你的第一个物品

我们首先通过一个叫 `RegistryEvent.Register<Item>` 的事件注册一个啥都没用的物品。

```java
@Mod.EventBusSubscriber(modid = "my_mod")
public final class ItemInitializer {

    // 和正常的事件一样，你不需要手动调用此方法！Forge 会自动调用它的。
    @SubscribeEvent
    public static void registerItem(RegistryEvent.Register<Item> event) {
        // 注意 setRegistryName 调用。
        // 每一个物品都对应唯一一个注册名，用于和其他物品区分开来。这个注册名不能含有大写字母。
        // 此方法返回被注册的 Item 对象。
        event.getRegistry().register(new Item().setRegistryName("my_mod:example_item"));
    }
}
```

注意，每一个透过此法注册的物品的实例必须都有调用过 `setRegistryName`，否则会抛异常并导致游戏退出。

但是……创造物品栏里没有你的新物品。当然你可以用 `/give <玩家名> <registry name>` 获得你的新物品，但你不觉得显示在创造模式物品栏里更方便么。

```java
// 我们需要一个 CreativeTabs 的实例，当然那个类下面就有原版的 10 个，可以直接拿来用，
// 但是你不觉得有一个自己的更方便吗……
// 一般是使用匿名内部类，就像这样。
public static final CreativeTabs EXAMPLE_CREATIVE_TAB = new CreativeTabs("example_tab") {
    // 获得用作标签图标的 ItemStack。你大可以往里面塞各种奇奇怪怪的数据。
    @Override
    public ItemStack createIcon() {
        return new ItemStack(Items.DIAMOND);
    }
};

// 然后这样我们就能让它显示在我们自己的创造标签页中。
// 这个方法返回 Item。
public static Item yourItem = new Item().setCreativeTab(EXAMPLE_CREATIVE_TAB);
```

## 呃…… 这物品名字不对……

你可能会发现你的新物品叫 `item.null.name`。我们需要给它一个正常点的名字。
因为 Minecraft 是个面向全球的游戏，所以这个过程有些复杂，因为我们需要确保使用其他语言的人可以翻译这个物品名称。关于这部分内容的细节可参考[第十三章](../chapter-13/index.md)。

```java
public static Item yourItem = new Item()
    .setCreativeTab(EXAMPLE_CREATIVE_TAB)
    // 注意此名字和 registry name 不是一个概念。
    // 这个名字仅用于国际化支持。
    // 这个方法也返回 Item。
    .setTranslationKey("my_mod.example_item")
    .setRegistryName("my_mod", "example_item");
```

然后，是喜闻乐见的语言文件：

 1. 在 `resources` 下新建 `assets` 文件夹
 2. 在 `assets` 下新建文件夹，名字和你的 modid 相同
 3. 在以你的 modid 命名的文件夹下新建 `lang` 文件夹
 4. 在 `lang` 文件夹下新建 `en_us.lang`：

    ```
    item.example_mod.example_item.name=Example Item
    ```

 5. 等等，如果没理解错，这是英文（美国）的 Locale 啊？  
    OK。然后还是 `lang` 文件夹，新建 `zh_cn.lang`：  

    ```
    item.example_mod.example_item.name=示例物品
    ```

## 紫黑块？

你可能会发现你的新物品的外观是……紫黑块。<!-- MISSING MODEL IS THE BEST MODEL -->
Minecraft 所使用的模型及渲染系统会[在第十一章有详细说明](../chapter-11/index.md)，这里我们在这里先从一个毫无特效的普通“贴图”开始。

首先，我们需要一个单独的类来订阅 `ModelRegistryEvent`，在这里我们需要让 Forge 告知 Minecraft 从我们指定的地方查找这个物品的模型：

```java
import net.minecraft.client.renderer.block.model.ModelResourceLocation;
import net.minecraftforge.client.model.ModelLoader;

@Mod.EventBusSubscriber(value = Side.CLIENT, modid = "my_mod")
public final class ModelMapper {
    @SubscribeEvent
    public static void onModelReg(ModelRegistryEvent event) {
        ModelLoader.setCustomModelResourceLocation(yourItem, 0, new ModelResourceLocation(yourItem.getRegistryName(), "inventory"));
    }
}
```

在这里我们通过重用物品的注册名，将该物品的模型定义文件指向了 `assets/my_mod/models/item/example_item.json`。  
此时我们只需要创建这个文件就可以了：

```json
{
    "parent": "item/generated",
    "texture": {
        "layer0": "my_mod:items/example_item"
    }
}
```

我们在这里直接使用原版内建的默认物品模型 `item/generated`，并指定它唯一一层纹理位于 `assets/my_mod/textures/items/example_item.png`。我们只需要把预先准备好的 PNG 格式的纹理放在这个位置就好了。

此时再重启游戏，你应该能在创造模式物品栏中找到一个看起来非常普通的物品了。
