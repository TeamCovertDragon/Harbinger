## 附魔

```java
public class MyPowerfulEnchantment extends Enchantment {
    public MyPowerfulEnchantment(Enchantment.Rarity rarity, EnumEnchantmentType type, EntityEquipmentSlot[] slots) {
        super(rarity, type, slots);
        // rarity 代表了这个附魔的稀有程度，可以是 COMMON、UNCOMMON、RARE 或 VERY_RARE。
        // type 代表了这个附魔可以加在什么工具/武器/装备上。
        // slots 代表了“这个附魔加在什么格子里装的工具/武器/装备上才有效果”，例如荆棘只在盔甲四件套上有效。
        // slots 会影响 getEnchantedItem（func_92099_a）的返回值，这个方法用于获取某个实体上有指定附魔的物品。
    }

    // func_77319_d，决定了附魔的最低可能等级
    @Override
    public int getMinLevel() {
        return 1;
    }

    // func_77325_b，决定了附魔的最高可能等级
    @Override
    public int getMaxLevel() {
        return 3;
    }
}
```

和药水效果一样，附魔的注册受 Forge 注册表系统的控制。

```java
@SubscribeEvent
public static void onEnchantmentRegistration(RegistryEvent.Register<Enchantment> event) {
    event.getRegistry().registerAll(new MyPowerfulEnchantment().setName("super_duper_enchantment").setRegistryName(...));
    // setName（func_77322_b）用于组成附魔名称的本地化键，和注册名无关。本地化相关的内容请参考第 13 章。
}
```

### 如何让自己的附魔有效果？

通常，附魔的效果和附魔本身的类没有关系。举个例子，精准采集和时运的逻辑实际上是在 `Block` 里的。还记得 Forge patch 后的那个获得掉落的方法 `getDrops` 吗？它最后一个 `int` 参数就是当前使用工具的时运等级。  
这也意味着，你的附魔的具体效果需要通过覆写 `Block` 或 `Item` 类下的某些方法及事件订阅等方式实现。`EnchantmentHelper` 类提供了一些帮助确定某物品的附魔等级的方法，比如 `getEnchantmentLevel`（`func_77506_a`）和 `getEnchantments`（`func_82781_a`）。
