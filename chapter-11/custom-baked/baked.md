## `IBakedModel`

`IBakedModel` 是 Minecraft 中所有方块和物品模型的基础。它的核心在于一个获取 `List<BakedQuad>` 的方法，而这个 `List<BakedQuad>` 则代表了一系列已经准备就绪，丢给显卡就可以渲染的 Quad 数据。

```java
public class BakedBrandNewModel implements IBakedModel {
    @Override
    public List<BakedQuad> getQuads(@Nullable IBlockState state, @Nullable EnumFacing side, long rand) {
        // 核心方法：获得 List<BakedQuad>
        // 第一个参数自然就是 IBlockState，可以在这里获得要渲染的方块的方块状态。
        // 第二个参数指定了要获取的 List<BakedQuad> 是哪个方向上的。参考原版方块六个面就不难想象这是什么意思了。
        // 第三个参数是…… 随机数…… https://xkcd.com/221/ 实际上在某些时候它和方块的位置有关。
        return Collections.emptyList(); // 返回这个就可以让这模型啥都渲染不出来。都没数据了，渲染什么？
    }

    @Override
    public boolean isAmbientOcclusion()
    {
        // 平滑光照/环境光遮蔽。
        return true;
    }

    @Override
    public boolean isGui3d()
    {
        // GUI 内 3D 效果，但笔者至今都看不出差别。
        return true;
    }

    @Override
    public boolean isBuiltInRenderer()
    {
        // 当返回 true 时，将调用 TileEntityItemStackRenderer 来进行绘制。
        // 99% 的时候你都应该返回 false。
        // 关于 TileEntityItemStackRenderer 的信息可参考本章 Forge 扩展部分的 TEISR 一节。
        return false;
    }

    @Override
    public TextureAtlasSprite getParticleTexture()
    {
        return null;
        // 实际上你应该返回点什么的。它代表了方块破坏时、玩家在上面奔跑
        // 或玩家摔上面等情况下使用的粒子效果的纹理。
    }

    @Override
    public ItemOverrideList getOverrides()
    {
        // 获取该模型的物品 Override 列表，详见对 ItemOverrideList 的介绍。
        // 一般情况下只需要返回 ItemOverrideList.NONE。
        return ItemOverrideList.NONE;
    }
}
```
