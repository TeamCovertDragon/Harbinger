# 自定义 Capability

显然 Forge 内置的数个 Capability 满足不了我们的需求，我们不可避免地会遇到需要存储我们自己的数据的情况。

## 从一个接口开始

首先我们需要一个能描述需要保存的数据的接口。

```java
public interface MyData {
    String getData();

    void setData(final String data);
}
```

## Capability 的注册

在 `FMLPreInitializationEvent`（即 pre-init 阶段）的时候注册：

```java
CapabilityManager.INSTANCE.register(MyMyData.class, new MyMyData.DefaultSerializer(), MyMyDataProvider::new);
```

然后我们需要一个 `Capability<MyData>` 的字段，它的用处会在后文中出现。
<!-- TODO：How the heck should we describe this? Flyweight? -->
```java
public final class MyCapabilities {
    @CapabilityInject(MyData.class) // 这个注解会令 Forge 将正确的值放这个字段里。
    public static Capability<MyData> MY_DATA_CAP;
}
```

## `new MyData.DefaultSerializer()` 和 `IStorage` 的存在意义

`register` 第二个参数传入的是“能序列化 `MyData` 的 `IStorage` 实现”的一个实例。

`IStorage` 代表了某个 Capability 默认的序列化方式。然而，实际使用当中，少有需要用到它的场景。它存在的意义也仅仅是在完全无法确定如何正确保存 Capability 的数据时“留的一手”而已。

以防万一，我们需要一个 `IStorage` 实现，虽然我们可能完全用不上。

```java
public interface MyData {
    String getData();

    void setData(final String data);

    public static final class DefaultSerializer implements Capability.IStorage<MyData> {
        @Nullable
        @Override
        public NBTBase writeNBT(Capability<MyData> cap, MyData instance, EnumFacing side) {

        }

        @Override
        public void readNBT(Capability<MyData> cap, MyData instance, EnumFacing side, NBTBase nbt) {

        }
    }
}
```

## `MyDataProvider::new` 的意义、`ICapabilityProvider` 与抽象工厂

`register` 第三个参数传入的是“能提供 `MyData` 的 `ICapabilityProvider` 的缺省实现”的工厂。

`ICapabilityProvider` 代表了持有若干个 Capability 的“容器”。这样的容器可以是简单的对 `MyCapability` 的一个包装，可以是 `TileEntity`，可以是 `Entity`，可以是 `ItemStack`，甚至是持有若干个别的 `ICapabilityProvider` 的 `CapabilityDispatcher`<!-- Composite + Chain of Responsibility + Proxy -->。你可以查询这个容器是否支持某种 Capability（`hasCapability`），也可以通过它拿到某个 Capability 的实例（`getCapability`）。

现在我们需要这样一个 `ICapabilityProvider` 的实现，这个实现持有 `MyData` 的实例。
有鉴于 `MyData` 是个接口，所以我们并不需要在意它是怎么实现的——只要合乎你定下的规则即可。  
这样一个 `ICapabilityProvider` 的实现充当了 `MyData` 的容器的角色。

```java
public class MyDataProvider implements ICapabilityProvider {
    private MyData instance = ...;

    @Override
    public boolean hasCapability(Capability<?> cap, EnumFacing facing) {
        return cap == MyCapabilities.MY_DATA_CAP;
    }

    @Override
    public <T> T getCapability(Capability<T> cap, EnumFacing facing) {
        return cap == MyCapabilities.MY_DATA_CAP ? MyCapabilities.MY_DATA_CAP.cast(this.instance) : null;
    }
}
```
