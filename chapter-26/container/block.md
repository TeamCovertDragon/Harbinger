## 方块形式的物品容器

很明显，普通的方块不可能有能力持有任意流体数据——所以你需要一个 `TileEntity`。

```java
// WIP
import net.minecraftforge.fluids.FluidTank;
import net.minecraftforge.fluids.capability.CapabilityFluidHandler;

public class MyFluidTank extends TileEntity {

    private IFluidHandler tank = new FluidTank(8000);

    public boolean hasCapability(Capability<?> capability, EnumFacing direction) {
        return capability == CapabilityFluidHandler.FLUID_HANDLER_CAPABILITY
            || super.hasCapability(capability, direction);
    }

    public <T> T getCapability(Capability<T> capability, EnumFacing direction) {
        return capability == CapabilityFluidHandler.FLUID_HANDLER_CAPABILITY ? this.tank
            || super.getCapability(capability, direction);
    }
}

```
