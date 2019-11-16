# 物品形式的流体容器

和方块形式的流体容器一样，物品形式的流体容器也是以 Capability 的形式出现的，惟其拥有一个专门的 `IFluidHandlerItem` 接口。
这个接口在继承 `IFluidHandler` 的基础上多了一个 `getContainerItem` 方法用于获取作为流体容器存在的 `ItemStack`。

你可以在 `net.minecraftforge.fluids.capability.template` 包下找到一些开箱即用的实现，例如下面这个例子所展示的 `FluidHandlerItemStack`：

```java
// WIP
import net.minecraftforge.fluids.capability.templates.FluidHandlerItemStack;

public class MyFluidContainer extends Item {
    public ICapabilityProvider initCapabilities(ItemStack item, NBTTagCompound data) {
        return new FluidHandlerItemStack(item);
    }
}
```

## 重新实现 `IFluidHandlerItem`：`getContainerItem`

`IFluidHandlerItem` 相比于普通的 `IFluidHandler` 只多了一个 `getContainerItem` 方法需要实现，其他方法的含义均和普通的 `IFluidHandler` 一致。

```java
// WIP
@Override
public ItemStack getContainerItem() {
    // 这样一来，这个流体容器就会被认为是“一次性”的。
    // 即，当流体被清空时，它返还的容器是 ItemStack.EMPTY，即空物品。
    // 但请注意，容器返还的具体逻辑是在其他地方完成的，不受这个接口它自己的控制。
    return ItemStack.EMPTY;
}
```
