### 物品概论

#### 你的第一个物品

````java
public class ExampleItem extends Item {
    public ExampleItem() {
        super();
        //同样是不能有大写字母，snake_case
        //此方法的用途很快会提到。
        //此方法返回Item，可组成调用链。
        this.setRegistryName("example_item");
    }
}
````

完。

#### 呃？我看不到我的物品啊？

啊不好意思，忘记注册了。注册的话要走事件。

````java
// 这个注解的意思是“将这个类注册到事件总线中去，该事件监听器属于 mymodid 这个 Mod”
@Mod.EventBusSubsriber(modid = "mymodid")
public final class ItemLoader {

    // 和正常的事件一样，你不需要手动调用此方法！Forge 会自动调用它的。
    @SubscribeEvent
    public static void registerItem(RegistryEvent.Register<Item> event) {
        event.getRegistry().register(new ExampleItem());
    }

}
````

注意，每一个透过此法注册的物品的实例必须都有调用过 `setRegistryName`，否则会抛异常并导致游戏退出。

好了注册了，但是... 创造物品栏里没有你的新物品。当然你可以用 `/give <玩家名> <registry name>` 获得你的新物品，但你不觉得显示在创造模式物品栏里更方便么。

````java
//我们需要一个CreativeTabs的实例，当然那个类下面就有原版的10个，可以直接拿来用，但是你不觉得有一个自己的更方便嘛...
//一般是使用匿名内部类，就像这样。
public static final CreativeTabs EXAMPLE_CREATIVE_TAB = new CreativeTabs("example_tab") {
    //根据Item获得对应的标签图标。
    //当然，如果需要Metadata甚至NBT等数据的话，还有另一个方法可以用
    public Item getIconItem() {
        return Items.DIAMOND;
    }
};

//然后这样就可以了。
//你可以考虑把下面这个东西塞进构造器里
public static Item yourItem = new ExampleItem().setCreativeTab(EXAMPLE_CREATIVE_TAB);
````

#### 呃... 这物品名字不对...

你可能会发现你得到的东西的名字叫 `item.null.name`。这...

````java
public static Item yourItem = new ExampleItem();
yourItem.setCreativeTab(EXAMPLE_CREATIVE_TAB);
//注意此名字和 registry name 不是一个概念。
//这个名字仅用于国际化支持。
//这个方法也返回 Item。
yourItem.setUnlocalizedName("example_mod" + "." + "example_item");
````

然后，是喜闻乐见的语言文件：

 1. 在 `resources` 下新建 `assets` 文件夹
 2. 在 `assets` 下新建文件夹，名字和你的 modid 相同
 3. 在以你的modid命名的文件夹下新建 `lang` 文件夹
 4. 在 `lang` 文件夹下新建 `en_us.lang`：

    ````
    item.example_mod.example_item.name=Example Item
    ````

 5. 等等，如果没理解错，这是英文（美国）的 Locale 啊？  
    OK。然后还是`lang`文件夹，新建`zh_cn.lang`：  

    ````
    item.example_mod.example_item.name=示例物品
    ````

如果你之前有汉化 Mod 的经验，你会发现流程是一致的。

不过为什么要搞这么麻烦呢？直接让它显示中文不就好了。
道理也很简单——如果国外的 Modder 也这么想，那我们还能汉化什么？

#### 享元

你应该注意到了，`Item` 是没有数量的概念的。它实际上只是一个物品种类的标记而已。想想看：

* 一颗钻石和一组钻石都是钻石，只是数量不同。
* 一块铁锭和一块金锭的数量相同，但它们不是同一种物品。

`Item` 代表了物品的“类型”。两颗钻石因为“都是钻石”所以能叠加在一起。两块不同的金属锭因为类型不同所以不能叠加在一起。
有数量概念的是 `ItemStack`，一个 `ItemStack` 的实例代表了“一堆特定物品，数量不定”。Minecraft 中玩家背包里的东西都是 `ItemStack`。玩家手持的也是 `ItemStack`。  
Minecraft 在这里采用了享元的概念：一种特定物品只对应一个 `Item` 实例。换言之，在 Modding 的过程中，我们可以直接通过 `==` 来判断两个物品是否为同一个种类。

#### 对于物品 `Item` 类更深入的理解

既然上文说到了 `Item` 实际上是指类型，那么不妨先来探讨一下物品的“类型”究竟是什么。

笔者认为，相同类型的物品(ItemStack)应该具有以下性质

* 对所有的互动行为有相同的处理逻辑

* 拥有相同的渲染、文本显示逻辑

* 对应的各项属性有相同的处理逻辑

* 拥有相同的修复逻辑

* 在同一个 `CreativeTab` 内

如果读者仔细翻阅了 `net.minecraft.item.Item` 类的话，会发现大多数方法、字段和上面的性质是对应的。

所谓 `Item` ，其实是对所有玩家通过 `ItemStack` 互动行为处理机制的逻辑所存放的类。

所有 `ItemStack` 在被实例化的时候必定指定了一个 `Item` 实例作为它的类型，相应的，该 `ItemStack` 的所有互动行为 (右键、挖掘方块、攻击实体、渲染..etc) 都会通过调用指定的 `Item` 类的方法 (`onItemRightClick`、`onItemUseFinish`、`hitEntity`、`onBlockDestroyed`、`canHarvestBlock` ...etc) 来实现。相应的，这其中绝大多数方法的参数中都会包含一个 (ItemStack) 实例——用于确定该物品的具体状态。

其实，通过复写这些方法，读者当然可以实现绝大部分自己想要的功能——但是十分麻烦，而且对其他mod的兼容性也会受影响(其实就是在造轮子)。

在 3.1 中，笔者将会介绍一些 `Item` 类的子类以及 `Item` 类其他的一些特性，以进一步阐述如何实现 `Item` 类。不过在此之前，请读者确保自己已经对上面所说的东西充分理解了。
