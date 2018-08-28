### 高级的方块

#### 没有碰撞

参考一下空气方块。

````java
public class BlockWithoutHitbox extends Block {

    //false代表这个方块不是完整的方块
    public boolean isFullBlock(IBlockState state) {
        return false;
    }

    //false代表这个方块不是正常的方块
    public boolean isBlockNormalCube(IBlockState state) {
        return false;
    }

    //false代表这个方块不是正常的方块
    public boolean isNormalCube(IBlockState state) {
        return false;
    }

    //false代表这个方块不是完整的立方体
    public boolean isFullCube(IBlockState state) {
        return false;
    }

    //true代表可以钻过这个方块
    public boolean isPassable(IBlockAccess worldIn, BlockPos pos) {
        return true;
    }

    //false代表这个方块透明
    public boolean isOpaqueCube(IBlockState state) {
        return false;
    }

    //不渲染
    public EnumBlockRenderType getRenderType(IBlockState state) {
        return EnumBlockRenderType.INVISIBLE;
    }

}
````

### 鼠标指针指过去看不到碰撞箱

还是参考下空气方块：

````java
public class BlockWithoutHitbox extends Block {
    public AxisAlignedBB getCollisionBoundingBox(IBlockState blockState, World worldIn, BlockPos pos) {
        return NULL_AABB; //呃，这是个Block里的final字段，实际上就是一个从(0,0,0)到(0,0,0)的AxisAlignedBB
    }
}
````

和之前的那个没碰撞结合在一起就十分有趣了。

### 红石信号敏感

<!-- TODO -->

### 作物

````java
public class MyCrop extends Block implements ICrop {
        // TODO
}
````

### 树苗

### 如果我需要存储像是物品这样的数据？
这需要第 10 章才会讲到。相关的标准则在第 26、27、28 章逐一介绍。这里暂时留个悬念。
