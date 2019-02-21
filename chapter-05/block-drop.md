## 方块掉落概论

修改方块的掉落可以说是非常常见的需求了。在 Forge patch 过原版的 `Block` 类后，你可以通过覆写 `getDrops` 来决定方块掉落：

```java
@Override
public void getDrops(NonNullList<ItemStack> drops, IBlockAccess blockAccess, BlockPos pos, IBlockState state, int fortune) {
    // 把要掉落的物品塞进 drops 里即可。
}
```

<!-- TODO func_149745_a `Block::quantityDropped` -->
<!-- TODO func_180660_a `Block::getItemDropped` -->
<!-- TODO func_180651_a `Block::damageDropped` -->
<!-- TODO func_149679_a `Block::quantityDroppedWithBonus`-->

### 经验掉落

注意到原版煤矿石、红石矿石、钻石矿石、青金石矿石、绿宝石矿石和下界石英矿石都会在正常采集后掉经验球。在 Forge patch 过原版 `Block` 等类后，你可以决定正常挖掘某个方块时会掉多少经验。

```java
@Override
public int getExpDrop(IBlockState state, IBlockAccess blockAccess, BlockPos pos, int fortune) {
    return 0;
}
```

### 精准采集

```java
@Override // Forge patch 的方法
public boolean canSilkHarvest(World world, BlockPos pos, IBlockState state, EntityPlayer player) {
    // 这个方法决定了精准采集有没有效果。
    return true;
}

@Override // func_180643_i
public ItemStack getSilkTouchDrop(IBlockState state) {
    // 而这个方法决定了精准采集会掉什么
    return ItemStack.EMPTY;
}
```

### 事件

显然，我们时不时会遇到“需要修改原版方块或其他 Mod 的方块的掉落”的问题。Forge 追加的事件中，`BlockEvent.HarvestDropsEvent` 正好可以胜任。

```java
@SubscribeEvent
public static void onHarvesting(BlockEvent.HarvestDropsEvent event) {
    // TODO Do something
}
```
