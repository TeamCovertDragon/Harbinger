## `TileEntitySpecialRenderer`

`TileEntitySpecialRenderer` 是个抽象类，有一抽象方法 `render` 用于具体的渲染操作。注意它要求一个 `<T extends TileEntity>` 所以你必须要有一个 TileEntity。

```java
public class CrazyRenderer extends TileEntitySpecialRenderer<MyCrazyTileEntity> {
    @Override
    public void render(MyCrazyTileEntity tile, double x, double y, double z, float partialTicks, int destroyStage, float alpha) {
        // 在这里你可以拿到这些信息：
        // 1. TileEntity 的坐标（x, y, z），以 double 的形式出现
        // 2. partialTicks // TODO 它的具体含义该怎么解释？
        // 3. destroyStage 大约是“方块被破坏的进度”的意思 // TODO 查清楚
        // 4. alpha // TODO 可能是“透明度”的意思，待查
    }
}
```

渲染的操作就是对固定管线（主要是 `GL11` 系列的固定管线）的操作了。搞不定的话请去参考相关资料。

### 为什么是固定管线？！

没办法，Minecraft 底层制约。有鉴于笔者缺少相关方面的知识，这里不讨论关于这方面的更多信息。

### 将 TESR 与 TileEntity 绑定

```java
ClientRegistry.bindTileEntitySpecialRenderer(MyCrazyTileEntity.class, new CrazyRenderer());
```

理论上在任何时候都可以调用这个，但本教程建议是订阅 `ModelRegistryEvent` 事件，然后连同普通物品模型一起注册，这样显得比较统一。
