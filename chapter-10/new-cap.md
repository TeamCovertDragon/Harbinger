# 你自己的 Capability

假如说你有一套你自己的能源系统：

```java
import java.math.BigInteger;

public interface PowerContainer {
    void setPower(BigInteger power);
    BigInteger getPower();
}
```

显然这种附加在物品或者 TileEntity 上的额外功能可以使用 Capability 暴露出来。
首先，这个 Capability 需要注册。注册 Capability 的过程比较复杂，它需要三样东西：

  - Capability 对应的接口的类。在这个例子中，是 `PowerContainer.class`。
    虽然说是“接口”，但它实际上可以不是 `interface`，也可以是 `abstract class`。
  - 一个 `IStorage<...>` 的实现。它的意义是“在完全没有其他信息的情况下（反）序列化这个接口需要保存的数据”。
    虽然是强制要求实现的，但如果你的数据是通过别的方式保存的，可以通过留空实现的方式（`writeNBT` 返回 `new NBTTagByte(0)`，`readNBT` 留空）来
  - 一个 `Callable<...>` 的实现，可以使用 lambda expression。它用作“提供这个接口的默认实现的工厂方法”。
    非常不建议在这里返回 `null` 或抛出异常。建议使用所谓的 Null Pattern 配合 Singleton。

```java
@Mod.EventHandler
public void preInit(FMLPreInitializationEvent event) {
    CapabilityManager.INSTANCE.register(PowerContainer.class, new Capability.IStorage<PowerContainer>() {
        @Override
        public NBTBase writeNBT(Capability<PowerContainer> cap, PowerContainer instance, EnumFacing side) {
            return new NBTTagString(instance.getPower().toString());
        }
        @Override
        public void readNBT(Capability<PowerContainer> cap, PowerContainer instance, EnumFacing side, NBTBase data) {
            if (data instanceof NBTTagString) {
                instance.setPower(new BigInteger(((NBTTagString)data).getString()));
            }
        }
    }, () -> new PowerContainer() {
        private BigInteger power = BigInteger.ZERO;
        @Override
        public void setPower(BigInteger power) {
            this.power = power;
        }
        @Override
        public BigInteger getPower() {
            return this.power;
        }
    });
}
```
