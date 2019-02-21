## Capability

请仔细回想一下 `TileEntity` 的用途：它可以存储物品、流体甚至是某种形式的能量，同时其他 Mod 也可以通过对应的接口与这个 `TileEntity` 持有的这些东西打交道。

```java
// 假想的情景，不要照抄
TileEntity tile = world.getTileEntity(pos);
if (tile instanceof Something) {
    ((Something)tile).interact(...);
}
```

这些接口自然是没办法统一的，但如果从一个更抽象的角度来看，不难发现：我们总是在检查某个 `TileEntity` 是否实现了某种功能。类似的情况实际上也出现在了 `ItemStack`、`Entity` 甚至是 `World` 和 `Chunk` 上。我们希望这些东西能持有某种特定的信息，并且希望其他 Mod 能通过指定的接口与这些信息打交道。我们可以控制 `TileEntity`，但 `ItemStack` 有 `final` 修饰符，我们不能直接干涉它的行为。更不用说 `World` 和 `Chunk` 是 Minecraft 自己管理的了。  
让我们再审视一遍需求：“让这些对象能持有某种特定的信息”。也就是说，我们只需要让它能持有这些信息，并暴露出一个能操纵这些信息的接口就可以了。至于是通过继承还是组合（Composite）来实现已经无关紧要了。有办法利用这一点来统一吗？接口可能不存在，但数据是不需要任何依赖就可以存在的。  
Capability 系统应运而生。

### 再论流体容器——`ICapabilityProvider`

下面这个例子来自[第二十六章物品形式的流体容器一节](../chapter-26/container/item.md)。

```java
// WIP
import net.minecraftforge.fluids.capability.templates.FluidHandlerItemStack;

public class MyFluidContainer extends Item {
    public ICapabilityProvider initCapabilities(ItemStack item, NBTTagCompound data) {
        return new FluidHandlerItemStack(item);
    }
}
```

注意到这个方法的返回值其实是 `ICapabilityProvider`。它只有这样两个方法：

```java
public interface ICapabilityProvider {
    boolean hasCapability(@Nonnull Capability<?> capability, @Nullable EnumFacing facing);

    @Nullable <T> T getCapability(@Nonnull Capability<T> capability, @Nullable EnumFacing facing);
}
```

似曾相识，不是吗？  
没错，`TileEntity` 这个类已经实现了这个接口。这两个方法的用途也非常明确：根据享元的 `Capability<?>` 对象来确定请求的接口类型（即 `getCapability` 中的 `T`），并按需返回一个具体的实现（或者一个表示存在与否的真值）。  
在 `TileEntity` 中，出于性能考虑<!-- 想想看，漏斗等 TileEntity 每一个 tick 都会请求一次 `IItemHandler`，getCapability 实际上调用频率不低 -->，我们通过一连串 `if` 来决定应该返回什么值。对于 `initCapabilities` 来说，我们也可以做类似的事情，即返回一个自己的 `ICapabilityProvider` 实现。毕竟，它叫 `Provider` 嘛：

```java
public class ConcreteCapabilityProvider implements ICapabilityProvider {
    private final ItemStack theItem;
    public ConcreteCapabilityProvider(ItemStack item) {
        this.theItem = item;
    }

    @Override
    public boolean hasCapability(@Nonnull Capability<?> cap, @Nullable EnumFacing facing) {
        return cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY || cap == CapabilityFluidHandler.FLUID_HANDLER_ITEM_CAPABILITY;
    }

    @Nullable
    @Override
    public <T> T getCapability(@Nonnull Capability<T> cap, @Nullable EnumFacing facing) {
        if (cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY) {
            return ...;
        } else if (cap == CapabilityFluidHandler.FLUID_HANDLER_ITEM_CAPABILITY) {
            return ...;
        } else {
            return null;
        }
    }
}
```
