## TileEntity

Tile 这个单词的本意是“瓷砖”或“瓦砖”。实际上这里它和 Block 是一个意思。所以它是一个给方块用的概念。实际上 Mojang 很早（大约 1.8 的时候）就给它改名 BlockEntity 了。  

### 为什么要有 TileEntity？
TileEntity 是一种和实体类似，可以存储一个 NBT 标签的特殊对象，这意味着你可以在一个方块中存储超过 4 bits 的数据。  
TileEntity 也可以像 Entity 一样，通过实现 `ITickable` 来获得每秒刷新 20 次的能力。  
可以说，绝大部分看上去功能异常强大的方块都是基于 TileEntity 的。

### 起点：声明该 Block 持有 TileEntity

```java
public class MyMachineBlock extends Block {
    @Override
    public boolean hasTileEntity(IBlockState state) {
        return true;
    }

    @Override
    public TileEntity createTileEntity(World world, IBlockState state) {
        return new MyMachine();
    }
}
```

### TileEntity 的注册

TileEntity 虽然需要注册，但它并不走 Forge 的注册表系统。一个比较常见的做法是在注册方块的时候一并注册 TileEntity。

```java
@SubscibreEvent
public static void onBlockRegistration(RegistryEvent.Register<Block> event) {
    // 第一个参数是你要注册的 TileEntity 对应的 class 对象。必须 extends TileEntity。
    // 第二个参数的要求和方块的注册名一致。
    GameRegistry.registerTileEntity(MyMachine.class, new ResourceLocation("my_mod", "my_machine"));
}
```

### 逻辑

然后就是要补全那个 `MyMachine` 类了。

```java
public class MyMachine extends TileEntity {
    //注意，虽然TileEntity本身是abstract，但它并没有任何抽象方法。
    public MyMachine() {
        //这个构造器是MinecraftForge加的。
        //加这个构造器的原因后面会讲。
        //Minecraft本身并未为TileEntity显式定义任何构造器。
        //由于MinecraftForge给它塞的构造器依旧是零参，爱省略就省略吧 - -
        super();
    }

    @Override
    public void readFromNBT(NBTTagCompound tagCompound) {
        //从NBT标签中读数据。
        //TileEntity被反序列化的关键一环
        //务必调用super.readFromNBT
        super.readFromNBT(tagCompound);
        //在调用super后务必读取你必须的数据
    }

    @Override
    public NBTTagCompound writeToNBT(NBTTagCompound tagCompound) {
        //向NBT标签中写数据
        //TileEntity被序列化的关键一环
        //在返回前务必写入必须的数据
        //永远要返回super.writeToNBT(tagCompouond)
        return super.writeToNBT(tagCompound);
    }

}
```

### `ITickable`

TileEntity 的刷新功能由 `ITickable` 接口提供。TileEntity 默认是没有刷新功能的，你必须实现 `ITickable` 接口方能获得1秒20次的刷新能力。

这里有必要解释一下 tick 的概念。Tick 是很多游戏中的最小时间单位的名字，但具体定义是取决于实际情况的。想象一下时间的流逝：你在读这些文本时大概没什么别的事发生，但时间依旧在走。Tick 的概念也是一样：你在游戏中可能什么都不做，但游戏内的时间依旧在走。时间流逝这个动作通常被称为 Ticking（没错，tick 突然从名词变成了动词），和“刷新”意思相近。  
Minecraft 中 1秒最多刷新 20 次。因此，Minecraft 的游戏刻 (Gametick) 的精确定义为“最理想状态下，1 秒 = 20 tick”。之所以不是 1 tick = 0.05 秒，是因为 TPS (tick per second) 概念的存在；TPS 低于 20 时，1 秒也将不再是 20 tick。TPS 最高为 20 也是因为这个。

````java
public class MyMachine extends TileEntity implements ITickable {
    //省略几千字
    @Override
    public void update() {
        //所有的逻辑全部在这里发生...
        //至于这里会发生什么完全要看想象力。
        //这也是Minecraft的魅力之一。
        //由Furnace所产生的ItemStack Manipulation的概念
        //产生了无数机器

        //另，因为蛋疼Minecraft不是多线程的，
        //所以这里的操作全部都在Minecraft的主线程上发生...
        //所以，不要试图在这里搞什么while(true)之类的死循环。
    }
}

````

只是为了存储数据的话，不需要实现 `ITickable` 接口，这样可以节约资源。
