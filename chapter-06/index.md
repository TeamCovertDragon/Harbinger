## 注册表

Minecraft 是一个处处充满了注册的游戏，因为…… 这个游戏里充满了各种各样奇奇怪怪的对象。

### 事件驱动的注册

Forge 引入了一个注册表系统以接管原版的注册表系统。Forge 的系统与原版的系统相比多了 ID 自动分配、映射存储、映射重定义等好用的功能。其中映射存储更是使得跨整合的存档分享成为了可能。此前带 Mod 的存档的分享往往需要连带整合一起发布，而现在的话只需要给出必须的 Mod 列表就可以了，不需要发布整个整合。  

````java
// 注意这里的泛型参数。它的边界是 T extends IForgeRegistryEntry<T>，
// 意味着所有注册项都可以用这个方法来完成注册
//
// 泛型事件的使用决定了你需要对每一种对象的注册都写一个订阅。你不能向方块的注册表里
// 注册物品，反之亦然。
@SubscribeEvent
public static void onRegistry(RegistryEvent.Register<Block> regBlock) {
    regBlock.getRegistry().register(myBlock);
}
````

### 注册项？
刚才说到了“注册项”这个概念。
那么我们来总结一下吧，需要通过标准化的注册表进行注册的东西有：

 * 物品（Item）
 * 方块（Block）
 * 附魔（Enchantment）
 * 药水（Potion）
 * 生物群系 (Biome)
 * 声音事件 (SoundEvent)
 * 村民的职业（VillagerProfession）
 * 实体（以 EntityEntry 的形式出现）
 * ~~原版工作台合成（IRecipe）~~

 这些东西会在后面的章节中一一讲到。  
 原版工作台合成的确也是受注册表控制的，但大多数时候你不应该通过这个方式注册原版工作台合成，而是写 JSON。相关细节在第二十章中有详细阐述。

### 结束了？
非也。还有一个十分有趣的注解： `@GameRegistry.ObjectHodler`。

````java
import javax.annotation.Nonnull;

// 本范例改编自 ForgeDocs 的对应案例
// 标记在主类上的 ObjectHolder 指定了默认的 namespace，即 ResourceLocation 中
// 构造器的第一个参数。
@ObjectHolder("minecraft")
public class MyStuffHolder {
    /*
     * 被注入的 Field 要求是：
     * public，静态，<T extends IForgeRegistryEntry.Impl<T>>。
     * final 可有可无。若使用 final，可以初始化为 Blocks.AIR 以避开 IDE 的 null 检查。
     */
    @ObjectHolder("beacon")
    public static final Block VANILLA_BEACON = Blocks.AIR;

    /*
     * 这样也可以，前提是：
     *   1. 所在类上有 ObjectHolder 注解，比如这个类。类的注解中的参数会用作 namespace。
     *   2. 字段名会用作 path，所以必须与某个物品匹配。
     *  查询时会统一转化为小写字母，所以可以放心使用传统的大写下划线常量命名。
     * 这样得到的物品便是 minecraft:iron_hoe。
     */
    public static final Item IRON_HOE = Items.AIR;

    //或者你还可以这样覆盖标记在类上的domain
    //依旧是参考 ResourceLocation 的构造器，这次参考一个 String 参数的构造器
    @ObjectHolder("forestry:grafter")
    public static final Item FORESTRY_GRAFTER = Items.AIR;
}

//或者你可以不在类上加@ObjectHolder：
public class MyStuffHolder2 {
    //直接这样也可以
    @ObjectHolder("theoneprobe:probe")
    public static final Item THEONEPROBE_PROBE = null;
}
````

虽然注册事件的触发顺序是未定义的，但有一个例外：根据 ForgeDocs，方块注册总是在物品注册之前。目前 MinecraftForge 本身所有的 test 都可以印证此约定。

### 复用注册表

所以你也可以使用 `IForgeRegistry` 来让其他 Mod 注册你自己的 Mod 的东西。

```java
// 待补
```
