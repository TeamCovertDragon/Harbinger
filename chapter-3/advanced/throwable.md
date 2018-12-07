## 投掷物（例如雪球）

这个涉及到一点实体的内容。关于实体的细节会在后面的章节中讲到。

```java
public class ItemInfiniteSnowball extends Item {
    public ItemSnowball() {
        this.setCreativeTab(CreativeTabs.MISC);
    }

	// 当玩家拿着这无限雪球右键的时候会调用这个方法。我们在这里实现丢雪球的效果。
    @Override
    public ActionResult<ItemStack> onItemRightClick(World world, EntityPlayer player, EnumHand hand) {
        ItemStack item = player.getHeldItem(hand);
		// 播放雪球被抛出去的声音——这个会在后面的章节中详细解释
        worldIn.playSound(null, player.posX, player.posY, player.posZ, SoundEvents.ENTITY_SNOWBALL_THROW, SoundCategory.NEUTRAL, 0.5F, 0.4F / (itemRand.nextFloat() * 0.4F + 0.8F));
		// world.isRemote 用于判断是服务端还是客户端，这里我们要做的逻辑显然应该只在服务端执行
        // 这个字段的细节在第十一章有详细阐述。
        if (!worldIn.isRemote) {
        	//生成雪球实体——关于实体的内容在第七章会详细解释，我们现在丢雪球就好了
            EntitySnowball snowball = new EntitySnowball(world, player);
            snowball.shoot(player, player.rotationPitch, player.rotationYaw, 0.0F, 1.5F, 1.0F);
            worldIn.spawnEntity(snowball);
        }
        // 互动成功，返回EnumActionResult.SUCCESS，item 是互动结束以后的 item
        // 因为这是个可以无限丢的雪球，所以这里数量没有减去 1。减去 1 的话丢出去就会少一个。
        // 自然地，减去 2 的话丢出去就会少两个。
        return new ActionResult<>(EnumActionResult.SUCCESS, item);
    }
}
````
