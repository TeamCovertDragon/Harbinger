# TileEntity

Tile 这个单词的本意是“瓷砖”或“瓦砖”。实际上这里它和 Block 是一个意思。所以它是一个给方块用的概念。
实际上 Mojang 很早（大约 1.8 的时候）就给它改名“Block Entity”了。
出于统一术语的考虑，本教程仍然主要使用“TileEntity”这个称呼，但仍会在特定情况下使用“BlockEntity”或它的中文译名“方块实体”。三种称呼在本教程中指代的是同一概念。

## 为什么要有 TileEntity？

TileEntity 是一种和[实体](../chapter-08/index.md)类似，可以存储一个 NBT 标签的特殊对象。
这意味着，借助 TileEntity，你可以在一个方块中存储超过 4 bits 的数据。  
TileEntity 可以通过[实现 `ITickable` 接口](ticking.md)来获得每秒刷新 20 次的能力。  
可以说，绝大部分看上去功能异常强大的方块都是基于 TileEntity 的。

## 起点：声明该 Block 持有 TileEntity

```java
public final class MyLavaFurnace extends Block {

    public MyLavaFurnace() {
        super(Material.ROCK, MapColor.LIGHT_BLUE);
    }

    @Override
    public boolean hasTileEntity(IBlockState state) {
        return true;
    }

    @Override
    public TileEntity createTileEntity(World world, IBlockState state) {
        return new MyLavaFurnaceEntity();
    }
}

public final class MyLavaFurnaceEntity extends TileEntity {
    // 暂时什么都没有
}
```

## TileEntity 的注册

TileEntity 虽然需要注册，但它并不走 Forge 的注册表系统，反映在下面两点上：

  - `TileEntity` 本身并不是享元对象，也因此不实现 `IForgeRegistryEntry<TileEntity>`。
  - `TileEntity` 的注册通过 `GameRegistry.registerTileEntity` 方法完成。

一个比较常见的做法是在注册方块的时候一并注册 TileEntity。

```java
@SubscribeEvent
public static void onBlockRegistration(RegistryEvent.Register<Block> event) {
    event.getRegistry().register(new MyLavaFurnace().setRegistryName("my_mod", "lava_furnace"));
    // 第一个参数是你要注册的 TileEntity 对应的 class 对象。必须 extends TileEntity。
    // 第二个参数的要求和方块的注册名一致。
    GameRegistry.registerTileEntity(MyLavaFurnaceEntity.class, new ResourceLocation("my_mod", "lava_furnace"));
}
```

## 持有数据

`TileEntity` 最大的用途在于持有超过 4 bit 的数据，它的数据由一个专门的 `NBTTagCompound` 保存。

```java
public final class MyLavaFurnaceEntity extends TileEntity {

    private int progress;
    private int fuel;

    // 注意，虽然 TileEntity 本身是抽象类，但它并没有任何抽象方法。
    // 有鉴于 Minecraft 反序列化 TileEntity 的方式，TileEntity 必须要有 public 的
    // 零参构造器。这里我们省略构造器不写，即使用隐式声明的默认构造器。

    @Override
    public void readFromNBT(NBTTagCompound tag) {
        // 从 NBT 标签中读数据，以反序列化 TileEntity
        // 一定要调用 super.readFromNBT
        super.readFromNBT(tag);
        this.progress = tag.getInteger("Progress");
        this.fuel = tag.getInteger("Fuel");
    }

    @Override
    public NBTTagCompound writeToNBT(NBTTagCompound tag) {
        // 向 NBT 标签中写数据，以序列化 TileEntity
        // 在返回前务必写入必须的数据
        // 一定要调用 super.writeToNBT(tag)
        tag.setInteger("Progress", this.progress);
        tag.setInteger("Fuel", this.fuel);
        return super.writeToNBT(tag);
    }

}
```

## 我们还需要再深入些

现在我们稍微改造一些 `MyLavaFurnace`，使之可以通过右击的方式接受熔岩作为燃料：

```java
public final class MyLavaFurnace extends Block {

    public MyLavaFurnace() {
        super(Material.ROCK, MapColor.LIGHT_BLUE);
    }

    @Override
    public boolean hasTileEntity(IBlockState state) {
        return true;
    }

    @Override
    public TileEntity createTileEntity(World world, IBlockState state) {
        return new MyLavaFurnaceEntity();
    }

    @Override
    public boolean onBlockActivated(World world, BlockPos pos, IBlockState state, EntityPlayer player, EnumHand hand, EnumFacing facing, float hitX, float hitY, float hitZ) {
        TileEntity tile = world.getTileEntity(pos);
        if (tile instanceof MyLavaFurnaceEntity && !world.isRemote) {
            player.setHeldItem(hand, ((MyLavaFurnaceEntity)tile).tryAcceptFuel(player.getHeldItem(hand)));
            player.sendStatusMessage(new TextComponentString("Fuel: " + ((MyLavaFurnaceEntity) tile).getFuel()), true);
            return true;
        }
        return false;
    }
}
```

```java
public final class MyLavaFurnaceEntity extends TileEntity {

    private int progress;
    private int fuel;

    /**
     * @param fuel 玩家当前手持的物品（见 MyLavaFurnace.onBlockActivated）
     * @return 尝试消耗燃料后的剩余物品，有可能仍然是未经修改的 fuel 实例
     */
    public ItemStack tryAcceptFuel(ItemStack fuel) {
        if (fuel.getItem() == Items.LAVA_BUCKET) {
            this.fuel += 1000;
            return new ItemStack(Items.BUCKET);
        } else {
            return fuel;
        }
    }

    public int getFuel() {
        return this.fuel;
    }

    @Override
    public void readFromNBT(NBTTagCompound tag) {
        super.readFromNBT(tag);
        this.progress = tag.getInteger("Progress");
        this.fuel = tag.getInteger("Fuel");
    }

    @Override
    public NBTTagCompound writeToNBT(NBTTagCompound tag) {
        tag.setInteger("Progress", this.progress);
        tag.setInteger("Fuel", this.fuel);
        return super.writeToNBT(tag);
    }

}
```
