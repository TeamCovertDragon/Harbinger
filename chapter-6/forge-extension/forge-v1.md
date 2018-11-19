### Forge BlockState V1 格式

考虑这样一个方块状态定义，这个方块是一种加工设备，可以面朝东南西北，有开关两种工作状态：

```json
{
    "variants": {
        "facing=north,status=on": { "model": ".../machine_on", "y": 0 },
        "facing=north,status=off": { "model": ".../machine_off", "y": 0 },
        "facing=south,status=on": { "model": ".../machine_on", "y": 180 },
        "facing=south,status=off": { "model": ".../machine_off", "y": 180 },
        "facing=west,status=on": { "model": ".../machine_on", "y": 270 },
        "facing=west,status=off": { "model": ".../machine_off", "y": 270 },
        "facing=east,status=on": { "model": ".../machine_on", "y": 90 },
        "facing=east,status=off": { "model": ".../machine_off", "y": 90 }
    }
}
```

这么一来总共有八种可能的 Block State。是不是感觉重复的内容特别多？简化的办法自然是有的——Forge 提供的 BlockState 格式。关于这个格式的说明并不多，甚至 ForgeDocs 上也只提及了一小部分。曾经负责 Forge 的渲染相关的 RainWarrior 曾写过一份它的[规范][link-spec]，但留下的注释并不多。本文将基于这份规范撰写，并尽力给出清晰的解释。

[link-spec]: https://gist.github.com/RainWarrior/0618131f51b8d37b80a6

#### 言归正传，来看简化后的版本

```json
{
    "forge_marker": 1,
    "variants": {
        "status": {
            "on": { "model": ".../machine_on" },
            "off": { "model": ".../machine_off" }
        },
        "facing": {
            "north": { "y": 0 },
            "south": { "y": 180 },
            "west": { "y": 270 },
            "east": { "y": 90 }
        }
    }
}
```

`forge_marker` 用来标记该 JSON 使用 Forge 的格式，并给出使用的格式的版本号。目前这个格式只有 V1 这一个版本，所以这里自然写了 1。然后就是不一样的地方了：`facing` 属性只定义了绕 Y 轴的旋转角度，而模型则只由 `status` 属性的值决定。Forge 会将这些属性反复组合起来生成所有可能的模型（其实就是个对这些属性求笛卡尔积的过程）。  
Forge BlockState V1 格式保留了一定程度上的对原版格式的兼容；那个 `y` 的行为和原版的一致。自然，原版的绕 X 轴以 90 度为单位旋转在 Forge BlockState V1 中也可以使用。甚至最开始那个原版格式的 JSON 加上 `"forge_marker": 1` 也符合 Forge BlockState V1 格式。

#### 默认属性

Forge BlockState V1 格式除了语法更简洁外，还支持更多特性。还是考虑刚才那个加工设备。如果它没有开关的状态，那么不论面朝哪里它都会是同一个模型，只是需要根据朝向转一转。Forge BlockState V1 允许你指定“默认值”：

```json
{
    "forge_marker": 1,
    "defaults": {
        "model": ".../machine"    
    },
    "variants": {
        "facing": {
            "north": { "y": 0 },
            "south": { "y": 180 },
            "west": { "y": 270 },
            "east": { "y": 90 }
        }
    }
}
```

这里的默认值便是要使用的模型。于是 Forge 会拿四种可能的状态与默认值相组合，得到最终对应的模型。直接在这里指定纹理路径也是可能的。

#### 子模型

Forge BlockState V1 格式允许你使用若干子模型，换言之可以把好几个互相独立的方块模型用这种方式拼起来。

```json
{
    "forge_marker": 1,
    "variants": {
        "status": {
            "on": {
                "model": ".../machine_on",
                "submodel": {
                    "part1": {
                        "model": ".../extended_part"
                    }
                }
            },
            "off": { "model": ".../machine_off" }
        },
        "facing": {
            "north": { "y": 0 },
            "south": { "y": 180 },
            "west": { "y": 270 },
            "east": { "y": 90 }
        }
    }
}
```

这样一来，这个设备工作的时候就会“长”出点东西。`part1` 在这里只是起到一个命名的作用；如果你需要多个子模型，你大可以在 `submodel` 里塞 `part2`、`part3`、……  
唯一的遗憾是子模型似乎不能在 `defaults` 中使用，原因未知。

#### TRSRTransformation 与仿射变换（Affine Transformation）

Forge BlockState V1 格式还允许你对模型（自然也包括子模型）做仿射变换。为简明起见，从这里开始，本文不给出完整的 BlockState JSON。

```json
{
    "...": "...",
    "defaults": {
        "model": ".../target_model",
        "transform": [
            [ 1, 0, 0, 0 ],
            [ 0, 1, 0, 0 ],
            [ 0, 0, 1, 0 ]
        ]
    },
    "...": "..."
}
```

最右边的 column 用于 translation。左侧的 3x3 矩阵不多解释。也可以写成这样：

```json
"transform": {
    "matrix": [
        [ 1, 0, 0, 0 ],
        [ 0, 1, 0, 0 ],
        [ 0, 0, 1, 0 ]
    ]
}
```

但是这样写起来的可读性多数时候并不好。所以还有另一种形式的写法可用：TRSRTransformation。[TRSR 是 "translation, rotation, scale, rotation" 的首字母缩略词。][citation-trsr]

