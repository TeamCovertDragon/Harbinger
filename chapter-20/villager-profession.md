## 村民职业

原版的村民职业是写死进 `EntityVillager` 的。经过 Forge 一番 patch 后我们可以通过 Forge 的注册表来注册新的村民职业了。

```java
import net.minecraftforge.fml.common.registry.VillagerRegistry;

@SubscribeEvent
public static void onVillagerProfessionRegistration(RegistryEvent.Register<VillagerRegistry.VillagerProfession> event) {
    // VillagerRegistry.VillagerProfession 的构造器接受三个 ResourceLocation 作为参数：
    // 第一个 ResourceLocation 是注册名。因为某些原因，它的构造器会调用 setRegistryName。
    // 第二个 ResourceLocation 指定了有这个职业的村民的纹理。必须指明 png 后缀。
    // 第三个 ResourceLocation 指定了有这个职业的僵尸村民的纹理。必须指明 png 后缀。
    event.getRegistry().registerAll(new VillagerRegistry.VillagerProfession("my_mod:weird_profession", "my_mod:textures/entity/villagers/weird_profession.png", "my_mod:textures/entity/zombie_villagers/weird_profession.png"));
}
```

### “子职业”（Career）

每一个村民职业（Profession）又会被细分为各种“子职业”（Career），比如原版农民（Farmer）被细分为了普通农民（Farmer）、渔夫（Fisherman）、牧羊人（Shepherd）和制箭师（Fletcher）。子职业的相关信息是直接存储在对应的职业下的。

```java
import net.minecraftforge.fml.common.registry.VillagerRegistry;

@SubscribeEvent
public static void onVillagerProfessionRegistration(RegistryEvent.Register<VillagerRegistry.VillagerProfession> event) {
    VillagerRegistry.VillagerProfession profession = event.getRegistry().getValue(new ResourceLocation("minecraft", "farmer"));
    VillagerRegistry.VillagerCareer newCarrrer = new VillagerRegistry.VillagerCareer(profession, "fisherman");
}
```

### 村民交易

一个卖鱼给 fisherman 的交易：

```java
newCarrrer.addTrade(1, new EntityVillager.ListItemForEmeralds(new ItemStack(Items.FISH), new PriceInfo(1, 1)));
```

注意到 `addTrade` 方法的第二个参数接受的是 `EntityVillager.ITradeList trades...`。在 `EntityVillager` 下有这些现成的实现可用：

  * `ItemAndEmeraldToItem`，用于沙砾和绿宝石换燧石这样的交易。
  * `ListEnchantedBookForEmeralds`，用于图书管理员卖附魔书。
  * `ListEnchantedItemForEmeralds`，用户铁匠卖工具和护甲。
  * `ListItemForEmeralds`，绝大多数物品换绿宝石或绿宝石换物品的交易都是用的这个。
  * `TreasureMapForEmeralds`，地图师卖地图用到了这个。

除此之外我们可以自己实现 `ITradeList` 来实现更高级的交易。

```java
public final class MyCustomTradeList implements EntityVillager.ITradeList {
    public void addMerchantRecipe(IMerchant merchant, MerchantRecipeList trades, Random random) {
        // 注意到 MerchantRecipeList 继承了 ArrayList<MerchantRecipe>，
        // 所以这个方法的实现其实就是向 trades 这个 List<MerchantRecipe> 中加新元素的操作了。
    }
}
```
