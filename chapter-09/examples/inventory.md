## 一口“箱子”

“箱子”加引号的意思是指它不一定就叫箱子，也可以叫别的名字（桶、抽屉、货架、……）。但不论叫什么，它们都有一个共同特征：可以保存任意物品，并且其他 `TileEntity` 可以访问。  
下面给出了一个这样的 `TileEntity` 的参考实现。关于 `ItemStackHandler`（[`IItemHandler`][ref-item-handler]）的内容会在第二十七章讲到 Capability 系统时有详细解释。

[ref-item-handler]: ../../chapter-27/built-in/item.md

```java
// TODO Explanation
import net.minecraft.nbt.NBTTagCompound;
import net.minecraft.tileentity.TileEntity;
import net.minecraft.util.EnumFacing;
import net.minecraftforge.common.capabilities.Capability;
import net.minecraftforge.items.CapabilityItemHandler;
import net.minecraftforge.items.IItemHandler;
import net.minecraftforge.items.ItemStackHandler;

public class MyInventory extends TileEntity {
    // 所有的物品数据全部保存在名为 Inventory 的标签下。
    private static final String KEY_INV = "Inventory";

    // 直接复用 Forge 提供的参考实现
    private ItemStackHandler inv = new ItemStackHandler(8);

    public void readFromNBT(NBTTagCompound data) {
        super.readFromNBT(data);
        this.inv.deserializeNBT(data.getCompoundTag(KEY_INV));
    }

    public NBTTagCompound writeToNBT(NBTTagCompound data) {
        data.setTag(KEY_INV, this.inv.serializeNBT());
        return super.writeToNBT(data);
    }

    public boolean hasCapability(Capability<?> cap, EnumFacing direction) {
        return cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY
                || super.hasCapability(cap, direction);
    }

    public <T> T getCapability(Capability<T> cap, EnumFacing direction) {
        return cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY ?
                CapabilityItemHandler.ITEM_HANDLER_CAPABILITY.cast(this.inv)
                :
                super.getCapability(cap, direction);
    }
}
```
