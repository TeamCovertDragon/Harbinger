# 流体渲染相关

因为流体并非原版 Minecraft 固有的系统，所以和流体相关的渲染实际上并不那么符合直觉。

## 流体没有模型

有模型的是流体对应的方块。既然有模型的是方块，那么只需要参考第十一章的内容就可以了。  
Forge 提供了一个已经实现好的流体模型，可以拿来就用。有鉴于这个模型的实现方式比较特殊，它必须通过 Forge BlockState V1 格式引用方能正常工作。下面给出了这个流体模型的使用方法：

```java
ModelLoader.setCustomStateMapper(myFluid.getBlock(), new StateMapperBase() {
    @Override
    protected ModelResourceLocation getModelResourceLocation(@Nonnull IBlockState state) {
        return new ModelResourceLocation(new ResourceLocation("my_mod", "fluid"), "my_fluid");
    }
});
```

注意到 `setCustomStateMapper` 将目标方块指向了一个特殊的 `StateMapper`，它进而无条件将模型指向 `assets/my_mod/blockstates/fluid.json` 中定义的 `my_fluid` 这个 variant。下面是 `fluid.json` 中的内容：

```json
{
    "forge_marker": 1,
    "variants": {
        "my_fluid": {
            "model": "forge:fluid",
            "custom": {
                "fluid": "my_fluid"
            }
        }
    }
}
```

很多 Mod 都会通过类似的方式将所有流体方块的模型整合在一起方便管理。

## 流体有纹理

注意到 `Fluid` 有几个接受若干 `ResourceLocation` 的构造器。它们用于指定流体的纹理。接受两个 `ResourceLocation` 的代表流体的静止纹理和流动纹理不一致；只接受一个的则是有统一的纹理，不论静止还是流动。  
如果我们的流体没有对应的方块，那么这几个纹理实际上是不会自动被识别并载入原版的 Texture Map 的。但有时候我们又需要依赖这些纹理——比如说绘制 GUI 中的流体储罐的时候——此时我们不得不手动确保这些纹理会载入原版方块及物品的 Texture Map 中。

```java
@SubscribeEvent
public static void regFluidSpirit(TextureStitchEvent.Pre event) {
    TextureMap textureMap = event.getTextureMap();
    textureMap.registerSprite(myFluid.getFlowing());
    textureMap.registerSprite(myFluid.getStill());
}
```
