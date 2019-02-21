## `IEnergyStorage`

<!-- TODO 说明。讲道理我也不知道怎么说明啊。 -->

```java
// TODO 解释
import net.minecraftforge.energy.EnergyStorage;

public class MyFurnace extends TileEntity {

    // 直接复用参考实现。10000 代表容积 10000 单位。
    private final EnergyStorageenergy = new EnergyStorage(10000);

    @Override
    public boolean hasCapability(Capability<?> cap, EnumFacing facing) {
        return cap == CapabilityEnergy.ENERGY ? true : super.hasCapability(cap, facing);
    }

    @Override
    public <T> T getCapability(Capability<T> cap, EnumFacing facing) {
        return cap == CapabilityEnergy.ENERGY ? energy : super.getCapability(cap, facing);
    }
}
```
