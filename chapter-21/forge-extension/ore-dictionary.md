### 矿物字典

Forge 在原版的 JSON 合成表的基础上提供的五种扩展功能之一。说是“在原版的 JSON 合成表的基础上”，实际上它的历史可以追溯回适用于 Minecraft 1.4.6 的 Forge。那个时候 FML 和 Forge 还是两个项目，只是两者之间有密切合作。

### 为什么会有这么一个东西？

屏幕前的读者应该知道，大型整合里往往有七八种铜锭。这些铜锭在设定上应该是可以互换的。但怎么做呢？有请 OreDictionary。

````java
// 带若干重载：
// addOre(Block block, String oreName); // 相当于 addOre(Item.getItemFromBlock(block), oreName);
// addOre(Item item, String oreName); // 相当于 addOre(new ItemStack(item, 1, 0), oreName);
OreDictionary.addOre(new ItemStack(MyItems.copperOre), "oreCopper");
````

完。  
等等怎么可能就这么完了呢，你还要把它用在某些地方啊——

### 有序合成，带矿词支持

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

### 无序合成，带矿词支持

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

### 混合

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
