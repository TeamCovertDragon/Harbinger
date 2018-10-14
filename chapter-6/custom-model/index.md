## 自定义模型

这里所谓的“自定义模型”，是指完全基于新的 `IBakedModel` 的实现产生的方块或物品模型。搭配 decorator pattern 来实现新的 `IBakedModel` 是比较常见的策略。重新从零开始生成 `IBakedModel` 需要的 `BakedQuad` 并非不可能，但需要的工作量会十分巨大，通常你也不需要走这弯路。  
自定义模型的应用场景也很明确：原版模型加 Forge BlockState V1 表达不了的东西往往需要一个新的 `IBakedModel` 的逻辑来搞定。最经典的例子是匠魂的工具。[ForgeDocs 关于这一部分的内容](https://mcforge.readthedocs.io/en/latest/models/advanced/introduction/)讲得很全面，可以拿来参考。

### 为了搞定 `IBakedModel`，你需要了解这些东西：

 - [`IModel`](unbaked.md)
 - [`IBakedModel`](baked.md)
 - `IExtendedBlockState`、`IUnlistedProperty`
 - [`ItemOverrideList`](item-override.md)
 - [`IModelLoader`](loader.md)
