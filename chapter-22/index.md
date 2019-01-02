## 战利品表（Loot Table）

战利品表是 Minecraft 1.9 时引入的新特性，允许地图制作者修改实体的掉落物和战利品箱子（地牢、要塞、废弃矿井等）中的内容。1.14 中也开始用于决定方块的掉落物。

### 先看一个表

下列这个表只包含一个战利品池（Loot Pool），这个战利品池只有 10% 的概率会被用到，一旦被抽到则会从整个池中随机抽取五次，每点幸运会带来一次额外抽取。整个池中只有一个条目：钻石，且因为 `set_count` 的存在，每次抽到的钻石都会是 6 个。

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
      "bonus_rolls": 1,
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

 - Loot Table（战利品表）：指多个随机池组成的一张表。之所以叫表，很大程度上是因为 JSON 将它以表的形式呈现出来了。
 - Loot Pool（随机池）：指多个 Loot Entry 组成的一个集合。~~这实际上就是平时所说的“奖池”~~……主要作用是方便管理。Loot Table 会根据每一个随机池的 Loot Condition 决定是否从中抽物品，并根据 `rolls` 和 `bonus_rolls` 决定抽多少次物品。
 - Loot Entry：一个特定的“战利品条目”。可用 Loot Condition 控制是否使用。可用 Loot Function 控制更多属性。
 - Loot Condition：启用某个 Loot Pool/Loot Entry/Loot Function 的判据。
 - Loot Function：修改 Loot Entry 抽到的物品的“函数”。

### 使用战利品表

从战利品表中抽物品很简单：首先你需要知道目标战利品表的名字，然后：

```java
WorldServer world = ...;
LootTableManager manager = world.getLootTableManager()
ResourceLocation lootTableLocator = ...;
LootTable table = manager.getLootTableFromLocation(lootTableLocator);
// 注：LootContext.Builder 的构造器只接受 WorldServer，但 getLootTableManager 方法是 World 里的。
LootContext context = LootContext.Builder(world).withPlayer(player).withDamageSource(...).build();
List<ItemStack> loots = table.generateLootForPools(world.rand, context);
```

在 `LootTableList` 类中有全部原版使用的 Loot Table 的 ID。

除此之外，对于继承了 `TileEntityLockableLoot` 的原版 `TileEntity`，你还可以这么做：

```java
TileEntity tile = world.getTileEntity(pos);
if (tile instanceof TileEntityLockableLoot) {
    ((TileEntityLockableLoot)tile).setLootTable(lootTableID, randomSeed);
}
```

对于箱子等，这会令其在下一次被打开时从对应的 Loot Table 中抽取物品并放入容器中。抽取结束后这些容器会清除它们被设定的 Loot Table ID。

### 注册新的战利品表

你可能已经发现了，`LootTableList` 下的战利品表都是“注册”过的，用途是 `LootTableManager` 中的 `reloadLootTables` 方法会自动加载这些“注册”过的战利品表。

```java
// 这个路径展开后就是 /assets/my_mod/loot_tables/custom.json。
// 是的，和工作台合成及进度一样，因为数据包跳票，所以被迫放在 /assets 目录下，且路径硬编码。
LootTableList.register(new ResourceLocation("my_mod", "custom"));
```
