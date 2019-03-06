## 方块状态

和 [`ItemStack`][ref-item-stack] 一样，方块状态（`IBlockState`）代表了同一类方块在这个世界中被附加上不同“属性”（即 `IProperty<?>`）后的“状态”。考虑下列案例即可注意到方块状态与 `ItemStack` 的相似之处：

  * 橡木方块是橡木方块的事实不会因为它的年轮朝向而转移。
  * 干燥耕地和湿润耕地都能支撑作物生长，惟干燥耕地比湿润耕地更容易退化为普通泥土。
  * 红石线的连接情况不影响它与生俱来的传输红石信号的特性。

相似地，`Block` 类下包含了所有的逻辑，大多数方法中也能找到一个 `IBlockState` 参数。

[ref-item-stack]: ../chapter-04/item-stack.md

### ~~枚举、排列与组合？~~ 枚举与笛卡尔积

和 `ItemStack` 不一样，一个方块的所有可能的方块状态是可以被穷尽的。  
一个方块的方块状态由有限多个 `IProperty<?>` 所描述，这些 `IProperty<?>` 都会传入 `BlockStateContainer` 的构造器中，如下所示：

```java
@Override
public BlockStateContainer createBlockState() {
    // 实际上第二个参数是 var arg，这里为了让参数更明显而使用了显式的数组声明。
    return new BlockStateContainer(this, new IProperty<?>[] { ... });
}
```

而 `IProperty<?>` 代表了一个有有限多个可能性的一种属性。比如说 `BlockHorizontal.FACING` 的可能值便只有 `EnumFacing.EAST`、`EnumFacing.SOUTH`、`EnumFacing.WEST` 和 `EnumFacing.NORTH`。这些可能性互不相同，换言之每一个 `IProperty<?>` 的所有可能性可以放到一个集合中。  
如此一来，所有可能的方块状态便是…… 所有 `IProperty<?>` 的 `getAllowedValues()` 返回值的笛卡尔积。

$$
\prod_{p \in block}(p.getAllowedValues())
$$

### `ImmutableMap`？

方块状态可以看作是一个 `Map`，你可以用某个 `IProperty<?>` 拿到目标 `IBlockState` 中的对应值。

```java
EnumFacing facing = state.getValue(BlockHorizontal.FACING);
```

类似地，可以用 `withProperty` 改变 `IBlockState` 的内部状态，但如此做会得到一个新的 `IBlockState` 对象，而非原来的那一个，是为“不变”：

```java
IBlockState newState = state.withProperty(BlockHorizontal.FACING, EnumFacing.EAST);
assert newState != state;

// 事实上它的不变性也可以通过另一个角度看出来—— getProperties 返回了 Guava 的 ImmutableMap
ImmutableMap<IProperty<?>, Comparable<?>> properties = state.getProperties();
```

有一点要注意，贸然读取或写入目标方块不支持的 `IProperty<?>` 会产生 `IllegalArgumentException`，所以请务必先检查 `Block`。

```java
Block block = state.getBlock();
if（block == ...) {
    // 然后继续操作
}
```

### 序列化方块状态与方块的 metadata

很明显，既然 `IBlockState` 有 `Map` 的特征，那它的序列化应该不会太困难。
实际上并非如此——难道我们要把一个区块中的 16 \* 16 \* 256 = 65536 块方块全部序列化成 `Map`？  
Mojang 实际上在 1.13 用一个非常有趣的办法（即“调色盘”，palette）解决了这个问题，但那是发生在未来的事情，我们现在面对的是：方块状态需要正确映射到一个 metadata 上才能正确持久化。Mojang 在这个过渡时期使用的临时解决方案是 `getStateFromMeta` 和 `getMetaFromState`。
这个过程基本就是在不断位运算，因为 Minecraft 从 1.3 开始一直在使用的 Anvil 地图格式中，只给每一个坐标上的方块留了 4 个 bit 当 metadata 用。

```java
@Override
public IBlockState getStateFromMeta(int metadata) {
    // 取决于实际情况
}
@Override
public int getMetaFromState(IBlockState state) {
    // 取决于实际情况
}
```

### Hack 之上再加 Hack：不参与序列化的方块状态

然而 Mojang 为了临时解决问题使用的 Hack 导致了更大规模的 Hack 的出现。
新的问题出在红石线和栅栏上——它们的方块状态复杂到了 4 bit 压根儿没可能存得下来的地步。于是 `getActualState` 横空出世，允许某个方块根据当前世界里的属性补全方块状态。

```java
// 比如说栅栏、红石线、火这种。这些方块需要根据周围状态确定真实状态。
@Override
public IBlockState getActualState(IBlockAccess access, BlockPos pos, IBlockState baseState) {
    // 返回补全属性的 BlockState
    return baseState;
}
```
