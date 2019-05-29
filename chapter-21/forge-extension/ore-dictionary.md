# 矿物字典

Forge 在原版的 JSON 合成表的基础上提供的五种扩展功能之一。说是“在原版的 JSON 合成表的基础上”，实际上它的历史可以追溯回适用于 Minecraft 1.4.6 的 Forge。那个时候 FML 和 Forge 还是两个项目，只是两者之间有密切合作。

## 为什么会有这么一个东西？

屏幕前的读者应该知道，大型整合里往往有七八种铜锭。这些铜锭在设定上应该是可以互换的。但怎么做呢？有请 OreDictionary。

````java
// 带若干重载：
// addOre(Block block, String oreName); // 相当于 addOre(Item.getItemFromBlock(block), oreName);
// addOre(Item item, String oreName); // 相当于 addOre(new ItemStack(item, 1, 0), oreName);
OreDictionary.addOre(new ItemStack(MyItems.copperOre), "oreCopper");
````

完。  
等等怎么可能就这么完了呢，你还要把它用在某些地方啊——

## 有序合成，带矿词支持

```json
{
    "type": "forge:ore_shaped",
    "group": "my_mod:example",
    "result": { "item": "minecraft:diamond" },
    "pattern": [
        "XYX",
        "YXY",
        "XYX"
    ],
    "key": {
        "X": { "type": "forge:ore_dict", "ore": "dyeBlue" },
        "Y": { "type": "forge:ore_dict", "ore": "ingotGold" }
    }
}
```

## 无序合成，带矿词支持

```json
{
    "type": "forge:ore_shapeless",
    "group": "my_mod:example",
    "result": { "item": "minecraft:diamond" },
    "ingredients": [
        { "type": "forge:ore_dict", "ore": "blockDirt" },
        { "type": "forge:ore_dict", "ore": "stone" },
        { "type": "forge:ore_dict", "ore": "dye" },
        { "type": "forge:ore_dict", "ore": "nuggetIron" }
    ]
}
```

## 混合

自然，`forge:ore_shaped` 和 `forge:ore_shapeless` 两种类型的合成中也可以直接用某种物品。

```json
{
  "type": "forge:ore_shaped",
  "group": "my_mod:example",
  "result": { "item": "minecraft:beacon" },
  "pattern": [
    "ABC",
    "DEF",
    "GGG"
  ],
  "key": {
      "A": { "item": "minecraft:diamond_block" },
      "B": { "item": "minecraft:gold_block" },
      "C": { "item": "minecraft:iron_block" },
      "D": { "type": "forge:ore_dict", "ore": "gemLapis" },
      "E": { "item": "minecraft:nether_star" },
      "F": { "type": "forge:ore_dict", "ore": "gemLapis" },
      "G": { "item": "minecraft:obsidian" }
  }
}
```

## `OreDictionary.WILDCARD_VALUE`

有那么一些时候，你希望你的输入的 metadata 或者损害值不影响合成的匹配，比如原版的木棍合成使用的木板是可以混搭的，此时作为合成材料的木板的 metadata 是无所谓的（“don't care”）。  
为此，Minecraft 引入了一个幻数——`Short.MAX_VALUE`——来指代这一情况。你可以在 `Ingredient` 类的 `apply` 方法的实现中找到这个幻数（`32767`）的身影。

Forge 在矿物词典中明确了这个字段的含义及具体值，以 `OreDictionary` 类下的 `WILDCARD_VALUE` 字段的形式呈现。

有鉴于此，你可以这样表示原版的全部六种木板：

```json
{
    "item": "minecraft:plank",
    "data": 32767
}
```
