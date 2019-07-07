# 方块状态

和 [`ItemStack`][ref-item-stack] 一样，方块状态（`IBlockState`）代表了同一类方块在这个世界中被附加上不同“属性”（即 `IProperty<?>`）后的“状态”。考虑下列案例即可注意到方块状态与 `ItemStack` 的相似之处：

  * 橡木方块是橡木方块的事实不会因为它的年轮朝向而转移。
  * 干燥耕地和湿润耕地都能支撑作物生长，但只有完全干燥的耕地才有可能变为普通泥土。
  * 红石线的连接情况不影响它与生俱来的传输红石信号的特性。

相似地，`Block` 类下包含了所有的逻辑，大多数方法中也能找到一个 `IBlockState` 参数。

[ref-item-stack]: ../chapter-04/item-stack.md

## ~~枚举、排列与组合？~~ 枚举与笛卡尔积

和 `ItemStack` 不一样，一个方块的所有可能的方块状态是可以被穷尽的。  
一个方块的方块状态由有限多个“属性”（即 `IProperty<?>`）所描述，这些 `IProperty<?>` 都会传入 `BlockStateContainer` 的构造器中，如下所示：

```java
@Override
public BlockStateContainer createBlockState() {
    // 实际上第二个参数是 var arg，这里为了让参数更明显而使用了显式的数组声明。
    // 换言之，你可以这样写：
    // return new BlockStateContainer(this, prop1, prop2, prop3);
    return new BlockStateContainer(this, new IProperty<?>[] { ... });
}
```

而 `IProperty<?>` 代表了一个有有限多个可能性的一种属性。比如说 `BlockHorizontal.FACING` 的可能值便只有 `EnumFacing.EAST`、`EnumFacing.SOUTH`、`EnumFacing.WEST` 和 `EnumFacing.NORTH`。这些可能性互不相同，换言之每一个 `IProperty<?>` 的所有可能性可以放到一个集合中。  
如此一来，所有可能的方块状态便是…… 所有 `IProperty<?>` 的 `getAllowedValues()` 返回值的笛卡尔积。

$$
\prod_{p \in block}(p.getAllowedValues())
$$

## `IProperty<? extends Comparable<?>>`

前文说到 `IProperty` 描述了目标方块的某一种属性。下面给出一些例子：

  - `BlockHorizontal.FACING` 是一个 `PropertyDirection`，允许的值有 `EnumFacing.EAST`、`EnumFacing.SOUTH`、`EnumFacing.WEST` 和 `EnumFacing.NORTH`，用于描述仅能绕 Y 轴作“水平方向上的”旋转的方块的朝向。
  - `BlockCrops.AGE` 是一个 `PropertyInteger`，允许的值为整数 0 到 7，代表了作物的生长进度。
  - `BlockRedstoneRepeater.LOCKED` 是个 `PropertyBool`，允许的值为 `true` 和 `false`，在这里它代表了红石中继器是否处于锁存模式。

自然地，原版使用的这些属性不一定能准确描述我们的需求，我们需要全新的 `IProperty`。对于简单的情况，我们可以复用这几个原版类：

  - `PropertyInteger`。只需要 `PropertyInteger.create(propertyName, minValue, maxValue)` 简单调用即可获得一个全新的 `PropertyInteger` 对象，它通常代表某种进度（机器工作进度、设备生长进度）或某种测量值（信号强度）。
  - `PropertyBool`。只需要 `PropertyBool.create(propertyName)` 简单调用就可以拿到一个代表开/关的属性。
  - `PropertyEnum`。使用它有些复杂，首先我们需要一个枚举类，并让这个枚举类实现 `IStringSerializable`，然后我们才能通过 `PropertyEnum.create(propertyName, enumClass)` 拿到一个 `PropertyEnum`。方块的 meta-hack 即是用 `PropertyEnum` 实现的。除此之外代表方向的 `PropertyDirection` 也继承了 `PropertyEnum<EnumFacing>`。
    - `IStringSerializable` 只有一个方法：`String getName()`。有鉴于它用于方块状态属性的具体值，它的返回值只能使用小写拉丁字母、阿拉伯数字或下划线（`_`）。其他字符均会令 Minecraft 报错。
    - `PropertyEnum.create` 有两个重载，允许你只使用一部分你的枚举类的值。`PropertyDirection` 便用到了这两个重载中的一个，用于限定仅东南西北四个方向的 `EnumFacing` 有效。
    - 作为 `PropertyEnum` 的特例，`PropertyDirection` 可用于描述方向。虽然有对应的 `PropertyDirection.create`，但你可以直接使用 `BlockDirectional.FACING` 和 `BlockHorizontal.FACING` 两个现成的字段。

### 重新实现 `IProperty`

重新实现 `IProperty` 的需求非常罕见，几乎所有时候 `PropertyInteger`、`PropertyEnum` 和 `PropertyBool` 的组合即可满足需求。下面给出一个模仿 `PropertyEnum` 实现的 `IProperty<String>`，在实际使用上和 `PropertyEnum` 没有区别：

