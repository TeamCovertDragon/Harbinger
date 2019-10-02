# 世界类型（`WorldType`）

“世界类型”指的是你在新建世界时选择的生成类型，也就是说下面中的一个：

  - 普通
  - 超平坦
  - 巨型生物群系
  - 放大化
  - 自定义
  - 调试

如果你玩过 Biomes 'O Plenty，你也许知道你需要将世界类型指定为 BOP 才能在世界中生成 BoP 的生物群系。
不同的世界生成类型会对应不同的 `IChunkGenerator`。原版将这之间的映射关系写死在了 `WorldProvider` 中，但在 Forge patch 过有关的类后，我们便能在 `WorldType` 中指定。

## 你的世界类型

```java
public final class MyWorldType extends WorldType {
    public MyWorldType() {
        // 顺带一提，这个构造器也是 Forge patch 进去的。
        // 你需要传入的是这个 WorldType 的唯一识别 id，长度不能超过 16 个字符。
        // 小心不要和别的 Mod 的撞车了。
        super("my_world_type");
    }
}
```

然后只需要在 Mod 加载阶段构造一个它的实例即可。它的“注册”流程是在构造器中完成的。

```java
@Mod.EventHandler
public void preInit(FMLPreInitializationEvent event) {
    new MyWorldType().enableInfoNotice();
    // 没必要保留它的实例。
    // 你随时可以通过 WorldType.byName("my_world_type") 拿到你在这里创建的实例。
}
```

同时，别忘了在语言文件里加上它的名字和它的提示信息：

```
generator.my_world_type=My World
generator.my_world_type.info=It is mine!
```

## 指定世界生成

现在你有了一个新的世界类型，但选择它生成出的世界仍然和普通世界没什么两样。
我们需要覆盖若干方法来让这个 `WorldType` 使用我们自己的世界生成器：

```java
public final class MyWorldType extends WorldType {
    public MyWorldType() {
        super("my_world_type");
    }

    // 返回该世界类型所用的 BiomeProvider，决定了生物群系如何生成。
    // 原版 Minecraft 中 WorldType.FLAT 会使用 BiomeProviderSingle。
    @Override
    public BiomeProvider getBiomeProvider(World world) {
        return ...;
    }

    // 返回该世界类型所用的 IChunkGenerator，决定了世界生成。
    // 原版 Minecraft 中 WorldType.FLAT 会使用 ChunkGeneratorFlat。
    // 此方法决定的是主世界的世界生成。
    // 你可以尝试在这里返回 new ChunkGeneratorHell(world, true, world.getSeed())
    // 使得主世界“调用”下界的世界生成。
    @Override
    public IChunkGenerator getChunkGenerator(World world, String generatorOptions) {
        return ...;
    }
}
```

## 一些杂七杂八的数据

```java
public final class MyWorldType extends WorldType {

    public MyWorldType() {
        super("my_world_type");
    }
    // 决定了云的高度。
    @Override
    public float getCloudHeight() {
        return 233F;
    }

    // 决定了“海平面”的高度。注意这里有对应的 World 实例。
    @Override
    public double getHorizon(World world) {
        return 100F;
    }

    // 随机决定出生点时，出生点的最低高度。注意这里有对应的 World 实例。
    @Override
    public int getMinimumSpawnHeight(World world) {
        return 100F;
    }

    // 随机决定新玩家出生点时的“干扰因数”。
    // 注意这里有对应的 World 实例及当前的 MinecraftServer。
    @Override
    public int getSpawnFuzz(WorldServer world, MinecraftServer server) {
        return 6;
    }

    // 从世界底部开始到建造高度极限 Y = 256 为止，在哪一个高度虚空迷雾最浓。
    // 0.666 代表迷雾在 Y = 256 * 0.666 = 170.496，也就是 Y = 170 或 171 附近最浓。
    // 另见：WorldProvider#getVoidFogYFactor（func_76565_k）
    @Override
    public double voidFadeMagnitude() {
        return 0.666;
    }
}
```

## 关于超平坦自定义菜单

Forge patch 过后的 `WorldType` 允许你也给自己的 `WorldType` 弄一个所谓的“个性化”菜单。

```java
public final class MyWorldType extends WorldType {

    public MyWorldType() {
        super("my_world_type");
    }

    // 首先要让这个方法返回 true，代表“该世界类型可定制”。
    @Override
    public boolean isCustomizable() {
        return true;
    }

    @Override
    public void onCustomizeButton(Minecraft mc, GuiCreateWorld parentGui) {
        // 然后这个方法就会被调用了。
        // 在这里打开你的定制界面即可。
    }
}
```
