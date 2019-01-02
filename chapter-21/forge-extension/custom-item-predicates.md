## 自定义物品匹配（`ItemPredicate`）

有一个非常简单的问题：如果你希望玩家物品栏里有铜锭的时候解锁某个进度，该怎么写？  
很明显，原版 Minecraft 已经有 `minecraft:inventory_changed` 这个 criterion 了，我们可以复用它来实现对物品栏检测。然后我们要检测一个铜锭，按常识来说我们应该支持任何有 `ingotCopper` 这个矿物词典名的物品…… 等等，这个 criterion 好像不支持矿物词典。难道除了重新造轮子就只能 GG 了？并没有。[Forge PR#4188](https://github.com/MinecraftForge/MinecraftForge/pull/4188) 给出了一套不用重新造 Criterion 的方案。

```json
{
  "criteria": {
    "example": {
      "trigger": "minecraft:inventory_changed",
      "conditions": {
        "items": [
          {
            "type": "forge:ore_dict",
            "ore": "ingotCopper"
          }
        ]
      }
    }
  }
}
```

与此同时它还追加了一个非常简单的自定义 Item Predicate 的系统，可用于解决类似问题，免去重新造 Criterion 的麻烦。

```java
import net.minecraft.advancements.critereon.ItemPredicate;
import net.minecraftforge.advancements.critereon.ItemPredicates;

public final class CustomItemPredicate {
    // 这个方法只需要在所有进度加载入游戏之前调用即可。
    public static void init() {
        // 第二个参数接受的是 java.util.function.Function<JsonObject, ItemPredicate>
        ItemPredicates.register(new ResourceLocation("my_mod", "custom"), CustomItemPredicate::fromJson);
    }

    public static ItemPredicate fromJson(JsonObject json) {
        return ...;
    }
}
```
