## 状态效果与属性修饰符

虽然状态效果的具体逻辑是需要通过其他手段（事件订阅、方块更新、物品使用等）来实现的，但有一小部分原版状态效果的逻辑和原版的[属性（Attributes）](https://minecraft-zh.gamepedia.com/%E5%B1%9E%E6%80%A7)有关系。他们的逻辑是依靠在实体更新的某个时候靠用 `applyAttributesModifiersToEntity`（`func_111185_a`）实现的。如果你的状态效果也是用于修改某个属性的，可以考虑使用类似的方法实现：

```java
// attributeToken 是个 IAttribute。原版所用的 IAttribute 可在 SharedMonsterAttributes 中找到。
// uuidString 是 String 形态的 UUID。千万别和别的 UUID 重了。
// amount 和 operation 的含义请参考 MinecraftWiki。
Potion myPotion = new Potion()
        .registerPotionAttributeModifier(attributeToken, uuidString, amount, operation)
        .setRegistryName("my_mod:a_weird_potion");
```
