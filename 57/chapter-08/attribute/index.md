# 生物实体属性（Attribute）

生物实体的属性（Attribute）是指实体身上一系列描述生物“特征”的数值。这些数值包括：

  - 生命值上限
  - 跟随距离
  - 移动速度
  - 飞行速度
  - 攻击力
  - 攻击速度
  - 护甲
  - ……

在中文 Minecraft Wiki 上可以找到[关于它的详细说明][ref-1]，本文将重点解释它在 Mod 开发中的意义。  
若没有特殊说明，本文出现的“属性”一词均指只适用于生物实体的 Attribute 系统。

[ref-1]: https://minecraft-zh.gamepedia.com/%E5%B1%9E%E6%80%A7

## 生物限定

如名字所示，只有生物实体（`instanceof EntityLivingBase`）才有属性。所以就不用指望矿车之类的东西还能有属性了。  

## 享元

属性系统中也可以见到享元模式（Flyweight Pattern）的影子：

  - 每一种属性都由 `IAttribute` 接口描述。
  - 每一种属性的具体实例则由 `IAttributeInstance` 接口描述。
  - `IAttribute` 决定了某一属性的上下限和初始值等。
  - 操作属性的值时，我们修改的是 `IAttributeInstance`。

简单来说，`IAttribute` 之于 `IAttributeInstance` 就像是 `Item` 之于 `ItemStack`。

## 注册：不需要注册

`IAttribute` 无需注册，创建后即可拿去给生物实体用。
原版使用的实现是 `RangedAttribute`，我们可以直接拿来用。

```java
// 第一个参数指定一个别的 IAttribute 作为它的 parent。一般都是填 null，
// 但如果你的属性真的是从别的属性衍生而来的话，建议加上。原版会通过
// IAttribute.getParent() 把所有 parent 属性一并加入到目标实体上。
// 第二个参数是 id，兼职 translation key。不要和别的属性重名了。
// 第三个参数是该属性的初始值。
// 第四个参数指定下限：该属性最低必须是这个值。
// 第五个参数指定上限：该属性最高只能是这个值。
//
// setShouldWatch 决定了该属性的值的变化是否需要同步至逻辑客户端。
// setShouldWatch(false) 会令逻辑客户端看不到该属性的变化。
// 如果你不希望客户端看到这个值，或者客户端不需要知道这个值，可以用它
// 来稍微节约一点点带宽。
public static final IAttribute MAGIC_POWER = new RangedAttribute(null, "my_mod.attribute.magic", 0.0, 0.0, 20.0).setShouldWatch(false);
```

在原版的 `SharedMonsterAttributes` 类下可以找到原版实体使用的各种 `IAttribute`，
虽然我们也可以直接拿来用，但是在 `EntityLivingBase` 中这些属性大都已经在构造阶段添加好了，所以我们直接让我们的新生物继承 `EntityLivingBase` 即可。

## 初始化

上文提到 `EntityLivingBase` 在构造阶段就添加了若干属性，具体来说是 `applyEntityAttributes`（`func_110147_ax`）方法：

```java
@Override
protected void applyEntityAttributes() {
    super.applyEntityAttributes();
    // 这个 registerAttribute 的含义更像是 addAttribute。
    // 它只是在声明“这个实体拥有这个属性”。
    this.getAttributeMap().registerAttribute(...);
}
```

如果你想为你的生物添加全新的自定义属性，在这里添加就没错了。

如果你是想给原版实体添加新属性的话…… 可以考虑使用 `EntityEvent.EntityConstructing`：

```java
@SubscribeEvent
public static void entityInit(EntityEvent.EntityConstructing event) {
    final Entity e = event.getEntity();
    if (e instanceof EntityLivingBase) {
        final EntityLivingBase living = (EntityLivingBase) e;
        living.getAttributeMap().registerAttribute(...);
    }
}
```

## 获取当前属性数值

```java
EntityLivingBase something = ...;
double attack = something.getAttributeMap().getEntityAttribute(SharedMonsterAttributes.ATTACK_DAMAGE).getAttributeValue();
```
