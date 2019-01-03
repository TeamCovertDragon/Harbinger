### `IConditionFactory`

Forge 在原版的 JSON 合成表的基础上提供的五种扩展功能之一。如名字所示，它可以根据 JSON 合成中给出的条件，产生一个 `BooleanSupplier` 对象，用来决定是否使用某个 JSON 合成表或是某个命名常量。

#### 为什么会有这么一个东西？

假如说你的合成依赖别的 Mod 的物品，但你不硬依赖那个 Mod，那么没装那个 Mod 的时候 JSON 合成表岂不是完蛋了。所以 Forge 内建的 `IConditionFactory` 中有一个就是对付这个情况的：

```json
{
  "type": "minecraft:crafting_shapeless",
  "group": "my_mod:example",
  "conditions": [
    {
      "type": "minecraft:item_exists",
      "item": "biomesoplenty:mud"
    }
  ],
  "result": {
    "item": "minecraft:diamond_block",
    "count": 64
  },
  "ingredients": [
      { "item": "biomesoplenty:mud" },
      { "item": "biomesoplenty:mud" }
  ]
}
```

或者也可以：

```json
{
  "type": "minecraft:crafting_shapeless",
  "group": "my_mod:example",
  "conditions": [
    {
      "type": "forge:mod_loaded",
      "modid": "biomesoplenty"
    }
  ],
  "result": {
    "item": "minecraft:diamond_block",
    "count": 64
  },
  "ingredients": [
      { "item": "biomesoplenty:mud" },
      { "item": "biomesoplenty:mud" }
  ]
}
```

#### Forge 内建的六种 Condition

1. `minecraft:item_exists` - 在有指定物品时允许注册此合成。`item` 字段填入对应物品的注册名。
  ```json
  { "conditions": [ { "type": "minecraft:item_exists", "item": "my_another_mod:awesome_item" } ] }
  ```
2. `forge:mod_loaded` - 在有指定 Mod 时允许注册此合成。`modid` 字段填入对应 Mod 的 mod id。
  ```json
  { "conditions": [ { "type": "forge:mod_loaded", "modid": "my_another_mod" } ] }
  ```
3. `forge:not` - 逻辑非。下面的例子展示了“如何在指定 Mod 不存在时加载此合成”。
  ```json
  { "conditions": [ { "type": "forge:not", "value": { "type": "forge:mod_loaded", "modid": "my_another_mod" } } ] }
  ```
4. `forge:and` - 逻辑与。下面的例子展示了“如何在指定 Mod 与指定物品同时存在时，加载此合成”。
  ```json
  {
      "conditions": [
          {
              "type": "forge:and",
              "values": [
                  { "type": "forge:mod_loaded", "modid": "mod_a" },
                  { "type": "minecraft:item_exists", "modid": "mod_a:some_sort_of_weird_item" }
              ]
          }
      ]
  }
  ```
5. `forge:or` - 逻辑或。下面的例子展示了“如何在有下列 Mod 中的任意一个时加载此合成”。
  ```json
  {
      "conditions": [
          {
              "type": "forge:or",
              "values": [
                  { "type": "forge:mod_loaded", "modid": "mod_a" },
                  { "type": "forge:mod_loaded", "modid": "mod_b" }
              ]
          }
      ]
  }
  ```
6. `forge:false` - 逻辑全假。相当于一个 Debug 开关一样的东西，用这个就能让指定合成永远不会加载。自然地，在没有任何 Condition 的情况下，其效果和逻辑全真无异。
  ```json
  { "conditions": [ { "type": "forge:false" } ] }
  ```

#### 自己实现 `IConditionFactory`：受配置文件控制的合成表

首先：

```java
package my_mod;

public class ConfigBackedRecipeCondition implements IConditionFactory {
    /*
     * 注意 json 参数获得的不是整个合成表，而是 condition 它本身的 JSON Object。
     * 比方说 minecraft:item_exists 的实现中就通过这个拿到要求的物品 ID。
     */
    @Override
    public BooleanSupplier parse(JsonContext context, JsonObject json) {
        return () -> MyConfig.enableRecipe; // 管你配置文件怎么实现的，只要有对应的字段或 getter 就可以
    }
}
```

然后，在 `assets/[modid]/recipes/` 目录下新建 `_factories.json` 的文件，并按下列格式填入新的 Condition 的名称和对应的类的 Canonical name，这样 Forge 就会识别到：

```json
{
  "conditions": {
    "enable": "my_mod.ConfigBackedRecipeCondition"
  }
}
```

最后，你的合成改成这个样子，注意那个 `enable`：

```json
{
  "type": "minecraft:crafting_shapeless",
  "group": "my_mod:example",
  "conditions": [
    {
      "type": "enable"
    }
  ],
  "result": {
    "item": "minecraft:diamond_block",
    "count": 64
  },
  "ingredients": [
      { "item": "minecraft:dirt" },
      { "item": "minecraft:dirt" }
  ]
}
```
