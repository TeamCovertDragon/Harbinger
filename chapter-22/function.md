## 战利品表函数（Loot Function）

不是隔壁那个 `.mcfunction`，用于组合多个命令到一个文件里的函数。  

战利品表函数（Loot Function）本质上是修饰从战利品池里抽出来的物品的转换器。在 Java 的世界里，这个东西实质上等价于  `java.util.function.Function<ItemStack, ItemStack>`。  
为什么会有这么个东西？因为原版只有三种 Loot Entry：空、物品和某个别的战利品表。空的条目自然什么都干不了，而别的战利品表是别的战利品表的事情。对于物品，只能声明物品的 ID，别的什么都干不了。

```json
"entries": [
  {
    "type": "item",
    "name": "minecraft:diamond"
  }
]
```

这样写实际上只是一个钻石，没有任何别的奇奇怪怪的数据。如果我想表达半组钻石呢？

```json
"entries": [
  {
    "type": "item",
    "name": "minecraft:diamond",
    "functions": [
      {
        "function": "set_count",
        "count": 32
      }
    ]
  }
]
```

你可以这样理解：

```java
((Function<ItemStack, ItemStack>)(ItemStack item) -> {
    item.setCount(32);
    return item;
}).apply(lootItem);
```

同样的道理我们可以给物品加上乱七八糟的 NBT 数据、属性（Attribute）等。

### 原版内建的战利品表函数

实际上原版内建的函数基本够用了。Minecraft Wiki 上有一份[原版内建的战利品表函数列表](https://minecraft-zh.gamepedia.com/%E6%88%98%E5%88%A9%E5%93%81%E8%A1%A8#.E5.87.BD.E6.95.B0)。这里只列出其名称及用途简介。

  - `enchant_randomly`：随机附魔。想象一下使用真正的附魔台时的情况。
  - `enchant_with_levels`：随机附魔，但是能指定附魔等级（或指定随机等级范围）。
  - `exploration_map`：把原版空地图转换为指向附近某个结构的宝藏地图。
  - `furnace_smelt`：把抽到的物品烧炼并替换原本的物品。搭配某些 Loot Condition 可以做到实体被火烧死时掉落熟肉的效果。
  - `looting_enchant`：允许抢夺附魔影响抽到的物品数量。
  - `set_attributes`：给物品加属性（Attribute）。
  - `set_count`：无条件修改物品数量。可修改为某个固定数值，或随机修改为某个范围内的数值。
  - `set_damage`：无条件修改物品损伤值。不可和 `set_data` 互换。和 `set_count` 一样，支持固定数值或随机数值范围。
  - `set_data`：无条件修改物品 metadata。不可和 `set_damage` 互换。和 `set_count` 一样，支持固定数值或随机数值范围。<!-- 为什么这个连 Minecraft Wiki 上都没有记载？！-->
  - `set_name`：无条件赋予物品显示名称。参考铁砧重命名。
  - `set_nbt`：无条件赋予物品 NBT 数据。这里的 NBT 数据需要写成 String 的形式。

### 自定义的函数

显然我们会遇到“原版内建的 Loot Function 不够用”的窘境。写一个新的 Loot Function 其实很简单……

```java
public final class Identity extends LootFunction {

    /*
     * LootFunction 的构造需要这个数组。
     */
    public Identity(LootCondition[] conditions) {
        super(conditions);
    }

    /*
     * 所以实际上这不是单纯的 ItemStack -> ItemStack 的函数。第一个 ItemStack 参数就是
     * 原本的 ItemStack 了。随机数可以通过第二个 Random 参数获取。LootContext 包含了相关
     * 的上下文，比如实体来源等。返回值是修改过的 ItemStack。
     * 既然这个新的 Loot Function 叫 Identity，那我们就啥都不做好了。
     */
    @Override
    public ItemStack apply(ItemStack stack, Random rand, LootContext context) {
        return stack;
    }

    /*
     * 因为是 JSON，所以我们需要一个配套的序列化工具……
     */
    public static final class Serializer extends LootFunction.Serializer<Identity> {

        public Serializer() {
            // 传入唯一的识别 ID。
            super(new ResourceLocation("my_mod", "identity"), Identity.class);
        }

        @Override
        public void serialize(JsonObject object, Identity functionObject, JsonSerializationContext context) {
            // 既然是 Identity，好像也没什么需要保存的信息。
            // 值得一提的是……因为这样一个方法的存在，你可以自动生成 Loot Function 对应的 JSON，而不需要手写。
        }

        @Override
        public Identity deserialize(JsonObject object, JsonDeserializationContext context, LootCondition[] conditions) {
            // 既然是 Identity，好像也没什么需要恢复的信息。那就直接把 conditions 传过去就好了。
            // 这里传入的 conditions 颇有依赖注入的意思。
            return new Identity(conditions);
        }
    }
}
```

写好后注册即可：

```java
// 会自动将 Serializer 构造器中传入的 ResourceLocation 用作 ID。
LootFunctionManager.register(new Identity.Serializer());
```

然后就可以使用了：

```json
"entries": [
  {
    "type": "item",
    "name": "minecraft:diamond",
    "functions": [
      {
        "function": "my_mod:identity"
      }
    ]
  }
]
```
