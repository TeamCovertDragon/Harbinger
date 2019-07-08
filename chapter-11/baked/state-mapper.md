# 自定义方块状态映射方式

考虑以下几个情况：

  - 我的方块有若干方块状态，但所有方块状态的外观都是一致的（即只有一个模型）。如此一来，我仍然需要把这些属性对应的情况都列出来。有没有办法做到省略不写？
  - 出于某种原因，我希望 BlockState json 和 model json 可以一一对应，能否做到？
  - 出于某种原因，我想让某个特定方块状态使用的模型来自另一个 block state json。能否做到？

Minecraft 它本身也遇到了这样的奇怪需求，因此 `IStateMapper` 应运而生。它用于指定 `IBlockState` 和 `ModelResourceLocation` 之间的对应关系。在 Minecraft 1.12 中，所有的 `Block` 都有一个默认的 `IStateMapper` 实现，这个默认的实现也直接决定了原版的 BlockState json 的格式是这样的：

```json
{
    "variants": {
        "facing=west": { "model": "..." },
        "facing=east": { "model": "..." },
        "facing=north": { "model": "..." },
        "facing=south": { "model": "..." }
    }
}
```

<!-- 注：随着 BlockState JSON 格式的改进，IStateMapper 也在 1.13 中被淘汰掉了。 -->

## `BlockStateMapper.registerBlockStateMapper`

你需要通过 `BlockStateMapper.registerBlockStateMapper`（`func_178447_a`）来注册你自己的 `IStateMapper` 实现。

```java
BlockStateMapper.registerBlockStateMapper(ModBlocks.myBlock, block -> {
    // 返回值为 Map<IBlockState, ModelResourceLocation>。
    // 所以每一个可能的 IBlockState 都应对应一个 ModelResourceLocation。
    return ...;
})
```

## 现成的实现：`StateMap`

但是要手动构造一个包含所有可能性的 `Map<IBlockState, ModelResourceLocation>` 显得太过麻烦了。Minecraft 自己便有这样一个工具类可以快速解决一些简单的映射关系。

```java
BlockStateMapper.registerBlockStateMapper(ModBlocks.myBlock,
    new StateMap.Builder().ignore(...).withName(...).withSuffix("...").build());
```

其中：

  - `ignore` 用于忽略指定的 `IProperty`
  - `withName` 的效果是根据 `IProperty` 的值把一个 BlockState json 展开成多个。举例，如果有一个方块叫 `machine`，有 `TYPE` 属性，其值可以是 `MACHINE_A`, `MACHINE_B` 和 `MACHINE_C`，那么 `withName(TYPE)` 会令 Minecraft 尝试寻找下面三个 BlockState json 而不是 `machine.json` 下的 `type=machine_a` 这样的 variant：
    - `machine_a.json`
    - `machine_b.json`
    - `machine_c.json`
  - `withSuffix` 会在原有的 `ModelResourceLocation` 的 `location` 基础上加一个后缀。

## 现成的半成品：`StateMapperBase`

与此同时，Minecraft 还有一个 `StateMapperBase` 可以简化 `IStateMapper` 的实现。  
`StateMapperBase` 是个抽象类，它没有实现的方法叫 `getModelResourceLocation`（`func_178132_a`）——它接受一个 `IBlockState` 并返回对应的 `ModelResourceLocation`。

```java
BlockStateMapper.registerBlockStateMapper(ModBlocks.myBlock, new StateMapperBase() {
    @Override
    protected ModelResourceLocation getModelResourceLocation(@Nonnull IBlockState state) {
        // 是不是比 IStateMapper 更符合直觉了？
        return new ModelResourceLocation(new ResourceLocation("my_mod", ...), ...);
    }
});
```

这个类经常用在处理流体方块的方块状态与模型之间的映射上，在[第二十七章的有关章节](../../chapter-27/render.md)会再次提到。
