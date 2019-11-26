# `ITickable` 与 `TileEntity`

TileEntity 的刷新功能由 `ITickable` 接口提供。TileEntity 默认是没有刷新功能的，你必须实现 `ITickable` 接口方能获得1秒20次的刷新能力。

## Tick

Tick 是很多游戏中的最小时间单位的名字，但具体定义是取决于实际情况的。想象一下时间的流逝：你在读这些文本时大概没什么别的事发生，但时间依旧在走。Tick 的概念也是一样：你在游戏中可能什么都不做，但游戏内的时间依旧在走。时间流逝这个动作通常被称为 Ticking（没错，tick 突然从名词变成了动词），和“刷新”意思相近。  
Minecraft 中 1 秒最多刷新 20 次。因此，Minecraft 的游戏刻 (Gametick) 的精确定义为“最理想状态下，1 秒 = 20 tick”。之所以不是 1 tick = 0.05 秒，是因为 TPS (tick per second) 概念的存在；TPS 低于 20 时，1 秒也将不再是 20 tick。TPS 最高为 20 也是因为这个。

```java
public final class MyLavaFurnaceEntity extends TileEntity implements ITickable {

    private int progress;

    private ItemStack input = ItemStack.EMPTY;
    private ItemStack output = ItemStack.EMPTY;

    @Override
    public void update() {
        // 所有的逻辑全部在这里发生……
        // 至于这里会发生什么完全要看想象力。
        // 这也是Minecraft的魅力之一。
        // 由Furnace所产生的ItemStack Manipulation的概念
        // 产生了无数机器

        // 另，因为 Minecraft 不是多线程的，请不要试图在这里写死循环，
        // 那样会导致游戏假死。
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
