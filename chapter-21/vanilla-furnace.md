## 原版熔炉配方

和原版工作台配方不一样，原版熔炉并没有使用专门的 Recipe 类或接口，而是单纯地使用 `FurnaceRecipes` 这样一个类来管理一个私有的 `Map<ItemStack, ItemStack>`。实际上这个类使用这个 Map 的方式更像是在用 `Collection<Map.Entry<ItemStack, ItemStack>>`，毕竟 ItemStack 不满足用作 Map 的键的要求。  
实际上我们甚至不需要去管 `FurnaceRecipes` 这个类，因为 FML 出手了。

```java
// inputItem 是输入的 ItemStack
// outputItem 是输出的 ItemStack
// expGained 是烧炼一次后玩家获得的经验数量，一定是 float。
GameRegistry.addSmelting(inputItem, outputItem, expGained);
```

### 燃料热值

在 Forge patch 过原版熔炉后，燃料热值由一个事件决定：

```java
@SubscribeEvent
public static void getVanillaFurnaceFuelValue(FurnaceFuelBurnTimeEvent event) {
    if (check(event.getItemStack())) {
        event.setBurnTime(100);
        // 可以设定为 0。0 代表“这个物品不是燃料”，更准确地说是“这个物品燃烧时间是 0”。
        // 可以设定为 -1。-1 代表“由原版逻辑来决定”。
        // 可通过 event.getBurnTime() 获得当前决定的燃烧时间。
        // 这个事件可以取消。取消意味着后续的 Event listener 将不会收到这个事件，进而
        // 无法修改燃烧时间。
    }
}
```
