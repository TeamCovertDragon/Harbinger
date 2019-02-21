## `IItemHandler`

WIP

### `IItemHandlerModifiable`

第十四章中提到过一个叫 `Slot` 的东西，用于表示图形化界面中一个持有 `ItemStack` 的槽位。原版的 `Slot` 是基于 `IInventory` 的。Forge 为了绕开这个限制提供了一个基于 `IItemHandler` 的 `Slot` 实现：`SlotItemHandler`。为了正确实现 `SlotItemHandler`，`IItemHandlerModifiable` 应运而生。  
相比于 `IItemHandler`，`IItemHandlerModifiable` 多出一个 `setItemStack` 方法，允许外界直接设定指定槽位中持有的 `ItemStack`：

```java
public void setItemStack(int slotIndex, ItemStack newContent) {
    // 省略实现细节
}
```
