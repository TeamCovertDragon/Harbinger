## 类似空气的方块

如果要写一个类似空气那样的方块该做什么？

```java
public class BlockWithoutHitbox extends Block {

    // TODO Actual meaning and necessity
    @override
    public boolean isFullBlock(IBlockState state) {
        return false;
    }

    // TODO Actual meaning and necessity
    @override
    public boolean isBlockNormalCube(IBlockState state) {
        return false;
    }

    // TODO Actual meaning and necessity
    @override
    public boolean isNormalCube(IBlockState state) {
        return false;
    }

    // func_149686_d，返回 false 代表这个方块不是完整的 1x1x1 立方体。
    // 和 isOpaqueCube 不一样的地方在于，这个方法会影响窒息、碰撞等判定及光照计算。
    @override
    public boolean isFullCube(IBlockState state) {
        return false;
    }

    // func_176205_b，返回 true 代表可以钻过这个方块，参考原版门的情况
    @override
    public boolean isPassable(IBlockAccess worldIn, BlockPos pos) {
        return true;
    }

    // func_149662_c，返回 false 代表这个方块是完整的 1x1x1 的正方体，且没有透明材质
    // TODO According to MCP, this affects ambient occlusion & culling?
    @override
    public boolean isOpaqueCube(IBlockState state) {
        return false;
    }

    // func_149645_b，返回 EnumBlockRenderType.INVISIBLE 代表不渲染这个方块
    @override
    public EnumBlockRenderType getRenderType(IBlockState state) {
        return EnumBlockRenderType.INVISIBLE;
    }

}
```

### 但是鼠标指过去还是有外面那一圈黑框啊。

```java
// func_180646_a
// TODO Is this the correct method?
@Override
public AxisAlignedBB getCollisionBoundingBox(IBlockState blockState, World worldIn, BlockPos pos) {
    return Block.NULL_AABB; // field_185506_k，代表一个从 (0,0,0) 到 (0,0,0) 的 AxisAlignedBB
}
```
