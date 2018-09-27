### `TileEntitySpecialRenderer`

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
