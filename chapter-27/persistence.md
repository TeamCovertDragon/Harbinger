## 关于 Capability 数据的持久化

Capability 好用归好用，但相关的数据的持久化其实是个坑。

### `TileEntity`

`TileEntity` 持有的 Capability 数据通常是保存在 `TileEntity` 本身的那个 NBT 里的。所以，对于 `TileEntity` 来说，持久化 Capability 的数据和持久化其他数据没有任何区别。

```java
@Override
public NBTTagCompound writeToNBT(NBTTagCompound data) {
    data.setTag("something", this.capObject.serializeNBT());
    data.setInteger("another_thing", this.someEnergy);
    return super.writeToNBT(data);
}
```

### `ItemStack`

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

### `Entity`

实体死亡时，Capability 数据会丢失，需要手动同步。但实际上，对于 `EntityPlayer`，从末地返回时，Capability 数据理应被保存。处于某种原因，这个特性目前还未被实现。

https://github.com/MinecraftForge/MinecraftForge/pull/5211

### `Village`

### `Chunk`

### `World`
