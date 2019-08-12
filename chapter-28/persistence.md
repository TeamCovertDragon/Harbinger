# 关于 Capability 数据的持久化

Capability 好用归好用，但相关的数据的持久化其实是个坑。

## `TileEntity`

`TileEntity` 持有的 Capability 数据通常是保存在 `TileEntity` 本身的那个 NBT 里的。所以，对于 `TileEntity` 来说，持久化 Capability 的数据和持久化普通的 `TileEntity` 里的字段没有任何区别。

```java
@Override
public NBTTagCompound writeToNBT(NBTTagCompound data) {
    data.setTag("something", this.capObject.serializeNBT());
    data.setInteger("another_thing", this.someEnergy);
    return super.writeToNBT(data);
}
```

## `Chunk`、`Entity`、`ItemStack`、`Village` 和 `World`

这五个情况下你需要提供你自己的 `ICapabilityProvider`。  
若需要持久化数据，只需要让实现 `ICapabilityProvider` 的类同时实现 `INBTSerializable<NBTTagCompound>` 即可。Forge 会检查它拿到的 `ICapabilityProvider` 是否实现了 `INBTSerializable`，并调用相关方法读写数据。

```java
public final class ConcreteCapabilityProvider
implements ICapabilityProvider, INBTSerializable<NBTTagCompound> {
    // ……
}
```

或者可以只实现 `ICapabilitySerializable<NBTTagCompound>`，这个接口等价于上述两个接口合二为一：

```java
public final class ConcreteCapabilityProvider implements ICapabilitySerializable<NBTTagCompound> {
    // ……
}
```

### 关于 `ItemStack` 的数据同步

一般来说，`ItemStack` 的同步其实都是它所在的物品栏在负责的，因为 `ItemStack` 它本身并不与任何游戏底层机制耦合，和底层耦合的是物品栏及其宿主——`Container`、`TileEntity` 等等。也因为此，同步一个 `ItemStack` 实际上比表面上的情况还要复杂——

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

### 玩家死亡与复活时的数据？

玩家死亡时，Capability 数据会丢失，需要手动同步。但实际上，玩家从末地返回时 Capability 数据理应被保存。然而，出于某种原因，[这个特性目前还未被实现][ref-1]。

[ref-1]: https://github.com/MinecraftForge/MinecraftForge/pull/5211

所以你需要手动处理这个特殊情况：

```java
@SubscribeEvent
public static void reSyncDataOnPlayerDeath(PlayerEvent.Clone event) {
    MyCapability cap = event.getEntityPlayer().getCapability(MyCapability.INSTANCE, null);
    if (cap != null) {
        MyCapability original = event.getOriginal().getCapability(MyCapability.INSTANCE, null);
        // 然后将 original 里的数据逐个同步进 cap 中。
    }
}
```
