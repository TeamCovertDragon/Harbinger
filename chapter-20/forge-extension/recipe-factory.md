### `IRecipeFactory`

Forge 在原版的 JSON 合成表的基础上提供的五种扩展功能之一。顾名思义，这个接口代表一个 `IRecipe` 的抽象工厂，本质上是个反序列化器，用于从 JSON 中构建一个 `IRecipe` 实例。关于实现一个新的 `IRecipe` 的要求可参考[这一章](../custom-recipe.md)。

```java
package my_mod;
public class MyRecipeFactory implements IRecipeFactory {
    @Override
    public IRecipe parse(JsonContext context, JsonObject json) {
        return new MyInsaneRecipe();
    }
}
```

不过要想使用它，就需要先在 `assets/[modid]/recipes/_factories.json` 中声明：

```json
{
    "factories": {
        "my_recipe": "my_mod.MyRecipeFactory"
    }
}
```

注意到这里声明的是完整的类名；内部类需要用 `$` 自不必说。  
这样一来，在写 Recipe JSON 的时候，就可以通过 `modid:factory_id` 这样的形式来指明“使用自己的 Recipe Factory”了：

```json
{ "type": "my_mod:my_recipe" }
```

世界清静了。
