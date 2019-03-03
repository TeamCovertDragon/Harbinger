## 地图生成特性（World Gen. Feature）

回想一下原版中的地图生成。看起来有一些东西的生成是非常有规律的：比如矿石生成，它们一定生成在某个特定的高度，服从某一种特定的离散或连续分布；再比如甘蔗，一定出现在水边；以及粘土<!-- balance! -->，一定是出现在河床、湖床这样的地方；还有下界的荧石，它们总是倾向于挂天花板上。

实际上它们的确是有规律的。因为这些东西有一个共同的抽象层：世界生成特性（World Generation Feature）。我们可以简称它为地图特性（Feature）。从代码的角度来看，它们均由某个 `WorldGenerator` 的衍生类实现。

### `WorldGenerator`

`WorldGenerator` 代表了一种在新世界和/或新区块中生成某种按特定方式放置方块的算法。有这样一个东西后，我们就可以通过持有一系列 `WorldGenerator` 并调用 `generate` 方法来在目标世界中“生成”（也就是放置）一系列有特定结构的方块了。所以我们先写一个 `WorldGenerator`。

```java
public class MyFirstWorldGenFeature extends WorldGenerator {

    public MyFirstWorldGenFeature() {
        // 这个 true 的含义是“在调用 setBlockAndNotifyAdequately 时会造成方块更新”。
        // setBlockAndNotifyAdequately 是 WorldGenerator 类提供的一个 util method，
        // 可以根据实际需求给 setBlockState 提供不同的更新选项。
        super(true);
    }

    @Override
    public boolean generate(World world, Random rand, BlockPos position) {
        // 所有放置方块的逻辑均在这里发生。
        return false;
    }
}
```

### Strategy pattern

`WorldGenerator` 和世界生成其实没什么联系。它只是封装了一系列放置方块的操作，具体是谁在用它其实是无所谓的，只要有一个 `World` 实例、一个起点坐标和一个合适的伪随机数发生器就行了。所以 `WorldGenerator` 实际上只是一种“放置方块的策略”，并不是专职负责地图生成的。两者之间唯一的联系只是“按特定方式放置方块”。  
一个非常典型的例子是原版树苗——原版树苗方块（`BlockSapling`）中就复用了若干种 `WorldGenerator` 的实现（都是生成原版树的）来避免重复的代码。  
同样的理由，`WorldGenerator` 也不需要注册，因为没有意义，需要的时候 new 一个，或者用别的地方提前 new 好的就可以了。

### Cascading World Generation

注意，通常情况下你需要给 x 和 z 坐标各加 8。换言之，即：

```java
BlockPos correctedCenter = position.add(8, 0, 8);
```

这样做的原因是，如果你在没有生成的区块放置方块，它会把这个区块先生成出来，然后在生成这个区块的时候相同的事情又发生了……
听上去是不是像遇到了递归？没错，这意味着这样的连锁反应一定会严重拖慢世界生成。
这种现象被称作 Cascading World Generation（或缩写 Cascading World Gen）。
这个问题可以严重到什么程度呢？[你可以参考 mezz 写过的一份报告][ref-mezz-report]。

那么问题出现了，为什么给 x 和 z 各加 8 就能解决问题？出于某种原因，那个生成的中心点（即 `position` 参数）实际上是在要生成的区块的左上角，而已生成的区块则位于这个区块的右侧、下侧及右下侧。给 x 和 z 各加 8 后就会让这个中心点向已生成的区块靠拢。对于大多数地图特性使用的 `nextInt(16)` 来说这已经足够用了。  

[ref-mezz-report]: https://redd.it/5x0twz
