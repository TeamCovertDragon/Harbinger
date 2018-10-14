## 依赖于具体情况的物品模型

这里讲述的内容只适用于物品模型；方块模型若需要类似的效果，可能需要使用 Forge BlockState V1。  
原版的物品模型都很简单，绝大多数物品的外貌也都是几个大版本下来都雷打不动的。但 Mod 不一样，很多时候我们都会发现“我们的物品的模型需要随着物品的某一数据的改变而改变”。有没有办法实现这个效果呢。

### 依赖于损害值的物品模型
可以说是所有情况中最简单的一种，多见于 meta hack。

```java
// 还记得这个方法吗？第二个参数就是 metadata。之前我们写了 0，因为物品没什么特别的。
// 对于这个情况，只需在调用的时候传入别的 metadata 并指定合适的模型位置就可以了。
ModelLoader.setCustomModelResourceLocation(myItem, 0, targetModelResourceLocation);
```

### Property Override

但不是所有的时候我们都有 metadata 可以用。比如工具，它实际上没有 metadata，但它有耐久。如果我们希望一个工具的耐久会决定其外观该怎么做？最简单的方法就是 Property Override。  
这是原版物品模型的一个机制，允许物品模型根据一个浮点数（准确地说是一个 `float`）的值来决定该使用什么模型。举个例子，对于有耐久度设定的工具来说，可以考虑这么做：

```json
{
    "parent": "minecraft:item/handheld",
    "textures": {
        "layer0": "..."
    },
    "overrides": [
        {
            "predicate": { "damage": 0.5 },
            "model": "..."
        }
    ]
}
```

这样，在这个工具只剩下一半耐久的时候，它就会使用另外一个模型。  
这个 predicate 是原版提供的。它有一个“缺陷”：这里的 0.5 是精确匹配的。也就是说，当这个值是 0.49 或 0.51 的时候它都不会改变模型——实际上，这个“缺陷”正好给地图制作者和服务器插件开发者提供了契机，因为利用这个特性可以用来给原版“新增一堆物品”。  
不过这里讲的是 Forge Mod 啊，既然这个 predicate 满足不论我们的需求，那我们自己写一个不就好了。为此你需要的类叫做 `IItemPropertyGetter`：

```java
public class MyEpicTool extends Item {
    public MyEpicTool() {
        super();
        // 必要的初始化，请按需添加
        this.addPropertyOverride(new ResourceLocation("example_mod", "test_damage"), new IItemPropertyGetter() {
            @Override
            @SideOnly(Side.CLIENT)
            public float apply(ItemStack stack, @Nullable World world, @Nullable EntityLivingBase entity) {
                return 0F;
            }
        });
        // 是的，这里可以用 lambda，但这里仍然使用匿名内部类的原因是那个 @SideOnly，它的含义在后面的章节会讲到。
    }
}
```

你应该发现了，你可以利用的东西不止当前的 `ItemStack` 对象，还有当前的 `World` 和持有它的实体。为什么不是 `EntityPlayer`？因为能拿东西的实体多了去了，僵尸和骷髅都可以。我们只需要在这里实现我们需要的逻辑就好，然后让模型用这个 predicate：

```json
{
    "...": "...",
    "overrides": [
        {
            "predicate": { "example_mod:test_damage": 0.1 },
            "model": "..."
        },
        {
            "predicate": { "example_mod:test_damage": 0.2 },
            "model": "..."
        }
    ]
}
```

尽管如此，它的局限依然很大——比如返回的值必须在 [0, 1] 之间。而且这只是一个 `float`，面对其他不能完美转化的数据它就束手无策了。

### `ItemMeshDefinition`

如果我要依赖的数据是字符串一类的东西该怎么办？是时候让 `ItemMeshDefinition` 登场了。  
`ItemMeshDefinition` 是一个原版类，允许某个物品只根据 `ItemStack` 提供的信息返回不同的 `ModelResourceLocation`。一个 `ItemStack` 里有什么？物品类型、数量、metadata（或损害值）甚至是 NBT 数据。<!-- 当然还有 Capability 数据，但是这需要后面的章节才会讲到。 -->理论上应该足够满足各种奇怪的需求了。  

```java
@SubscribeEvent
public static void onModelRegistration(ModelRegistryEvent event) {
    // 理论上这个也可以用 lambda。
    ModelLoader.setCustomMeshDefinition(new ItemMeshDefinition() {
        @Override
        public ModelResourceLocation getModelLocation(ItemStack stack) {
            return aCertainLocation;
        }
    });
}
```

和方块模型类似，使用 `ItemMeshDefinition` 时必须能提前知道所有可能的返回值，并告知 Minecraft 加载对应的模型。尝试返回一个 Minecraft 没有加载的模型的位置会导致实际显示为紫黑块。这也意味着：它不能用来动态生成模型。

```java
@SubscribeEvent
public static void onModelRegistration(ModelRegistryEvent event) {
    // 第二个参数是 var-arg，类型 ModelResourceLocation。
    // 注意这里使用了原版的 ModelBakery，而非 Forge 的 ModelLoader。
    ModelBakery.registerItemVariants(myItem, ...);
}
```

### 重新实现 `ItemOverrideList`

这一手段虽然仍然是基于原版类的，但有鉴于它牵扯到的其他信息，[它有自己的独立章节](../custom-model/item-override.md)。请直接前往那里获取有关信息。
