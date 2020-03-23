# 重新实现 `IRecipe`

有些时候 JSON 合成并不能满足我们的需求。考虑一下下面的情况：

  - 原版地图缩放是在工作台中进行的。
  - 烟花火箭的样式由合成时使用的材料确定。
  - 两把类型一样的工具在工作台中可以进行“修复”合成以恢复一定耐久。

这三种合成都不是普通的 JSON 合成表可以搞定的问题。

## `IRecipe`

我们要做的事情也非常简单：实现 `IRecipe`。

```java
// 在 1.12.2 中 IRecipe 这个接口“继承”了 IForgeRegistryEntry<IRecipe>，
// 也就是说它和方块、物品一样，都有一个“注册名”。
// 我们需要存储这个注册名。这里我们直接通过继承 IForgeRegistryEntry.Impl
// 这个 Forge 自带的实现就可以了。
public final class MyRecipe extends IForgeRegistryEntry.Impl<IRecipe> implements IRecipe {

    @Override
    public boolean matches(InventoryCrafting inv, World world) {
        // 在这里检查当前工作台中的物品是否匹配这个合成。
        // 若匹配，返回 true。
        return false;
    }

    @Override
    public ItemStack getRecipeOutput() {
        // 返回该合成的“默认”输出，用于原版小绿书那样的显示合成表的工具。
        // 对于普通的原版合成来说和 getCraftingResult(InventoryCrafting inv) 返回值是一样的。
        // 在极端情况下，这个方法的返回值会和 getCraftingResult 不一样，例如：
        // 
        //  - 原版的烟花合成，可以确定是能返回烟花火箭或者烟火之星，但无法附带正确的数据
        //  - 原版的工具修复合成，无法确定输出类型，此时只能返回 ItemStack.EMPTY
        return ItemStack.EMPTY;
    }

    @Override
    public ItemStack getCraftingResult(InventoryCrafting inv) {
        // 返回由当前工作台中物品决定的输出。
        // 此时你可以根据输入的物品来决定输出的产物的具体信息。
        return this.getRecipeOutput();
    }

    @Override
    public boolean canFit(int width, int height) {
        // 给定工作台的输入网格尺寸，判断该合成能否放进这个尺寸的合成网格中
        return true;
    }
  
    @Override
    public NonNullList<Ingredient> getIngredients() {
        // 返回该合成的输入，用于原版小绿书那样的显示合成表的工具。
        return NonNullList.create();
    }

    @Override
    public boolean isDynamic() {
        // 是否为“动态”合成表，即输入是否会影响输出。
        // 举例：泥土换钻石是静态的，泥土不论怎么放，输出都是一颗钻石，应返回 false。
        // 举例：工具修复合成，因为不同的耐久会影响最终修复后的工具的耐久，所以这个是动态的，应返回 true。
        return false;
    }
    
    @Override
    public String getGroup() {
        // 返回该合成表的分组名。
        // 原版用到它的地方是游戏内置合成表，也就是那本小绿书。
        return "";
     }
  
}
```

## 注册

我们还需要告知 Minecraft 新合成表的存在。有鉴于它也是 `IForgeRegistryEntry`，我们可以这么做：

```java
@SubscribeEvent
public static void onRecipeReg(RegistryEvent.Register<IRecipe> event) {
    event.getRegistry().register(new MyRecipe().setRegistryName("my_mod:my_custom_recipe"));
}
```

或者，你也可以考虑使用 Forge 的 [`IRecipeFactory`](./forge-extension/recipe-factory)。
两种方法都可以解决问题，但 Forge 的 `IRecipeFactory` 更接近 1.13 及更高版本中 Minecraft 对这些有特殊逻辑的合成的解决方案。如果你在意向新版迁移的问题，建议使用后者。