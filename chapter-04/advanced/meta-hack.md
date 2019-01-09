## Meta Hack

和物品的 Meta-hack 一样，方块也存在 meta-hack，参考原版的羊毛、木头等。很多 Modder 都会使用这样的技巧来避免占用过多的方块 ID。一般来说，它的实现原理是使用单独的 `PropertyEnum<T extends Enum<T>>` 来表达方块的具体类型，有时可能还会涉及到对 bit vector 的操作（即将方块 meta 看作一个 bit vector，并从中反序列化信息）。

```java
public class MetaBlock extends Block {

    public enum Type {
        FOO, BAR, BAZ;
    }

    private static final PropertyEnum<Type> TYPE = PropertyEnum.create(Type.class);

    public MetaBlock(Material m, MapColor c) {
        super(m, c);
    }

    @Override
    public BlockStateContainer createBlockState() {
        return new BlockStateContainer(this, TYPE); // var-args
    }

    @Override
    @SuppressWarnings("deprecation") // 1.13 中即将移除
    public IBlockState getStateFromMeta(int meta) {

    }

    @Override // 1.13 中即将移除
    public int getMetaFromState(IBlockState state) {

    }
}

```
