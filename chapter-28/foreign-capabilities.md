## 第三方 `Capability` 注入

考虑一下这么一个情况：

```java
import awesome.power.mod.api.AwesomePowerItemContainer;

public class WeirdItem extends Item {
    public ICapabilityProvider initCapabilities(ItemStack item, NBTTagCompound data) {
        return new AwesomePowerItemContainer(item);
    }
}
```

那么这里有一个潜在问题，这会让 JVM 尝试加载 `AwesomePowerContainer` 这个类，然后在没有装对应的 Mod 的情况下你会得到一个 `java.lang.ClassNotFoundException`。有没有怎么实现这种“可选的依赖”呢？  
答案是 `@CapabilityInject` 注解。

```java
import awesome.power.mod.api.AwesomePowerContainer;
import net.minecraftforge.common.capabilities.CapabilityInject;

public class TeslaCompatibility {
    // 不得不承认，这其实是个黑科技。注解中的类不是真正的 class 对象，泛型参数最终也会被擦除……
    @CapabilityInject(AwesomePowerContainer.class)
    public static void whenAwesomePowerAvailable(Capability<AwesomePowerContainer> cap) {

    }
}
```
