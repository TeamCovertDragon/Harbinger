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
有数量概念的是 `ItemStack`，一个 `ItemStack` 的实例代表了“一堆特定物品，数量不定”。
Minecraft 中玩家背包里的东西都是 `ItemStack`。玩家手持的也是 `ItemStack`，只是一部分具体的逻辑是写进 `Item` 里的。Minecraft 在这里采用了享元的概念：一种特定物品只对应一个 `Item` 实例。换言之，在 Minecraft 中，`Item` 实例之间是可以通过 `==` 直接比较的。
