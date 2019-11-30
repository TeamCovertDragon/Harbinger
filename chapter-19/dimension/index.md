# 新维度

实现新维度可以说是 Minecraft Mod 中最具神秘感的话题——不仅是因为有关它的资料的确少得可怜，还因为添加新维度的 Mod 也本来就不多。

## 从 `DimensionType` 开始

```java
import net.minecraft.world.DimensionType;
import net.minecraftforge.common.DimensionManager;

public static int dimID = 233;
public static DimensionType myDim;

@Mod.EventHandler
public void preInit(FMLPreInitializationEvent event) {
    // 第一个参数代表此维度的内部名称。
    // 第二个参数是村庄等数据保存时会用到的“当前维度的专有后缀名”。
    // 第三个参数是维度 ID。维度 ID 是基于 Minecraft 1.12.2 的 Forge Mod 开发中
    // 仍然需要手动指定数字 ID 的游戏内容，也因此几乎所有的 Modder 都会允许通过配置文件
    // 修改维度 ID。
    // 第四个参数是该维度使用的 WorldProvider 的类。要求这个类有零参构造器。
    // 第五个参数代表“是否保持该维度的 spawn 区块一直加载”。
    myDim = DimensionType.register("my_dimension", "_my_dim", dimID, MyWorldProvider.class, false);
    // 在拿到自己的维度的 myDimType 后，需要再向 Forge 告知这一新的 DimensionType 的存在，
    // 否则某些 Forge 加入的功能会无法在你的新维度中工作。
    // 第一个参数仍然是维度 ID，和上面保持一致即可。
    DimensionManager.registerDimension(dimID, myDim);
}
```

## `WorldProvider`

是的，一个新的维度必须要有一个 `WorldProvider` 的实现。关于它的具体实现[很快就会讲到](dimension.md)，这里我们暂时先用一个和主世界完全一致的 `WorldProvider`：

```java
public final class MyWorldProvider extends WorldProviderSurface {
    @Override
    public final DimensionType getDimensionType() {
        return myDimType;
    }
}
```
