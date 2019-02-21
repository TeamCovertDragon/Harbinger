## 动画状态机

正如第十二章提到的那样，动画状态机是以 Capability 的形式附加在某个 `ICapabilityProvider` 上的。因此它的使用方式和其他基于 Capability 的接口无异。  
下面的例子展示了如何给某一个物品同时追加 `IItemHandler`、`IFluidHandlerItem` 和 `IAnimationStateMachine` 三个 Capability：

```java

public class AdventureBackpack extends Item {

    public ICapabilityProvider initCapabilities(ItemStack item, NBTTagCompound data) {
        return new CapProvider();
    }

    public static final class CapProvider implements ICapabilityProvider {
        // TODO
    }    
}

```
