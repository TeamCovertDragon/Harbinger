# `ITickable` 与 `TileEntity`

TileEntity 的刷新功能由 `ITickable` 接口提供。TileEntity 默认是没有刷新功能的，你必须实现 `ITickable` 接口方能获得1秒20次的刷新能力。

## Tick

Tick 是很多游戏中的最小时间单位的名字，但具体定义是取决于实际情况的。
想象一下时间的流逝：你在读这些文本时大概没什么别的事发生，但时间依旧在走。Tick 的概念也是一样：你在游戏中可能什么都不做，但游戏内的时间依旧在走。时间流逝这个动作通常被称为 Ticking（没错，tick 突然从名词变成了动词），和“刷新”意思相近。  
通常，实现这个效果的方式是一个可以跳出的无限循环。

```java
// 在我们点退出游戏，或者服务器输入 /stop 命令时，
// 这个 keepGameOn 就会变成 false。
while (keepGameOn) {
    // 这个 doGameLogic 里包含了各种各样的东西：
    // 实体刷新、方块随机刷新、TileEntity 刷新、
    // 天气更新、向各个客户端发包、……
    // 我们根据当前游戏的状态来决定游戏接下来会
    // 变成什么样子（另见：时序电路）。
    doGameLogic();
}
```

Minecraft 中 1 秒最多刷新 20 次。它大约是这样实现的：

```java
while (keepGameOn) {
    for (int i = 0; i < 20; i++) {
        // 记录这个 tick 开始的时间
        long start = System.currentTimeMillis();
        doGameLogic();
        // 根据结束时间，计算这个 tick 的实际耗时
        long timeElapsed = System.currentTimeMillis() - start;
        // 若这个 tick 耗时低于 50 毫秒，则通过 Thread.sleep 补足至 50 毫秒
        if (timeElapsed < 50) {
            Thread.sleep(50 - timeElapsed);
        }
    }
}
```

有鉴于此，Minecraft 的游戏刻 (Gametick) 的精确定义为“最理想状态下，1 秒 = 20 tick”。  
与此同时出现的还有 TPS（Tick Per Second，每秒刻数）的概念。相应的，在 Minecraft 的世界中，TPS 达到最大值 20 时才是正常的。TPS 低于 20 时，1 秒也将不再是 20 tick，游戏逻辑的执行也会慢下来（以耗时长的方式呈现）。

```java
public final class MyLavaFurnaceEntity extends TileEntity implements ITickable {

    private int progress;

    @Override
    public void update() {
        // 所有的逻辑全部在这里发生…… 至于这里会发生什么完全要看
        // 想象力。
        // 这也是 Minecraft 的魅力之一——由原版熔炉所产生的“操纵
        // 物品”的概念，产生了无数机器，为科技主题 Mod 提供了
        // 理论可能。

        // 如上文所述，只要这个 TileEntity 还存在于这个世界上，
        // 这个 TileEntity 的 update 方法每一个 tick 就都会
        // 被调用一次。我们不应也没有必要在这里写死循环。
        if (!world.isRemote && fuel > 0 && ++progress > 200) {
            for (EntityItem entity : this.world.getEntitiesWithinAABB(EntityItem.class, new AxisAlignedBB(this.pos.up()))) {
                final ItemStack result = FurnaceRecipes.instance().getSmeltingResult(entity.getItem());
                if (!result.isEmpty()) {
                    fuel--;
                    entity.setItem(result.copy());
                    if (fuel <= 0) {
                        break;
                    }
                }
            }
        }
    }

    public ItemStack tryAcceptFuel(ItemStack fuel) {
        return fuel.getItem() == Items.LAVA_BUCKET ? ItemStack.EMPTY : fuel;
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

## `ITickable` 并非必须

只是为了存储数据的话，不需要实现 `ITickable` 接口，这样可以节约资源。
