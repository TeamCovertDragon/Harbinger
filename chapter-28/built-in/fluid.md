## `IFluidHandler`

实际上有两种 `IFluidHandler`：一种就是它自己，另一种是 `IFluidHandlerItem`。`IFluidHandlerItem` 继承 `IFluidHandler`。

### 普通的 `IFluidHandler`

大多数时候你不需要手动实现这个类——`FluidTank` 已经正确实现这个接口了，还附赠读写 NBT 的方法，拿来直接用就好。但实际上我们难免会遇到“需要有特殊行为的 `IFluidHandler`”的情况。这里只对实现 `IFluidHandler` 的要求做简要说明。

```java
public class MyFluidHandler implements IFluidHandler {
    /*
     * 返回这个 IFluidHandler 内部各个“储罐”（Tank）的信息。
     * 是的，对于一个 IFluidHandler 来说，它的内部可能有不止一个储罐，
     * 可以是两个、三个甚至十几个。这些储罐也可能只是模拟的，实际上根本
     * 没有对应的对象，或者它自己不持有对应的对象。但无论如何，这个方法
     * 可以让外界对这个 IFluidHandler 的内部信息有一个认识。
     * 这些信息是只读的。
     * 注意：千万不要直接往返回的数组里赋值！这个方法返回的值可以是缓存
     * 的结果，直接赋值会破坏缓存！
     */
    @Override
    public IFluidTankProperties[] getTanksProperties() {
        return new IFluidTankProperties[0];
    }

    /*
     * 向该 IFluidHandler 填充流体。
     * 填充的具体逻辑全部在这个方法中发生，对外界完全不公开。
     * 返回的 int 代表“这个 IFluidHandler 最终接受的流体的数量”，
     * 也就是说调用这个方法的用户理当从 resource.amount 中减去
     * 你返回的值。你不应直接操作 resource。
     * 当 doFill 为 false 时，整个过程为“模拟”的结果，也就是说你
     * 不应对你的 IFluidHandler 信息做出任何修改。参考很多程序都
     * 提供的“dry-run”的选项。
     */
    @Override
    public int fill(FluidStack resource, boolean doFill) {
        return resource.amount;
    }

    /*
     * 从该 IFluidHandler 中提取指定类型的指定数量的流体。
     * FluidStack 的 getFluid 决定了要提取的流体的类型，它的 amount
     * 则决定了最大允许的提取数量。
     * 返回值代表实际提取出的 FluidStack 对象，若为 null 表示“因为某种
     * 原因，这个 IFluidHandler 拿不出要求的 FluidStack”。反之，则
     * 返回的 FluidStack 代表的流体类型和请求的流体类型一致，但 amount
     * 只保证小于等于请求的量。
     * 当 doDrain 为 false 时，整个过程为“模拟”的结果，也就是说你
     * 不应对你的 IFluidHandler 信息做出任何修改。参考很多程序都
     * 提供的“dry-run”的选项。
     * TODO：查证一下是否允许返回比请求的量更多的 FluidStack？
     */
    @Nullable
    @Override
    public FluidStack drain(FluidStack toDrain, boolean doDrain) {
        return null;
    }

    /*
     * 从该 IFluidHandler 中提取指定数量的流体。
     * maxDrainAmount 决定了最大允许的提取数量。
     * 返回值代表实际提取出的 FluidStack 对象，若为 null 表示“因为某种
     * 原因，这个 IFluidHandler 拿不出要求的 FluidStack”。反之，则
     * 返回的 FluidStack 的 amount 只保证小于等于请求的量，对于返回的
     * 流体类型则没有任何保证。
     * 当 doDrain 为 false 时，整个过程为“模拟”的结果，也就是说你
     * 不应对你的 IFluidHandler 信息做出任何修改。参考很多程序都
     * 提供的“dry-run”的选项。
     */
    @Nullable
    @Override
    public FluidStack drain(int maxDrainAmount, boolean doDrain) {
        return null;
    }
}
```

### `IFluidHandlerItem`

因为 `IFluidHandlerItem` 继承了 `IFluidHandler`，所以两者基本一致，只是 `IFluidHandlerItem` 多一个 `getContainerItem` 方法。

```java
@Override
public ItemStack getContainerItem() {
    // 这样一来，这个流体容器就会被认为是“一次性”的。
    // 即，当流体被清空时，它返还的容器是 ItemStack.EMPTY，即空物品。
    // 但请注意，容器返还的具体逻辑是在其他地方完成的，不受这个接口它自己的控制。
    return ItemStack.EMPTY;
}
```
