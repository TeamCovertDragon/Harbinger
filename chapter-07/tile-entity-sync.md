## TileEntity 与数据同步

TileEntity 类中有四个和数据同步相关的方法，分别是：

  * `getUpdatePacket` (`func_189518_D_`)
  * `onDataPacket`
  * `getUpdateTag` (`func_189517_E_`)
  * `handleUpdateTag`

其中 `getUpdatePacket` 和 `getUpdateTag` 是原版的固有方法，其余两个均是 Forge 加进去的。关于四个方法的解释，[Forge 自己的文档](http://mcforge.readthedocs.io/en/latest/tileentities/tileentity/)已经解释得很清楚了，可以直接拿来参考。这里只作简要说明。

### `onDataPacket`

`getUpdatePacket` 返回一个 `SPacketUpdateTileEntity`。这个是原版 Minecraft 中用来将逻辑服务器上需要同步的 TileEntity 数据发送到逻辑客户端上所用的数据包。它的构造器需要一个 `NBTTagCompound` 对象，这个对象就是你需要同步的数据的集合了。  
在 Forge 修改过 Minecraft 的底层后，你可以通过 `onDataPacket` 在逻辑客户端上拿到你同步过来的数据。

```java
@Override
public SPacketUpdateTileEntity getUpdatePacket() {
    // 第一个参数是要同步的 TileEntity 它自己。
    // 第二个参数是幻数，Forge patch 后的实现中，如果不是原版的 TileEntity，这个参数就没有意义。
    // 第三个就是要同步的数据了，你会在 onDataPacket 中拿到它。
    return new SPacketUpdateTileEntity(this, 1, new NBTTagCompound());
}

@Override
public void onDataPacket(NetworkManager manager, SPacketUpdateTileEntity packet) {
    NBTTagCompound data = packet.getNbtCompound();
}
```

值得注意的是，Minecraft 本身只会在 TileEntity 所在方块有更新时才同步 TileEntity。因此你需要 `World::notifyBlockUpdate`（`func_184138_a`）方法来告知 Minecraft 方块需要更新了。请按需控制更新！频繁更新只会浪费带宽资源。  
另外，`notifyBlockUpdate` 方法的第二和第三个参数分别代表旧方块状态和新方块状态。如果只是 TileEntity 有变化，大可把同一个方块状态传给第二和第三个参数。

#### 复用 `SPacketUpdateTileEntity`

如果你只是想同步 `TileEntity`，但 `notifyBlockUpdate` 显得太重量级了，那怎么办？其实我们可以复用原版的网络数据包。

```java
// 把这个丢进你的 TileEntity 里即可。
public void syncToTrackingClients() {
    if (!this.world.isRemote) {
        SPacketUpdateTileEntity packet = this.getUpdatePacket();
        // 获取当前正在“追踪”目标 TileEntity 所在区块的玩家。
        // 之所以这么做，是因为在逻辑服务器上，不是所有的玩家都需要获得某个 TileEntity 更新的信息。
        // 比方说，有一个玩家和需要同步的 TileEntity 之间差了八千方块，或者压根儿就不在同一个维度里。
        // 这个时候就没有必要同步数据——强行同步数据实际上也没有什么用，因为大多数时候这样的操作都应会被
        // World.isBlockLoaded（func_175667_e）的检查拦截下来，避免意外在逻辑客户端上加载多余的区块。
        PlayerChunkMapEntry trackingEntry = ((WorldServer)this.world).getPlayerChunkMap().getEntry(this.pos.getX() >> 4, this.pos.getZ() >> 4);
        if (trackingEntry != null) {
            for (EntityPlayerMP player : trackingEntry.getWatchingPlayers()) {
                player.connection.sendPacket(packet);
            }
        }
    }
}
```

### `getUpdateTag`

`getUpdateTag` 返回一个 `NBTTagCompound`。这个方法是在“区块刚刚被加载时”，同步 TileEntity 数据到逻辑客户端用的。对于那些不需要 `ITickable` 的 TileEntity，比如纯装饰性的 TileEntity 来说，使用它可以有效避免频繁更新 TileEntity 带来的开销。毕竟数据就在那了，同步一次一劳永逸。

```java
@Override
public NBTTagCompound getUpdateTag() {
    return new NBTTagCompound(); // 记得写要同步的数据进去
}

@Override
public void handleUpdateTag(NBTTagCompound data) {
    // 你刚才返回的 NBTTagCompound 就是现在的参数了。
}
```
