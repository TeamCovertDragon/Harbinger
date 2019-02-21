## 物品形式的流体容器

```java
// WIP
import net.minecraftforge.fluids.capability.templates.FluidHandlerItemStack;

public class MyFluidContainer extends Item {
    public ICapabilityProvider initCapabilities(ItemStack item, NBTTagCompound data) {
        return new FluidHandlerItemStack(item);
    }
}
```
