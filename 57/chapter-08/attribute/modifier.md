# 实体属性修饰符

实体属性修饰符（Attribute Modifier）是一种修改当前实体属性的值的机制。
通过这套（简单的？）机制我们可以为任何生物实体（自然包括玩家本身）提供正面或负面效果。

## 创建

```java
// 我们首先需要一个能和别的修饰符区分开的 UUID。
// 有鉴于 UUID 的性质，我们在网上随便找个生成器生成一个就行了。
// 如果随机生成的 UUID 还能和别的修饰符撞车的话，建议去买彩票。
public static final UUID MODIFIER_ID = UUID.fromString("b44157cd-2a86-4dc4-ae8a-9820767db864");

// 第一个参数是刚才的 UUID。实际上可以省略，Minecraft 默认会随机生成一个。
// 随机生成的 UUID 影响也不大，只是不稳定。
// 第二个参数是我们一看就知道是干什么的名字。不能是 null。
//
// 第三个参数（double value）和第四个参数（int operation）共同决定了这个修饰符会如何影响该属性：
//   - operation == 0 时，原属性值 + value = 新属性值。
//   - operation == 1 时，原属性值 + 原属性值 * value = 新属性值。
//   - operation == 2 时，原属性值 * (1 + value) = 新属性值。
AttributeModifier mod = new AttributeModifier(MODIFIER_ID, "my_modifier", 1.0, 0);

// 如果你希望这个修饰符在退出游戏/服务器关闭时不保存，请调用 setSaved(false)。
// 原版默认保存所有修饰符，疾跑加速的修饰符是一个例外。
// 通常来说你可以无视这个的存在……
mod = mod.setSaved(false);
```

### 最后两个参数到底是什么意思？？？

假如说某个属性当前值为 10，且拥有下列修饰符：

|修饰符名称|运算类型（operation）|操作数（value）|
|:--|:--|:--|
|Foo|0|2|
|Bar|0|-4|
|Baz|1|0.5|
|Thonk|1|1|
|Donk|2|0.25|
|Wut|2|0.25|

那么这个属性实际生效的值是这样计算的：

  - 首先结算运算 0（Foo 和 Bar）：10 + 2 + (-4) = 8
  - 然后结算运算 1（Baz 和 Thonk）：8 + 8 * 0.5 + 8 * 1 = 20
  - 最后结算运算 2（Donk 和 Wut）：20 * (0.25 + 1) * (0.25 + 1) = 31.25

因此该属性实际值为 31.25。

### 幻数

Forge 提供了对应的常量以消灭幻数的使用。
这些常量位于 `net.minecraftforge.common.util.Constants` 下：

|运算类型|常量|
|:--|:--|
|0|`Constants.AttributeModifierOperation.ADD`|
|1|`Constants.AttributeModifierOperation.ADD_MULTIPLE`|
|2|`Constants.AttributeModifierOperation.MULTIPLY`|

## 为指定属性追加或删除修饰符

```java
EntityLivingBase living = ...;
IAttributeInstance attr = living.getAttributeMap().getAttributeInstance(...);
// 追加修饰符
attr.applyModifier(...);
// 删除修饰符
attr.removeModifier(...);
```

有必要指出的是，若是通过这种方式手动添加修饰符，则也需要通过这种方式手动删除修饰符，否则修饰符会一直存在。
然而，在原版底层中，有些透过此系统追加修饰符的方式不需要我们自己手动清理，因为原版自己已经有清理修饰符相关的代码了。

### 物品与修饰符

装备在身上或手持的物品也可以为当前实体提供修饰符。这些修饰符受 Minecraft 底层自动管理，我们只需要在 `Item` 类下覆写一个方法就行了：

```java
@Override
public Multimap<String, AttributeModifier> getAttributeModifiers(EntityEquipmentSlot slot, ItemStack stack) {
    ArrayListMultimap<String, AttributeModifier> modifiers = ArrayListMultimap.create();
    // 往 modifiers 里塞东西就可以改攻击改防御改各种属性了
    // slot 决定了当前物品被装备到了什么地方（四个护甲格、主手或副手）
    // 而 stack 则是具体的物品，你可以根据物品当前的 NBT 数据判断
    // 应该追加什么修饰符。
    // 你只需要发挥你的想象力……
    return modifieres;
}
```

### 药水效果与修饰符

`registerPotionAttributeModifier` 方法可以令药水作用于生物实体上时将指定修饰符附加到该实体上，药水过期后移除。

```java
new Potion(...).registerPotionAttributeModifier(
        SharedMonsterAttributes.ATTACK_DAMAGE, // 目标属性类型
        "b44157cd-2a86-4dc4-ae8a-9820767db864", // 字符串形式的 UUID
        1, Constants.AttributeModifierOperation.ADD // 同 AttributeModifier 构造器第 3、4 个参数
);
```
