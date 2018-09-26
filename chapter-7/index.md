## 实体

一句话：Minecraft 里会动的，但不是方块的东西，基本上都是实体。  
诸如你自己（玩家）、动物、地上掉的物品、经验球、怪物、矿车…… 都是实体。物品展示架和盔甲架也都是实体，虽然看起来像是方块。  

```java
public class MyEntity extends Entity {
    //未完待续
}
```

### 注册

实体的注册项通过一个叫 `EntityEntry` 的东西代理，而它是 `IForgeRegistryEntry`。所以：

```java
@SubscribeEvent
public static void onEntityRegistation(RegistryEvent.Register<EntityEntry> event) {
    event.getRegistry().register(EntityEntryBuilder.create()
            .entity(MyEntity.class)
            .id(new ResourceLocation(myModId, "my_entity"), 233)
            .name("MyEntity")
            .tracker(80, 3, false)
            .build()
    );
}
```

### 数据同步

所有 Mod 的实体都需要有这样一个构造器：

```java
public MyEntity(World world) {
    // 初始化
}
```

这实际上是 FML 的要求，因为 FML 接管了非原版实体的数据同步。相关的细节会在第十四章重新提到。
