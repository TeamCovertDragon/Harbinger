# 状态效果与属性修饰符

虽然状态效果的具体逻辑是需要通过其他手段（事件订阅、方块更新、物品使用等）来实现的，但有一小部分原版状态效果的逻辑是直接利用[生物实体属性](../chapter-08/attribute/modifier.md)实现的。
具体来说，这些状态效果的逻辑是通过在生物实体获得状态效果后，调用 `Potion.applyAttributesModifiersToEntity`（`func_111185_a`）来实现的。原版有这些状态效果是通过这个方式实现的：

  - 速度（`MobEffects.SPEED`），提升生物移动速度（`SharedMonsterAttributes.MOVEMENT_SPEED`）
  - 迟缓（`MobEffects.SLOWNESS`），降低生物移动速度
  - 急迫（`MobEffects.HASTE`），（忽略挖掘速度）提升攻击速度（`SharedMonsterAttributes.ATTACK_SPEED`）
  - 挖掘疲劳（`MobEffects.MINING_FATIGUE`），（忽略挖掘速度）降低攻击速度
  - 力量（`MobEffects.STRENGTH`），提升攻击力（`SharedMonsterAttributes.ATTACK_DAMAGE`）
  - 虚弱（`MobEffects.WEAKNESS`），降低攻击力
  - 生命提升（`MobEffects.HEALTH_BOOST`），提升生命值上限（`SharedMonsterAttributes.MAX_HEALTH`）
  - 幸运（`MobEffects.LUCK`），提升幸运值（`SharedMonsterAttributes.LUCK`）
  - 霉运（`MobEffects.UNLUCK`），降低幸运值

如果你的 `Potion` 的效果也是影响某个属性，可以考虑使用类似的方法实现。
只需要调用 `Potion.registerPotionAttributeModifier` 即可：

```java
// 第一个参数 attribute 即 IAttribute 实例。
// 第二个参数 uuid 是 String 形态的 UUID。
// 第三个参数和第四个参数同 AttributeModifier 构造器的最后两个参数。
//
// registerPotionAttributeModifier 返回 Potion 它本身，可组成链式调用。
Potion myPotion = new Potion()
        .registerPotionAttributeModifier(attribute, uuid, amount, operation)
        .setRegistryName("my_mod:a_weird_potion");
```
