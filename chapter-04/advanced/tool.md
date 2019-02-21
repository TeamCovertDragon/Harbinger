## 工具、武器、盾牌、以及任何有耐久的东西

对于普通的工具来说，复用或继承 ItemTool 即可。

```java
public class MyTool extends ItemTool {
    // 待补全
}
```

### 附加篇：你持盾吗

从 https://github.com/MinecraftForge/MinecraftForge/pull/4126 合并之后，我们可以自定义盾牌了。

```java
@Override
public boolean isShield(ItemStack stack, @Nullable EntityLivingBase entity) {
    // 注意它传入了盾牌本身和持盾的实体。（注意到持盾的不一定是玩家！）
    // 换言之，你可以在这个时候对这玩意搞各种事情
    return true;
}
```
