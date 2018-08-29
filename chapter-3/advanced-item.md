### 更高级的物品
嘛，高级的物品一箩筐，怎么说呢，先从一些奇葩的开始吧。

#### 复用现有的类
事实上，如果你只是需要一种没有任何逻辑的新物品（比如原版的木棍、铁锭、钻石这样）的话，复用现成的类就可以了。

````java
IForgeRegistry<Item> registry = event.getRegistry();

Item myItem = new Item().setCreativeTab(myTab).setRegistryName("my_first_item");
registry.register(myItem);

Item myTool = new ItemTool(...); // 注意到 ItemTool 是 Item 的派生类
registry.register(myTool);
````

或者弄个匿名类？

````java
Item myItem = new Item() {
    //最后一个boolean是指F3+H快捷键启用的高级Tooltip
    @Override
    public void addInformation(ItemStack stack, EntityPlayer player, List<String> tooltips, boolean enableHiddenTooltip) {
    }
}.setCreativeTab(myTab).setRegistryName("my_first_item");
registry.register(myItem);
````

#### Meta Hack

几乎所有的 Modder 都会使用这样的技术来避免占用过多的物品 ID。下面是这个 hack 的具体实现。原版中也有类似的代码可以参考。

```java
public class MultiMetaItem extends Item {
    public MultiMetaItem() {
        super();
        //声明此物品有“子类型”，即有Metadata值。
        //没错就是这么简单...
        //声明之后物品即拥有Meta值。最小为0，最大是Short.MAX_VALUE，即32767。
        //最大值同时也有一个神奇的用途，将在讲到OreDictionary时解释。
        //在范围内的Meta都是合法的。只是它的材质会变成紫黑，然后可能会导致游戏崩溃而已。
        setHasSubtype(true);
        //将最大损害值设定为0，避免一些奇妙的“修复物品”产生的刷物品 Bug。
        setMaxDamage(0);
        //防止被修复。
        setNoRepair();

        //注：
        //损害值和Metadata完全不是一个概念。
        //但一直都是耦合在一起的。准确地说，共用一个字段。
        //换言之，能损坏的工具，是不能同时拥有 Metadata 的，除非耐久度以 NBT 存储。（代表是沉浸工程的工程师系列工具）
        //但一般都会调用setMaxDamage和setNoRepair以防奇葩问题出现。
    }

    //让带多个Meta的物品显示在创造模式物品栏里。
    @SideOnly(Side.CLIENT) //这个的解释在后面的章节中会讲到
    @Override
    public void getSubItems(Item item, CreativeTabs tabs, List<ItemStack> list) {
        // ItemStack表示“一堆数量若干的物品”。
        // 构造器中，第一个参数可以是Item也可以是Block（有对应的重载）
        // 第二个参数是数量。第三个参数即是meta值。
        // 若省略第三个参数则默认为0。
        // 若省略第二个参数则第三个参数也必须省略，默认为数量1，meta为0。
        list.add(new ItemStack(item, 1, 0));
        list.add(new ItemStack(item, 1, 1));
    }
}
```

End。

#### 可以吃的东西（含水瓶这种表面上应该是“喝下去”的东西）

```java
public class MyFood extends ItemFood {
    // 待补全
}
```

#### 护甲

````java
public class MyCloth extends ItemArmor implements ISpecialArmor {

    /**
     * @param material “护甲材质”，Enum类型，必须借助EnumHelper获得自定义的实例，除非复用原版的护甲材质。
     * @param renderType 幻数
     * @param slot 这片护甲穿哪？头上？身上？腿上？脚上？Enum类型。
     */
    public MyCloth(ArmorMaterial material, int renderType, EntityEquipmentSlot slot) {
        super(material, renderType, slot);
    }

    @Override
    public String getArmorTexture(ItemStack stack, Entity entity, EntityEquipmentSlot slot, String type) {
        //为了材质！
    }

    @Override
    public void onArmorTick(World world, EntityPlayer player, ItemStack itemStack) {
        //穿在身上的时候的每时每刻都会调用
    }

    // ISpecialArmor 接口实现开始

    @Override
    public ArmorProperties getProperties(EntityLivingBase player, ItemStack armor, DamageSource source, double damage, int slot) {
        return new ArmorProperties(0, 1.0, 100);
    }

    @Override
    public int getArmorDisplay(EntityPlayer player, ItemStack armor, int slot) {
        return 0;
    }

    @Override
    public void damageArmor(EntityLivingBase entity, ItemStack stack, DamageSource source, int damage, int slot) {

    }

    // ISpecialArmor 接口实现结束
}
````

#### 可以右击丢出去的东西（例如雪球）

这个涉及到一点实体的内容。关于实体的细节会在后面的章节中讲到，这里先用原版的雪球讲解吧。

````java
public class ItemSnowball extends Item
{
    public ItemSnowball()
    {
    	//字面意思
        this.maxStackSize = 16;
        this.setCreativeTab(CreativeTabs.MISC);
    }
	//当玩家拿着雪球右键的时候我们应该做的事情
    public ActionResult<ItemStack> onItemRightClick(World worldIn, EntityPlayer playerIn, EnumHand handIn)
    {
    	//通过EntityPlayer::getHeldItem 方法，我们可以获得当前玩家手持的物品——这里当然是雪球，对应的ItemStack实例
        ItemStack itemstack = playerIn.getHeldItem(handIn);
		//如果玩家不是创造模式那么先让物品的数量减少1
        if (!playerIn.capabilities.isCreativeMode)
        {
            itemstack.shrink(1);
        }
		//播放雪球被抛出去的声音——这个会在以后详细解释
        worldIn.playSound((EntityPlayer)null, playerIn.posX, playerIn.posY, playerIn.posZ, SoundEvents.ENTITY_SNOWBALL_THROW, SoundCategory.NEUTRAL, 0.5F, 0.4F / (itemRand.nextFloat() * 0.4F + 0.8F));
		//world.isRemote用于判断是服务端还是客户端，这里我们要做的逻辑显然应该只在服务端执行
        if (!worldIn.isRemote)
        {
        	//生成雪球实体——以后会详细解释
            EntitySnowball entitysnowball = new EntitySnowball(worldIn, playerIn);
            entitysnowball.shoot(playerIn, playerIn.rotationPitch, playerIn.rotationYaw, 0.0F, 1.5F, 1.0F);
            worldIn.spawnEntity(entitysnowball);
        }
		//TODO 这是啥我也不知道
        playerIn.addStat(StatList.getObjectUseStats(this));
        //互动成功，返回EnumActionResult.SUCCESS，itemstack是互动结束以后的itemstack(这里是数量减去1以后的itemstack)
        return new ActionResult<ItemStack>(EnumActionResult.SUCCESS, itemstack);
    }
}
````

#### 工具、武器、任何有耐久的东西

````java
public class MyTool extends ItemTool {
    // 待补全
}
````

#### 附加篇：你持盾吗
从 https://github.com/MinecraftForge/MinecraftForge/pull/4126 合并之后，我们可以自定义盾牌了。

````java
//在Item的子类中覆写：
@Override
public boolean isShield(ItemStack stack, @Nullable EntityLivingBase entity) {
    //注意它传入了盾牌本身和持盾的玩家。
    //换言之，你可以在这个时候对这玩意搞各种事情
    return true;
}
````
