## 战利品表（Loot Table）

战利品表是 Minecraft 1.9 时引入的新特性，允许地图制作者修改实体的掉落物和战利品箱子（地牢、要塞、废弃矿井等）中的内容。1.14 中也开始用于决定方块的掉落物。

### 先看一个表

```json
{
  "pools": [
    {
      "conditions": [
        {
          "condition": "random_chance",
          "chance": 0.1
        }
      ],
      "rolls": 5,
      "bonus_rolls": 2,
      "entries": [
        {
          "type": "item",
          "name": "minecraft:diamond",
          "functions": [
            {
              "function": "set_count",
              "count": 6
            }
          ]
        }
      ]
    }
  ]
}
```

### 术语

 - Loot Table
 - Loot Pool
 - Loot Entry

WIP
