## 实体渲染

所有实体的渲染都基于名为 `Render` 的类，它实际上是对一系列 `GL1X` 系列固定管线操作的一个总封装，Minecraft 只需要根据实体的具体类型找到对应的 `Render` 的实现并调用 `doRender`（`func_76986_a`）即可。具体渲染的细节完全不透明。

```java
public class MyEntityRenderer extends Render<MyEntity> {
    public MyEntityRenderer(RenderManager manager) {
        super(manager);
    }

    @Override
    public void doRender(MyEntity entity, double x, double y, double z, float entityYaw, float partialTicks) {
        // 具体的渲染逻辑全部在此发生。
    }

    @Override
    protected ResourceLocation getEntityTexture(MyEntity entity) {
        // 你需要通过这个方法来声明你使用的实体渲染的纹理。
        // 实际上这个方法是必须要实现的，因为这个方法声明的时候有 abstract 修饰。
        // 但这里有一个坑：Minecraft 的渲染系统并不会主动调用这个方法！
        // 你需要在 doRender 里调用 bindEntityTexture（func_180548_c）来切换当前的纹理上下文到你的纹理上。
        return MY_ENTITY_TEXTURE;
    }

    private static final ResourceLocation MY_ENTITY_TEXTURE = new ResourceLocation("example_mod", "textures/entity/first_entity.png");
}
```

### 与实体绑定

Minecraft 并没有暴露出绑定实体渲染的相关方法。为此 Forge 出面通过一行 patch 解决了这个问题——之后我们可以通过 Forge 的 `RenderingRegistry` 来把 Mod 的实体绑定到某一个特定的 `Render` 上去了。

```java
// 实际上 registerEntityRenderingHandler 的调用时机没有明确限制，
// 但出于统一的考虑，这里建议通过订阅 ModelRegistryEvent，并在它发布的时候注册。
// 如此一来所有的方块、物品和实体渲染相关都能在一个时间处理完毕。
@SubscribeEvent
public static void bindEntityRenderer(ModelRegistryEvent event) {
    // 自然也可以用 method reference。这里写成 lambda 只是为了更加明显。
    RenderingRegistry.registerEntityRenderingHandler(MyEntity.class, manager -> new MyEntityRenderer(manager));
}
```