[citation-trsr]: https://github.com/SlimeKnights/Mantle/blob/13695e8464b1f110c47f06fb141021ce3118f143/src/main/java/slimeknights/mantle/client/model/TRSRBakedModel.java#L30

```json
{
    "...": "...",
    "defaults": {
        "model": ".../target_model",
        "transform": {
            "translation": [ 0, 0, 0 ],
            "rotation": [ { "x": 0 }, { "y": 0 }, { "z": 0 } ],
            "scale": [ 1, 1, 1 ],
            "post-rotation": [ { "x": 0 }, { "y": 0 }, { "z": 0 } ]
        }
    },
    "...": "..."
}
```

其中：

  - 所有字段都是可选的，不需要的变换都可以直接省略。
  - `rotation` 和 `post-rotation` 字段可使用四元数 `[ x, y, z, w ]`
  - `rotation` 和 `post-rotation` 字段亦可直接赋值为类似 `{ "x": 0 }` 的形式，表示只在唯一一个指定的轴上转。
  - `scale` 可直接赋值为一个数，表示 uniform scale。使用 `[ 1, 1, 1 ]` 形式则表示在对应的轴上缩放（x、y、z）。

#### 不同视角下的变换

`transform` 字段可以定义不同视角下需要应用的不同变换；这个特性类似于原版方块模型中的 `display` 字段，但相比原版方块模型，Forge BlockState V1 的版本可以使用仿射变换或 TRSRTransformation。

```json
{
    "...": "...",
    "transform": {
        "thirdperson": { "matrix": "..." },
        "thirdperson_lefthand": { "matrix": "..." },
        "thirdperson_righthand": { "matrix": "..." },
        "firstperson": { "matrix": "..." },
        "firstperson_lefthand": { "matrix": "..." },
        "firstperson_righthand": { "matrix": "..." },
        "head": { "matrix": "..." },
        "gui": { "matrix": "..." },
        "ground": { "matrix": "..." },
        "fixed": { "matrix": "..." }
    },
    "...": "..."
}
```

其中 `thirdperson` 是 `thirdperson_righthand` 的 shortcut，`firstperson` 是 `firstperson_righthand` 的 shortcut，剩余字段的含义可直接对照 [Minecraft Wiki 上的说明][ref-transform-type]来理解。

[ref-transform-type]: https://minecraft-zh.gamepedia.com/%E6%A8%A1%E5%9E%8B#.E6.96.B9.E5.9D.97.E6.A8.A1.E5.9E.8B

#### 预设的变换模版

上文中提到的  `transform` 字段其实还可以直接赋值为 String，此时 Forge 会使用对应的预设模板对模型进行变换。

```json
{
    "...": "...",
    "defaults": {
        "model": ".../target_model",
        "transform": "identity"
    },
    "...": "..."
}
```

`identity` 自然是返回模型它本身的单位变换。其他可用的变换还有 `forge:default-block`（用于方块及 `ItemBlock`）、`forge:default-item`（用于一般物品）和 `forge:default-tool`（用于镐、斧这样的工具）。  
有一点需要注意：`forge:default-block`、`forge:default-item` 和 `forge:default-tool` 已经完整定义了 `thirdperson`、`firstperson`、`head`、 `ground`、`gui`、`fixed` 等等场景下的变换，所以上文中提到的“不同视角下的变换”是不能用这三个模板的，只有 `identity` 可用。想想看也能知道允许的话会显得十分奇怪。

##### `ForgeBlockStateV1.Transforms`

14.23.5.2772 中你可以通过 `ForgeBlockStateV1.Transforms` 的 `get` 方法拿到上文中提到的 `forge:default-block`、`forge:default-item` 和 `forge:default-tool` 三个模板对应的 `IModelState` 对象。如此一来，可以通过 `IModelState` 的 `apply` 方法拿到不同视角下的 `TRSRTransformation`（封装在一个 `Optional` 中，因为的确有可能不存在），在某些时候这个数据相当有用。

#### 物品模型使用 Forge BlockState V1

没错，四种可用的变换模板里的 `forge:default-item` 实际上是可以给物品用的。具体来说，是这样操作的：

```java
ModelLoader.setCustomModelResourceLocation(myItem, 0, new ModelResourceLocation(new ResourceLocation("example_mod", "example_item_model"), "inventory"));
```

实际上这个 `ModelResourceLocation` 也可以代表一个 BlockState JSON 的位置。比如上面这个例子中的 `ModelResourceLocation` 也可以指向 `assets/example_mod/blockstates/example_item_model.json` 中定义的 `variants.inventory`。事实上，Minecraft/Forge 也的确是会搜索这里的。  
但这里有一个坑：`variants` 同时也包含了所有方块状态中的属性列表，直接声明成这样会有歧义：

```json
{
    "...": "...",
    "variants": {
        "inventory": { "...": "...", "transform": "forge:default-item" }
    }
}
```

这究竟是一个完整的 variants，还是一个方块状态中的特定属性？[Forge BlockState V1 没有解决这个问题][ref-forge-4797]，但这个问题有一个 workaround：

[ref-forge-4797]: https://github.com/MinecraftForge/MinecraftForge/issues/4797

```json
{
    "...": "...",
    "variants": {
        "inventory": [{
            "...": "...",
            "transform": "forge:default-item"
        }]
    }
}
```

注意到数组的声明。在原版的 BlockState JSON 格式中这代表从数组中随机抽一个出来作为实际使用的模型。因为某些原因，使用这样的声明要求对应的键必须是完整的 variant 名，因此这样写可以消去歧义。
