# 实例：透过 Capability 系统提供物品存储服务

让我们回到上一章写的 `MyLavaFurnaceEntity` 上去。很明显，我们可以让它拥有缓存输入和输出的能力，
而这种内部的物品存储（“inventory”）功能可以通过 Capability 系统暴露出来。

为此，我们需要实现 Forge 内建的四种 Capability 接口之一——`IItemHandler`，并通过 Capability 系统暴露出来。
有鉴于 `TileEntity` 已经实现了 `ICapabilityProvider`，我们只需要重载 `ICapabilityProvider` 的两个方法：

  - `hasCapability`
  - `getCapability`

就可以了。

Forge 提供了一个标准的 `IItemHandler` 的实现：`ItemStackHandler`，我们可以直接拿来用。
那么让我们试着改造一下 `MyLavaFurnaceEntity`……

```java
public final class MyLavaFurnaceEntity extends TileEntity implements ITickable {

    private int progress;
    private int fuel;
    // 2 代表“我们需要两个槽位”——一个放输入，一个放输出。
    private final ItemStackHandler inventory = new ItemStackHandler(2);

    @Override
    public void update() {
        if (!world.isRemote) {
            // IItemHandler 的下标从 0 开始。
            if (inventory.getStackInSlot(0).isEmpty()) {
                for (EntityItem entity : this.world.getEntitiesWithinAABB(EntityItem.class, new AxisAlignedBB(this.pos.up()))) {
                    final ItemStack result = FurnaceRecipes.instance().getSmeltingResult(entity.getItem());
                    if (!result.isEmpty()) {
                        // insertItem 的第一个参数是要放入的物品
                        // 第二个参数是目标槽位的编号
                        // 第三个参数指定“该操作是不是仅模拟”（simulate/dry-run）。
                        // 若第三个参数为 true，则该操作不会真的把物品放入物品栏中。
                        // 常用于测试目标物品栏。
                        entity.setItem(inventory.insertItem(0, entity.getItem(), false));
                        progress = 0;
                        break;
                    }
                }
            } else {
                --fuel;
                ++progress;
                if (progress > 200) {
                    inventory.insertItem(1, FurnaceRecipes.instance().getSmeltingResult(inventory.getStackInSlot(0).copy(), false));
                }
            }
        }
    }

    public ItemStack tryAcceptFuel(ItemStack fuel) {
        return fuel.getItem() == Items.LAVA_BUCKET ? ItemStack.EMPTY : fuel;
    }

    public int getFuel() {
        return this.fuel;
    }

    @Override
    public boolean hasCapability(Capability<?> cap, EnumFacing facing) {
        return cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY || super.hasCapability(cap, facing);
    }

    @Override
    public <T> T getCapability(Capability<T> cap, EnumFacing facing) {
        if (cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY) {
            return CapabilityItemHandler.ITEM_HANDLER_CAPABILITY.cast(this.inventory);
        } else {
            return super.getCapability(cap, facing);
        }
    }

    @Override
    public void readFromNBT(NBTTagCompound tag) {
        super.readFromNBT(tag);
        this.progress = tag.getInteger("Progress");
        this.fuel = tag.getInteger("Fuel");
        this.inventory.deserializeNBT(tag.getCompoundTag("Inventory"));
    }

    @Override
    public NBTTagCompound writeToNBT(NBTTagCompound tag) {
        tag.setInteger("Progress", this.progress);
        tag.setInteger("Fuel", this.fuel);
        tag.setTag("Inventory", this.inventory.serializeNBT());
        return super.writeToNBT(tag);
    }


}
```

## 一些细节

实现 `hasCapability` 和 `getCapability` 的时候有一些细节问题需要注意：

  - 需要保证在 `hasCapability` 返回 `true` 时，用一样的参数调用 `getCapability` 不能返回 `null`。
  - 出于性能考虑，`getCapability` 的实现建议一路 `if-else if-else` 到底。
