## Meta Hack

很多 Modder 都会使用这样的技巧来避免占用过多的物品 ID。下面是这个 hack 的具体实现。原版中也有类似的代码可以参考。  
在 1.13 中，随着 metadata 的取消和工具耐久合并入 NBT 数据中，这个 hack 也将不再适用。因此，在使用这个 hack 前，请三思。

```java
public class MultiMetaItem extends Item {
    public MultiMetaItem() {
        super();
        // 声明此物品有“子类型”，即有 Metadata 值。没错就是这么简单...
        // 声明之后物品即拥有 Meta 值。最小为 0，最大是 Short.MAX_VALUE，即 32767。
        // 最大值同时也有一个特殊含义，将在讲到合成表及 OreDictionary 时解释。
        // 在范围内的 Meta 都是允许的。通常所谓的“非法 metadata”都与某些涉及 metadata
        // 有效性的假定有关（比如直接使用 metadata 当作数组下标访问数组内元素，此时可能会
        // 出现下标越界的情况）。
        setHasSubtype(true);
        //将最大损害值设定为0，避免一些奇妙的“修复物品的设备”产生的刷物品 Bug。
        setMaxDamage(0);
        //防止被修复。避免一些奇妙的“修复物品的设备”强行修复该物品。
        setNoRepair();

        // 注：
        // 损害值和 Metadata 是两个概念，但它们的具体实现是耦合在一起的，准确地说是
        // 共用一个字段存储这个数据，由 hasSubType 属性控制其具体含义。
        // 换言之，能损坏的工具，是不能同时拥有 Metadata 的，除非耐久度以 NBT 存储。
        // 这样做的代表是 Immersive Engineering 的工程师系列工具。
    }

    // 让带合适 Meta 的物品显示在创造模式物品栏里。
    // 实际上这个方法控制该物品在创造模式物品栏中显示的所有具体物品。
    // 比方说你可以用这个来显示一个没电的电钻和一个充满电的电钻（参考 IC2）。
    @Override
    public void getSubItems(CreativeTabs tab, List<ItemStack> list) {
        if (this.isInCreativeTab(tab)) {
            // ItemStack表示“一堆数量若干的物品”。
            // 构造器中，第一个参数可以是 Item 也可以是 Block（有对应的重载）
            // 第二个参数是数量。第三个参数即是 meta 值。
            // 若省略第三个参数则默认为 0。
            // 若省略第二个参数则第三个参数也必须省略，默认为数量 1，meta 为 0。
            list.add(new ItemStack(item, 1, 0));
            list.add(new ItemStack(item, 1, 1));
        }
    }
}
```
