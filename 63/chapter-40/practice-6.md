# 实现 Railcraft 界钉（Worldspike，即原世界锚 World Anchor）

  - 消耗末影珍珠以保持区块加载的 TileEntity。
    - 区块加载通过 Forge 的 `ForgeChunkManager` 完成。
      - 通过 `ForgeChunkManager.requestTicket(modInstance, world, ForgeChunkManager.Type.NORMAL)`
        拿到一个 `ForgeChunkManager.Ticket`。
      - 通过 `ForgeChunkManager.forceChunk(ticket, chunk)` 强制保持目标区块加载，其中 `chunk` 是 `ChunkPos`。
        - `ticket` 即你刚才获得的 `ForgeChunkManager.Ticket` 实例。
        - `BlockPos` 换算 `ChunkPos`：`ChunkPos` 有一个接受 `BlockPos` 的构造器，直接用它即可。
      - 通过 `ForgeChunkManager.unforceChunk(ticket, chunk)` 解除对目标区块的强制加载。
      - 通过 `ForgeChunkManager.releaseTicket(ticket)` 释放所有该 ticket 名下强制加载的区块。
    - 一颗末影珍珠可保持该 TileEntity 所在区块及周围 3x3 区块加载 12 小时，以现实时间计算。
    - 在服务器重新启动后，会主动重新加载自己及所在的区块。
    - 可通过漏斗等设备自动输入末影珍珠
    - 亦可通过一个 GUI 放入珍珠。GUI 中显示剩余加载时间。
    - 可通过配置文件将锚消耗的燃料从末影珍珠改为其他物品。
    - 可通过配置文件将单颗末影珍珠提供的加载时间修改为其他数值。
  - 有四种版本：普通、被动、私人和管理员
    - 普通锚的特性如上所述。
    - 被动锚不会在服务器启动时主动重新加载自己。
    - 私人锚只在其主人在线时工作。
      - 可通过 `EntityPlayer.getUniqueID()` 拿到玩家的 UUID。
    - 管理员锚不消耗末影珍珠，也没有 GUI。