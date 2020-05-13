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
    // 我们指定 0 号槽为输入，1 号槽为输出。
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
  - 出于性能考虑，实现 `getCapability` 的一般方式是 `if-else if-else` 到底。

## 区分输入和输出

然而目前的例子还是有问题。我们很快就会发现：通过使用漏斗等设备，我们指定的输出格也可以放进去物品。我们不能直接将 `ItemStackHandler` 暴露出来，而是通过一个“中间人”来完成。将 `getCapability` 方法改成这样：

```java
@Override
public <T> T getCapability(Capability<T> cap, EnumFacing facing) {
    if (cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY) {
        return CapabilityItemHandler.ITEM_HANDLER_CAPABILITY.cast(new IItemHandler {
            @Override
            public int getSlots() {
                return inventory.getSlots();
            }
            @Override
            public ItemStack getStackInSlot(int slot) {
                return inventory.getStackInSlot(slot);
            }
            @Override
            public ItemStack insertItem(int slot, @Nonnull ItemStack stack, boolean simulate) {
                // 仅当对 0 号槽位尝试输入物品时允许通过，否则拒绝输入
                if (slot == 0) {
                    return inventory.insertItem(0, stack, simulate);
                }
                return stack;
            }
            @Override
            public ItemStack extractItem(int slot, int amount, boolean simulate) {
                // 仅当对 1 号槽位尝试提取物品时允许听过，否则拒绝提取
                if (slot == 1) {
                    return inventory.extractItem(1, amount, simulate);
                }
                return ItemStack.EMPTY;
            }
            @Override
            public int getSlotLimit(int slot) {
                return inventory.getSlotLimit(slot);
            }
            @Override
            public boolean isItemValid(int slot, ItemStack stack) {
                // 我们只允许 0 号槽位输入物品。
                // 1 号槽位中的物品不能通过自动化手段输入，必须由我们自己控制。
                return slot == 0;
            }
        });
    } else {
        return super.getCapability(cap, facing);
    }
}
```

## 破坏方块后掉落物品

最后我们需要考虑一下这样一个问题：挖掉这个方块之后，我们的 `inventory` 里的东西怎么办？  
原版箱子的解决方法是掉落。我们这里也仿照原版箱子的处理方式，来让我们内部物品栏里的东西在方块本体被挖掉后掉出来：

```java
public final class MyLavaFurnace extends Block {
    // 省略之前的写的方法

    @Override
    public void breakBlock(World world, BlockPos pos, IBlockState state) {
        final TileEntity tile = world.getTileEntity(pos);
        if (tile instanceof MyLavaFurnaceEntity) {
            final ItemStackHandler inv = ((MyLavaFurnaceEntity)tile).inventory;
            for (int i = 0; i < inv.getSlots(); i++) {
                InventoryHelper.spawnItemStack(world, pos.getX(), pos.getY(), pos.getZ(), inv.getStackInSlot(i));
            }
        }
        super.breakBlock(world, pos, state);
    }
}
```
