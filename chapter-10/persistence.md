# Capability 数据的持久化

Capability 并没有解决数据的持久化的问题。  
准确地来说，Capability 系统只有一个 `IStorage` 可以提供缺省缺省状态下的数据持久化，但它不一定适用于所有的情况。
在 Forge 内部使用的 `CapabilityDispatcher` 中，它会检查每一个收集到的 `ICapabilityProvider` 是否还实现了 `INbtSerializable`，并在识别到 `INbtSerializable` 时使用它来序列化对应的 `ICapabilityProvider`。但即便如此，这也是 `CapabilityDispatcher` 自行实现的序列化机制，而非 Capability 系统的特性。

一言以蔽之，若不是通过 `AttachCapabilitiesEvent` 追加到别的地方的 Capability，我们需要自行解决数据保存的问题。

## `TileEntity`

我们可以直接把数据写入 `TileEntity` 自己的 NBT 中。

```java
@Override
public NBTTagCompound writeToNBT(NBTTagCompound data) {
    data.setTag("something", this.capObject.serializeNBT());
    data.setInteger("another_thing", this.someEnergy);
    return super.writeToNBT(data);
}

@Override
public void readFromNBT(NBTTagCompound data) {
    super.readFromNBT(data);
    this.capObject.deserializeNBT(data.getCompoundTag("something"));
    this.someEnergy = data.getInteger("another_thing");
}
```

## `ItemStack`

我们可以直接把数据写入 `ItemStack` 自己的 NBT 中。

```java
@Override
public ICapabilityProvider initCapabilities(ItemStack stack, NBTTagCompound nbt) {
    return new ICapabilityProvider() {

    };
}
```

若是通过 `AttachCapabilitiesEvent` 附加的功能，也可以通过实现 `INbtSerializable` 的方式保存数据。然而这里有一个坑：
一般来说，`ItemStack` 的同步其实都是 GUI 在负责的，因为 `ItemStack` 它本身并不与任何游戏底层机制耦合，和底层耦合的是 GUI 及 TileEntity。也因为此，同步一个 `ItemStack` 实际上比表面上的情况还要复杂——

<!--
https://github.com/MinecraftForge/MinecraftForge/issues/2523
https://github.com/MinecraftForge/MinecraftForge/pull/3099
https://github.com/MinecraftForge/MinecraftForge/pull/3283
https://github.com/MinecraftForge/MinecraftForge/issues/3483
https://github.com/MinecraftForge/MinecraftForge/issues/3682
https://github.com/MinecraftForge/MinecraftForge/pull/3776
https://github.com/MinecraftForge/MinecraftForge/issues/4580
https://github.com/MinecraftForge/MinecraftForge/pull/4594
https://github.com/MinecraftForge/MinecraftForge/pull/4932
https://github.com/MinecraftForge/MinecraftForge/pull/5009
-->

## `Entity`

实体死亡时，Capability 数据会丢失，需要手动同步。但实际上，对于 `EntityPlayer`，从末地返回时，Capability 数据理应被保存。处于某种原因，这个特性目前还未被实现。

https://github.com/MinecraftForge/MinecraftForge/pull/5211

## `Village`

## `Chunk`

## `World`
