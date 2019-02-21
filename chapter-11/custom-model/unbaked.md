## `IModel`

`IModel` 代表了一个模型的最初形态。这个模型还没有经过处理（即所谓的“bake”（烘培）过程），其具体细节（UV 锁、平滑光照/ambient occlusion、GUI 中是否有 3D 效果、使用的纹理、`IModelState`/仿射变换状态等等）都有可能发生变化。

```java
public class ABrandNewModel implements IModel {

    private final ResourceLocation modelLocation;

    public ABrandNewModel(ResourceLocation location) {
        this.modelLocation = location;
    }

    @Override
    public IBakedModel bake(IModelState state, VertexFormat format, Function<ResourceLocation, TextureAtlasSprite> bakedTextureGetter) {
        return aBakedModel;
    }
}
```

而产生 `IBakedModel` 的过程便是在这里。在这个方法中可以拿到最终使用的 `IModelState`、模型使用的顶点格式（VertexFormat）和一个用于获取 `TextureAtlasSprite` 的高阶函数对象。（当然…… Java 语言中没有 First-class function，这里说高阶函数实际上并不严谨。）

### `retexture`（更换纹理）

```java
public IModel retexture(ImmutableMap<String, String> textures) {
    return this;
}
```

覆盖这个方法后，在模型 bake 的过程中可以通过 Forge BlockState V1 中单个 variant 下的 `textures` 字段覆盖现有纹理，该方法传入的 ImmutableMap 实际上也正是 `textures` 字段代表的 JSON Object 的 `Map` 形态。典型的使用案例是 Forge 的 `ModelDynBucket`，用于 Forge 提供的 Universal Bucket 的物品模型。

### 自定义数据

```java
@Override
public IModel process(ImmutableMap<String, String> customData) {
    return this;
}
```

覆盖这个方法后，在模型 bake 的过程中可以读取到一系列额外的数据；这些数据需要通过 Forge BlockState V1 中单个 variant 下追加一名为 `custom` 的 JSON Object 字段来提供。典型的使用案例是 Forge 提供的流体模型（关于流体的内容会在第二十六章中提到），它通过这个自定义数据来获知目标流体的具体名称：

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
