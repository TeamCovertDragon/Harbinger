## 常量池

Forge 在原版的 JSON 合成表的基础上提供的五种扩展功能之一。对于那些反复出现的 `Ingredient`，可以通过将其放在常量池中并复用，来让 JSON 合成表更加简洁。每一个 Mod 都有一个属于自己的常量池，位于 `assets/[modid]/reicpes/_constants.json`。

### `_constants.json`

`_constants.json` 大致长这样：

```json
[
  {
    "name": "my_mod:ingredient_a",
    "ingredient": {
      "type": "forge:ore",
      "ore": "ingotIron"
    }
  },
  {
    "name": "my_mod:ingredient_b",
    "conditions": [
      {
        "type": "forge:false"
      }
    ],
    "ingredient": [
      {
        "item": "minecraft:diamond"
      },
      {
        "item": "minecraft:emerald"
      }
    ]
  }
]
```

`ingredient` 字段可以是一个 Ingredient 或者多个 Ingredient 组成的数组。可选的 `conditions` 字段将决定是否会被加载，用法和在合成表 JSON 中的 `conditions` 一致。

### 引用 `_constants.json` 中的常量

参考下面这个例子：

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
    "Y": { "item": "#my_mod:ingredient_a" }
  }
}
```

只需要使用 `item` 并在常量名之前加上 `#` 前缀即可。