```java
import com.google.common.base.Optional;

public final class MyProperty implements IProperty<String> {
    // 返回该属性的名称。
    // PropertyEnum.create 的第一个参数即是它的返回值。
    @Override
    public String getName() {
        return "my_property";
    }

    // 返回所有该属性允许的值。
    // 所有允许的值必须能够穷尽。
    // PropertyEnum.create 的第二个参数可以拿到枚举类的 class，进而可以通过
    // Class<T>.getEnumConstants() 拿到所有允许的值。若是使用带过滤器的
    // 重载，则会过滤出允许的值。
    @Override
    public Collection<String> getAllowedValues() {
        return Collections.emptySet();
    }

    // 属性的值的类型。
    @Override
    public Class<String> getValueClass() {
        return String.class;
    }

    // 从 String 中反序列化出该属性的值，在反序列化命令中出现的
    // 方块状态等情况下会用到此方法。
    // 注意这个 Optional 是 Guava 的。用 Guava 的 Optional 的原因也很简单，
    // Minecraft 直到 1.12.2 才使用 Java 8，而 java.util.Optinal 也是
    // 从 Java 8 才有的类。
    @Override
    public Optional<String> parseValue(String value) {
        return Optional.of(value.toLowerCase(Locale.ENGLISH));
    }

    // 将给定的该属性的值序列化为 String。
    @Override
    public String getName(String value) {
        return value.toLowerCase(Locale.ENGLISH);
    }
}
```

## 操作方块状态与方块状态的不变性

方块状态可以看作是一个 `Map`，你可以用某个 `IProperty<?>` 作为键拿到目标 `IBlockState` 中的对应值。

```java
// 我们可以这样来获得某个方块的当前朝向，前提是这个方块有 BlockHorizontal.FACING 这个属性。
EnumFacing facing = state.getValue(BlockHorizontal.FACING);
```

类似地，可以用 `withProperty` 改变 `IBlockState` 的内部状态，但如此做会得到一个新的 `IBlockState` 对象，而非原来的那一个，是为“不变”：

```java
// 我们可以这样来改变方块的朝向。
IBlockState newState = state.withProperty(BlockHorizontal.FACING, EnumFacing.EAST);
// 因为我们拿到了一个全新的 IBlockState 对象，所以我们需要手动调用 setBlockState 之类的方法
// 从而让当前游戏世界看到这个变化。
assert newState != state;

// 事实上它的不变性也可以通过另一个角度看出来—— getProperties 返回了 Guava 的 ImmutableMap。
// getProperties() 这个方法在 Mod 开发中基本没有用处，它之所以存在是因为 Minecraft 它自己需要用到，
// 比如序列化方块状态成一个 NBTTagCompound 的时候。
ImmutableMap<IProperty<?>, Comparable<?>> properties = state.getProperties();
```

有一点要注意，贸然读取或写入目标方块不支持的 `IProperty<?>` 会产生 `IllegalArgumentException`，所以请务必先检查 `Block`。

```java
Block block = state.getBlock();
if（block == ...) {
    // 然后继续操作
}
```

## 序列化方块状态与方块的 metadata

很明显，既然 `IBlockState` 有 `Map` 的特征，那它的序列化应该不会太困难。
实际上并非如此——难道我们要把一个区块中的 16 \* 16 \* 256 = 65536 块方块全部序列化成 `Map`？  
Mojang 实际上在 1.13 用一个非常有趣的办法（即“调色盘”，palette）解决了这个问题，但那是发生在未来的事情，我们现在面对的是：方块状态需要正确映射到一个 metadata 上才能正确持久化。Mojang 在这个过渡时期使用的临时解决方案是这样的：

  - `getStateFromMeta`，顾名思义，根据 meta 构造对应的 `IBlockState`，是为反序列化。
  - `getMetaFromState`，顾名思义，根据 `IBlockState` 获得对应的 meta，是为序列化。

这个过程基本就是在不断位运算，因为 Minecraft 从 1.3 开始一直在使用的 Anvil 地图格式中，只给每一个坐标上的方块留了 4 个 bit 当 metadata 用。

```java
@Override
public IBlockState getStateFromMeta(int metadata) {
    // 取决于实际情况
    return 0;
}
@Override
public int getMetaFromState(IBlockState state) {
    // 取决于实际情况
    return state;
}
```

## Hack 之上再加 Hack：不参与序列化的方块状态

然而 Mojang 为了临时解决问题使用的 Hack 导致了更大规模的 Hack 的出现。
新的问题出在红石线和栅栏上——它们的方块状态复杂到了 4 bit 压根儿没可能存得下来的地步。于是 `getActualState` 横空出世，允许某个方块根据当前世界里的属性补全方块状态。

```java
// 比如说栅栏、红石线、火焰方块这种。这些方块需要根据周围状态确定真实状态。
@Override
public IBlockState getActualState(IBlockAccess access, BlockPos pos, IBlockState baseState) {
    // 返回补全属性的 BlockState
    return baseState;
}
```
