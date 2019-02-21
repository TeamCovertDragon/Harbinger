### `IIngredientFactory`

Forge 在原版的 JSON 合成表的基础上提供的五种扩展功能之一。如名字所示，它是个将 JSON 转化为 `Ingredient` 实例的抽象工厂，本质上是 JSON 的反序列化器。

```java
package my_mod;
public final class MyIngredientFactory implements IIngredientFactory {
    @Nonnull
    @Override
    public Ingredient parse(JsonContext context, JsonObject json) {
        return new MyIngredient();
    }
}
```

#### “实现” `Ingredient`

`Ingredient` 并非是接口，所以说“继承”或“覆写”可能更准确些。总而言之，既然是自定义的 `Ingredient`，它至少要覆写下面这几个方法：

```java
public final class MyIngredient extends Ingredient {
    // 来自 java.util.function.Predicate
    @Override
    public boolean apply(@Nullable ItemStack input) { // 应该是 Mojang 抽风了，ItemStack 在这里不可能是 null
        return false;
    }

    /*
     * 返回包含已知所有能令 MyIngredient::apply 返回 true 的所有 ItemStack
     * 的数组。用于原版小绿书中的合成提示。
     */
    @Nonnull
    @Override
    public ItemStack[] getMatchingStacks() {
        return new ItemStack[0];
    }

    /*
     * 返回包含已知所有能令 MyIngredient::apply 返回 true 的所有 ItemStack
     * 的打包版本的 IntList。打包需要通过 RecipeItemHelper 完成。用于原版小绿
     * 书中的合成提示。这个方法的返回值应当等价于下列 statement 的结果：
     * <pre>
     *     new IntArrayList(Arrays.stream(this.getMatchingStacks())
     *         .mapToInt(RecipeItemHelper::pack)
     *         .sorted()
     *         .toArray())
     *     )
     * </pre>
     *
     * 注：IntList 来自 FastUtil。FastUtil 的 IntList 有对 Java 集合框架
     * 的兼容（即实现 List<Integer>）。
     *
     * 这里有一个小知识：虽然 RecipeItemHelper 这个类是客户端包里的（具体来说是
     * net.minecraft.client.util 包中），但它没有 @SideOnly(Side.CLIENT)。
     */
    @Nonnull
    @Override
    public IntList getValidItemStacksPacked() {
        return IntLists.empty();
    }

    /*
     * 令此 Ingredient 实例“作废”。原版的 Ingredient 中，getMatchingStacks()
     * 和 getValidItemStacksPacked() 两个方法实际上是惰性求值的，且会缓存结果。
     * 此方法用于清理这些无效缓存。
     */
    @Nonnull
    @Override
    protected void invalidate() {

    }

    /*
     * 此方法强烈建议无条件返回 true。这个方法的存在和下列几个 Forge 的 Issue 和 Pull
     * Request 有关：
     *
     * https://github.com/MinecraftForge/MinecraftForge/pull/4472
     * https://github.com/MinecraftForge/MinecraftForge/issues/4516
     * https://github.com/MinecraftForge/MinecraftForge/pull/4519
     *
     * 出于某些理由，仅当此方法返回 true 时，覆写后的 apply 方法才会被调用。
     */
    @Override
    public boolean isSimple() {
        return true;
    }

}
```

然后根据具体实现调整对应的 `IIngredientFactory` 实现即可。

#### 使用

要使用自定义的 `Ingredient`，就需要先在 `assets/[modid]/recipes/_factories.json` 中声明：

```json
{
    "ingredients": {
        "my_ingredient": "my_mod.MyIngredientFactory"
    }
}
```

注意 `"my_ingredient"` 的值是一个类的 Canonical name。  
正确声明后，即可在合成中如此声明：

```json
{
  "type": "minecraft:crafting_shapeless",
  "group": "my_mod:example",
  "result": { "item": "minecraft:diamond_block", "count": 64 },
  "ingredients": [
    {
      "type": "my_mod:my_ingredient",
      "...": "..."
    }
  ]
}
```

`IIngredientFactory` 中，工厂方法的 `JsonObject json` 参数即代表这一小块 JSON：

```json
{
  "type": "my_mod:my_ingredient",
  "...": "..."
}
```
