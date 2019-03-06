## 关于流体容器之间的交互

有鉴于某些设计决定，如果你要跟某个其他 Mod 的流体容器打交道，你会这么写：

```java
TileEntity tile = world.getTileEntity(pos);
if (tile.hasCapability(CapabilityFluidHandler.FLUID_HANDLER_CAPABILITY, facing.getOpposite())) {
    IFluidHandler handler = tile.getCapability(CapabilityFluidHandler.FLUID_HANDLER_CAPABILITY, facing.getOpposite());
    FluidStack drained = handler.drain(..., true);
    if (drained != null && ...) {
        handler.drain(..., false);
    }
}
```

最坏的情况下你大概要重复这样的代码几十遍。和玩家手里的物品打交道的情况只会更糟。
值得庆幸的是，[早在万能桶被引入的那个 PR 中][ref-pr-2333]，有一个名为 `FluidUtil` 的类也被随之引入了。这个类下有一大批已经封装好的标准化的和流体容器打交道的方法，直接复用即可。

<!-- TODO Why not talk about what methods does FluidUtil provides, and what are their use cases? -->

[ref-pr-2333]: https://github.com/MinecraftForge/MinecraftForge/pull/2333
