## 高级物品实例

这一部分将介绍一些比较普遍的特殊物品的实现方式。

### 复用现有的类

虽然这不是在说具体的实现，但这一点仍然相当重要：复用现有的类。比方说我要写一个纯粹的合成材料，像原版铁锭和钻石那样的东西。这些物品没有任何额外的行为，那这个时候你完全可以直接复用原版的 `Item` 类，而不必再新写一个。

```java
event.getRegistry().register(new Item().setCreativeTab(myTab).setRegistryName("my_first_item"));
```

或者弄个匿名类，以获得额外的行为？？

```java
event.getRegistry().register(myItem = new Item() {
    // 右击一下就会播放恶魂尖叫的音效。
    @Override
    public ActionResult<ItemStack> onItemRightClick(World world, EntityPlayer player, EnumHand hand) {
        world.playSound(null, player.posX, player.posY, player.posZ, SoundEvents.ENTITY_GHAST_SCREAM, SoundCategory.NEUTRAL, 0.5F, 0.4F / (itemRand.nextFloat() * 0.4F + 0.8F));
    }
}.setCreativeTab(myTab).setRegistryName("troll"));
```
