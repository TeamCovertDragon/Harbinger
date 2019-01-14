## `IFluidHandler`

实际上有两种 `IFluidHandler`：一种就是它自己，另一种是 `IFluidHandlerItem`。`IFluidHandlerItem` 继承 `IFluidHandler`。

### 普通的 `IFluidHandler`

WIP

### `IFluidHandlerItem`

因为继承了 `IFluidHandler`，所以两者基本一致，只是 `IFluidHandlerItem` 多一个 `getContainerItem` 方法。

```java
public ItemStack getContainerItem() {
    // 这样一来，这个流体容器就会被认为是“一次性”的。
    // 即，当流体被清空时，它返还的容器是 ItemStack.EMPTY，即空物品。
    // 但请注意，容器返还的具体逻辑是在其他地方完成的，不受这个接口它自己的控制。
    return ItemStack.EMPTY;
}
```
