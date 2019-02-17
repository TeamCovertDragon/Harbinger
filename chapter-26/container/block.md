## 方块形式的物品容器

很明显，普通的方块不可能有能力持有任意流体数据——所以你需要一个 `TileEntity`。  
关于 TileEntity 的内容可参考[第十章的相关内容](../../chapter-10/index.md)。  
关于 `IFluidHandler` 的细节可参考[第二十七章的相关内容](../../chapter-27/built-in/fluid.md)。

```java
// WIP
import net.minecraftforge.fluids.FluidTank;
import net.minecraftforge.fluids.capability.CapabilityFluidHandler;

public class MyFluidTank extends TileEntity {

    // 我们直接复用 Forge 自带的 FluidTank 实现，它可以满足 90% 的需求。
    private IFluidHandler tank = new FluidTank(8000);

    public boolean hasCapability(Capability<?> capability, EnumFacing direction) {
        return capability == CapabilityFluidHandler.FLUID_HANDLER_CAPABILITY
            || super.hasCapability(capability, direction);
    }

    public <T> T getCapability(Capability<T> capability, EnumFacing direction) {
        if (capability == CapabilityFluidHandler.FLUID_HANDLER_CAPABILITY) {
            return CapabilityFluidHandler.FLUID_HANDLER_CAPABILITY.cast(this.tank);
        } else {
            return super.getCapability(capability, direction);
        }
    }
}

```
