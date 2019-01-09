### 矿石

我们先来模仿原版钻石写一个蓝宝石矿石。

```java
public class SapphireOre extends Block {

    public SapphireOre() {
        super(Material.ROCK, MapColor.STONE);
        // Forge 的采掘等级机制，幻数 2 代表与钻石矿同级，magic string "pickaxe" 代表镐
        this.setHarvestLevel("pickaxe", 2);
    }

    // SRG func_180660_a，用于决定掉落的物品种类
    @Nonnull
    @Override
    public Item getItemDropped(IBlockState state, Random rand, int fortune) {
        return MyModItems.SAPPHIRE;
    }

    // SRG func_149745_a，用于决定掉落的物品数量
    @Override
    public int quantityDropped(Random random) {
        return 1;
    }

    // SRG func_149679_a，用于决定受时运影响时掉落的物品数量
    @Override
    public int quantityDroppedWithBonus(int fortune, Random random) {
        if (fortune > 0) {
            int bonusFactor = Math.max(random.nextInt(fortune + 2) - 1, 0);
            return this.quantityDropped(random) * (bonusFactor + 1);
        } else {
            return this.quantityDropped(random);
        }
    }

    // Forge 的 patch，用于决定掉落的经验数量（参考原版煤炭、红石、青金石、钻石、绿宝石、下界石英）
    @Override
    public int getExpDrop(IBlockState state, IBlockAccess world, BlockPos pos, int fortune) {
        Random random = world instanceof World ? ((World) world).rand : new Random();
        return MathHelper.getInt(random, 3, 7);
    }

    // Forge 的 patch，取代 getItem (func_185473_a)，用于创造模式下鼠标中键选取方块的功能。
    @Override
    public ItemStack getPickBlock(IBlockState state, RayTraceResult target, World world, BlockPos pos, EntityPlayer player) {
        return new ItemStack(this);
    }
}
```

那么像是原版铁矿那样掉落自身的矿石呢？只需要让 `getItemDropped` 返回方块它对应的物品，并且 `quantityDroppedWithBonus`/`quantityDropped` 一定返回 1 即可。  
此外，`damageDropped` (`func_180651_a`) 可用于决定掉落的物品的 metadata，参考原版青金石的情况。


#### 有没有更干脆利落不用绕这么多弯的写法？

有。Forge 的 patch 中有一个方法用来收集所有可能的掉落物。

```java
public void getDrops(NonNullList<ItemStack> drops, IBlockAccess world, BlockPos pos, IBlockState state, int fortune) {

}
```

覆写它即可。所有的掉落物都应添加到 `drops` 这个 `List<ItemStack>` 中。在这个方法中你还有 `IBlockAccess` 和 `BlockPos` 两个参数可用。
