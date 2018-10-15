## 护甲

WIP。

````java
public class MyCloth extends ItemArmor implements ISpecialArmor {

    /**
     * @param material “护甲材质”，Enum类型，必须借助EnumHelper获得自定义的实例，除非复用原版的护甲材质。
     * @param renderType 幻数，写 0 即可，对 Mod 加的护甲毫无作用
     * @param slot 这片护甲穿哪？头上？身上？腿上？脚上？Enum 类型。
     */
    public MyCloth(ArmorMaterial material, int renderType, EntityEquipmentSlot slot) {
        super(material, renderType, slot);
    }

    @Override
    public String getArmorTexture(ItemStack stack, Entity entity, EntityEquipmentSlot slot, String type) {
        // 为了纹理贴图！
    }

    @Override
    public void onArmorTick(World world, EntityPlayer player, ItemStack item) {
        //穿在身上的时候的每时每刻都会调用的方法，可以用来追加药水效果什么的
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
