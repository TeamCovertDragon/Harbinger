## Forge 内建的四种 Capability

出于各种考虑，Forge 自身提供了四种 Capability：

  - [`IItemHandler`](item.m)，用于取代 Minecraft 底层的 `IInventory`，代表若干 `ItemStack` 的容器。
  - [`IFluidHandler`](fluid.md)，基于 Capability 的流体容器，<!-- 取代旧版 Forge 中的 IFluidContainerItem 等一系列接口，-->代表若干 `FluidStack` 的容器。
    - 包括一个专门为物品准备的 `IFluidHandlerItem`。
  - [`IAnimationStateMachine`](animation.md)，依托 Capability 系统存在的动画状态机，代表一个操纵其动画效果的有限状态机。
  - [`IEnergyStorage`](energy.md)，简化到极限的“能量”系统的参考实现，仅包括接口和对应的最简单的参考实现，没有任何其他细节（背景设定、传输、损耗、消费等）。
