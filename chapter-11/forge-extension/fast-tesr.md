## `FastTESR`

`FastTESR` 的使用和普通 TESR 基本一致，但有一处非常关键的不同：

```java
public class CrazyFastRenderer extends FastTESR<MyCrazyTileEntity> {
    @Override
    public void render(MyCrazyTileEntity tile, double x, double y, double z, float partialTicks, int destroyStage, float alpha, BufferBuilder bufferBuilder) {
        // 在这里你可以拿到这些信息：
        // 1. TileEntity 的坐标（x, y, z），以 double 的形式出现
        // 2. partialTicks // TODO 它的具体含义该怎么解释？
        // 3. destroyStage 大约是“方块被破坏的进度”的意思 // TODO 查清楚
        // 4. alpha // TODO 可能是“透明度”的意思，待查
        // 5. bufferBuilder：这便是 FastTESR 被称之为 FastTESR 的秘诀所在，所有要渲染的
        // 数据都应统一走这里，而非直接用 Tessellator 反复 draw，实际上你也不应该这么做。
        // 这个 BufferBuilder 收集的是顶点数据，数据的格式可在 VertexFormat 类中找到线索。
        // 所有的数据都必须统一按格式传入，否则会有非常诡异的渲染效果。
    }
}
```

### 将 FastTESR 与 TileEntity 绑定

因为 `FastTESR` 也继承 `TileEntitySpecialRenderer`，所以它的注册与普通 TESR 无异。  
但仍然需要注意一点：对于绑定了一个 `FastTESR` 的 TileEntity，其 `hasFastRenderer` 方法应返回 `true`：

```java
// 在 TileEntity 类中覆写这个 Forge patch 出来的方法
@Override
public boolean hasFastRenderer() {
    return true;
}
```
