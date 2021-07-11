# `Container` 与数据同步

默认，GUI 是纯客户端的概念。这意味着，当一名玩家打开了一个 GUI 时，服务器对此是一无所知的。  
但 Minecraft 中有不少 GUI 是会显示玩家背包中的物品的。玩家对背包里的物品进行操作时，操作的结果是需要反馈回服务器的。
网络通信不可避免。Minecraft 给出的解决方案是 `Container`——一套专门用来简化这种需要服务器与客户端之间保持通信的 GUI 的编写机制。  
如果你在写某个方块的 GUI，你几乎不可能避免使用 `Container`——因为你十有八九需要显示玩家背包内物品。

## `IGuiHandler`

在正式介绍 `Container` 之前有必要先介绍一下 `IGuiHandler`。
这是 FML 提供的一套历史悠久的简易机制，通过它我们就可以通过一个 `openGui` 的调用来简单让客户端正确打开 GUI 并建立与服务器之间对应的 `Container` 的连接，而不用头疼幕后的数据包等乱七八糟的东西。

```java
import javax.annotation.Nullable;
import net.minecraft.entity.player.EntityPlayer;
import net.minecraft.world.World;
import net.minecraftforge.fml.common.network.IGuiHandler;

public enum MyGUIHandler implements IGuiHandler {

    INSTANCE;

    private MyGUIHandler() {
        // 注册。推荐的调用时机是 FMLPreInitializationEvent 或者 FMLInitializationEvent。
        // 这里我们写在构造器里好了。
        NetworkRegistry.INSTANCE.registerGuiHandler(ExampleMod.instance, this);
    }

    @Nullable //感谢 mezz 的 MinecraftForge/MinecraftForge#3550
    @Override
    public Object getServerGuiElement(int ID, EntityPlayer player, World world, int x, int y, int z) {
        // 返回的对象必须是一个 Container。
        // 见 NetworkRegistry.getRemoteGuiContainer 中的强制转型。
        return null;
    }

    @Nullable //感谢 mezz 的 MinecraftForge/MinecraftForge#3550
    @Override
    public Object getClientGuiElement(int ID, EntityPlayer player, World world, int x, int y, int z) {
        // 返回的对象必须是一个 GuiScreen。
        // 见 FMLClientHandler.showGuiScreen 中的强制转型。
        return null;
    }
}
```

### 简单的使用

```java
EntityPlayer player = ...;
World world = ...;
if (!world.isRemote) {
    // 第二个参数即是 IGuiHandler 两个方法的第一个 int 参数。
    // 最后的 posX、posY 和 posZ 则是 IGuiHandler 两个方法中的最后三个参数。
   player.openGui(ExampleMod.instance, 0, world, posX, posY, posZ);
}
```

### 四个 int？

第一个 int 参数理论上是指你的 Mod 内部的 GUI 编号，剩下三个 int 参数理论上是指 `TileEntity` 在指定维度（`World`）里的坐标。这样设计的理由虽然不得而知，但很明显它简化了带 GUI 的 `TileEntity` 的编写难度……

但理想总是与现实有差距。根据[某个 Forge 的 Issue Ticket][ref-1]，最后的三个 `x`、`y`、`z` 不一定非得是坐标——它们可以拿来代表任意数据。换言之，你可以根据总长度 128 bit 的信息来确定应该打开哪个 GUI……

[ref-1]: https://github.com/MinecraftForge/MinecraftForge/issues/3228

## `GuiContainer` 与 `Container`

就 Container 和 GuiContainer 之间的关系，ustc-zzzz 有一张图（FMLTutor § 3.4.1）很好地对其进行了解释：

![Container 和 GuiContainer 的关系](https://fmltutor.ustc-zzzz.net/resources/gui_analysis.png)

虽然你完全可以直接看 zzzz 的教程，但为方便起见，我们在这里重新复述一遍：

  * 序号 1 是指客户端的操作发包至服务器。
  * 序号 2 是指客户端对 `Slot` 的操作由客户端侧的 `Container` 实例代理。
  * 序号 3 是指 `Container` 操作它控制的 `Slot`……
  * 序号 4 是指 `Slot` 的操作结果返回给 `Container`。
  * 序号 5 是指服务器端的业务发信（乱七八糟诸如物品和“进度条”这种）给客户端。
  * 序号 6 是指客户端的操作都会通知服务器端。
  * 虚线是 Mojang 用它的黑魔法帮你搞定了。
  * 实线是 Mojang 表示这个你要自己来。

<!-- 感谢潜渣指出的这里潜伏已久的一个问题，见 Harbinger PR GH-55) -->

### 一个空架子

我们拿第九章里的 `LavaFurnace` 来做例子：

```java
public class LavaFurnaceContainer extends Container {
    @Override
    public boolean canPlayerInteractWith(EntityPlayer player) {
        // 决定了指定玩家是否能与这个 Container 交互。
        // 返回 false 会阻止玩家正常使用 GUI。
        return true;
    }
}

public class LavaFurnaceGui extends GuiContainer {
}
```

### 显示玩家背包

### 显示机器内物品

### 进度条
