### 新的作物

对于简单的作物来说，原版的 BlockCrops 类可以直接拿来复用。

```java
public class MyCrop extends BlockCrops {
    // SRG func_149866_i
    @Nonnull
    @override
    protected Item getSeed() {
        return MyModItems.MY_CROP_SEED;
    }

    // SRG func_149865_P
    @Nonnull
    @override
    protected Item getCrop() {
        return MyModItems.MY_CROP_ITEM;
    }
}
```

这里 `getSeed` 和 `getCrop` 返回了不同的结果。如果是像原版胡萝卜和土豆那样，种子和收获的作物是同一种物品的话，这里可以直接返回同一个物品的实例。
