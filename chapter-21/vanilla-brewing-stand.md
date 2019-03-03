## 酿造台配方

在 Forge patch 过原版的酿造台后，我们可以通过一套 Forge 提供的接口来添加“酿造配方”了。

```java
import net.minecraftforge.common.brewing.BrewingRecipeRegistry;

// 第一个参数是“药水”，也就是三个药水格需要放的东西。第二个参数是“材料”，也就是地狱疣那一格需要放的物品。
// 第三个参数是输出。
BrewingRecipeRegistry.addRecipe(new ItemStack(Blocks.DIRT), new ItemStack(Blocks.STONE), new ItemStack(Items.DIAMOND));

// 另一个重载的版本，其中第二个参数是一个有效的矿物词典名。
BrewingRecipeRegistry.addRecipe(new ItemStack(Blocks.DIRT), "cobblestone", new ItemStack(Items.DIAMOND));
```

### 重新实现 `IBrewingRecipe`

除此之外还有一个 `IBrewingRecipe` 接口可用。如果需要某种更高级的酿造配方，可以考虑从零开始实现这个。

```java
public final class MyBrewingRecipe implements IBrewingRecipe {

    @Override
    public boolean isInput(@Nonnull ItemStack input) {
        // 用于判断指定药水输入是否匹配当前配方。
        return true;
    }

    @Override
    public boolean isIngredient(@Nonnull ItemStack ingredient) {
        // 用于判断指定材料输入是否匹配当前配方。
        return true;
    }

    @Nonnull
    @override
    public ItemStack getOutput(@Nonnull ItemStack input, @Nonnull ItemStack ingredient) {
        // 根据药水输入和新材料输入确定最终产物。
        return ItemStack.EMPTY;
    }
}
```

对于不需要依赖具体输入的酿造配方，可以考虑直接使用 `AbstractBrewingRecipe` 而不是从零开始实现 `IBrewingRecipe`。
