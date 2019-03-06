## 流体容器

和第三章提到的物品一样，流体也是有对应的容器的。流体容器可以分为两种：

  - [方块形式的流体容器](block.md)
  - [物品形式的流体容器](item.md)

### 流体桶

有一个非常奇怪的现象，那就是几乎所有的 Mod 在加新流体时，都会不约而同地仿照原版的风格加一个对应的“某某桶”物品。  
写一个这样的桶实际上并不简单：它右击时会放出流体方块，同时自身变成原版空桶，而原版空桶右击液体时会变成装某某流体的桶。首先你需要让自己的桶能放置自己的流体方块，这个比较简单。然后你发现你需要让“原版空桶”能在右击对应流体方块后变成自己的“某某桶”，也就是说你需要订阅某种事件，或使用注册表覆盖来实现和原版一样的空桶装填。注册表覆盖显然会影响到其他 Mod 的处理，所以订阅某个事件成了上策。<!-- 一点历史：曾经有这么一个专门处理这个问题的事件叫 `BucketFillEvent` -->是不是已经开始头疼了？
后来在几个 Modder <!-- 具体来说是 RWTema、boni-xx、mezz -->的不懈努力下，Forge 有了一个所谓的“万能桶”（Universal Bucket）物品，从此 Modder 们再也不需要一样的代码复制几十遍了。如果你需要的也只是这样一个桶，只需要这么做就可以了：

```java
FluidRegistry.addBucketForFluid(myFluid);
```

但万能桶这个功能默认禁用。需要至少有一个 Mod 在 `FMLPreInitializationEvent` 之前显式要求启用此功能。通常可选择在Mod主类的构造器，或静态初始化块中，显式开启：

```java
@Mod(modid = "example_mod", name = "Example Mod")
public class ExampleMod {
    public ExampleMod() {
        FluidRegistry.enableUniversalBucket();
    }
}
````

成功注册的流体桶可在原版创造模式物品栏的“杂项”底下找到这些桶。  
此外，`addBucketForFluid` 这个方法会同时尝试注册传入的流体，但 `addBucketForFluid` 这个方法没有返回值。请自行取舍。

<!--
可能有人会问，这不是和 Forge 的“不添加游戏内容”相冲突了吗？
这实际上正是这个功能默认禁用的原因。只有当某个 Mod 要求启用这个功能时，这个物品才会被注册。
自然地，为方便起见，Forge 也就顺势使用了它自己的 modid。
-->
