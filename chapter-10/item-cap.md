# 实例：透过 Capability 系统提供物品存储服务

让我们回到上一章写的 `MyLavaFurnaceEntity` 上去。很明显，我们可以让它拥有缓存输入和输出的能力，
而这种内部的物品存储（“inventory”）功能可以通过 Capability 系统暴露出来。

为此，我们需要实现 Forge 内建的四种 Capability 接口之一——`IItemHandler`，并通过 Capability 系统暴露出来。
有鉴于 `TileEntity` 已经实现了 `ICapabilityProvider`，我们只需要重载 `ICapabilityProvider` 的两个方法：

  - `hasCapability`
  - `getCapability`

就可以了。

```java
public final class MyLavaFurnaceEntity {
    @Override
    public boolean hasCapability(Capability<?> cap, EnumFacing facing) {
        return cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY || super.hasCapability(cap, facing);
    }

    @Override
    public <T> T getCapability(Capability<T> cap, EnumFacing facing) {
        if (cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY) {
            return new IItemHandler {
                @Override
                public int getSlots() {

                }
                @Override
                public ItemStack getStackInSlot(int slot) {

                }
                @Override
                public int getSlotLimit(int slot) {

                }
                @Override
                public ItemStack insertItem(int slot, ItemStack toInsert, boolean simulate) {

                }
                @Override
                public ItemStack extractItem(int slot, int maxSize, boolean simulate) {

                }
            };
        } else {
            return super.getCapability(cap, facing);
        }
    }
}
```

## 一些细节

实现 `hasCapability` 和 `getCapability` 的时候有一些细节问题需要注意：

  - 需要保证在 `hasCapability` 返回 `true` 时，用一样的参数调用 `getCapability` 不能返回 `null`。
  - 出于性能考虑，`getCapability` 的实现建议一路 `if-else if-else` 到底。
