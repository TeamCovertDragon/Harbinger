## 食物及饮料

```java
public class MyFood extends ItemFood {
    public MyFood(int hungerHeal, float saturation, boolean isWolfFood) {
        super(hungerHeal, saturation, isWolfFood);

        // ItemFood 的默认实现中，只有在玩家饥饿条不满的时候才能吃东西。
        // 用这个可以解除此限制。
        // this.setAlwaysEdible();
    }

    @Override
    protected void onFoodEaten(ItemStack stack, World world, EntityPlayer player) {
        // 会在玩家食用之后被调用，原版金苹果在这里追加多种药水效果
    }
}
```
