### 图形化用户交互界面

````java
public class MyContainer extends Container {
}

public class MyGui extends GuiContainer {
}
````

这就是 Minecraft 自己使用的 GUI 系统的基础：一个客户端显示的 `GuiContainer`（准确地说，应该是 `GuiScreen`），以及可选的 `Container`，用于逻辑服务器。

### 第一步：为什么要有 Container？
当且仅当你需要和服务器打交道时。

比方说一个工作台。你可以用这个 GUI 合成东西！所以必须有服务器端的业务逻辑，不然你合成的物品全是客户端特效你骗谁呢你。

就 Container 和 GuiContainer 之间的关系，ustc-zzzz 有一张图很好的对其进行了解释：

![我是图](https://fmltutor.ustc-zzzz.net/resources/gui_analysis.png)

（屏幕前的读者你要是直接看 Markdown 源文件的话你就会发现这图直接来自他教程… 准确地说是3.4.1的第一张插图。）  
虽然你完全可以直接看 zzzz 的教程，但我还是再用自己的理解复述一遍吧：

* 序号 1 是指客户端的操作发包至服务器。
* 序号 2 是指客户端对 Slot 的操作由客户端侧的 Container 实例代理。
* 序号 3 是指 Container 操作它控制的 Slot……
* 序号 4 是指 Slot 的操作结果返回给 Container。
* 序号 5 是指服务器端的业务发信（乱七八糟诸如物品和“进度条”这种）给客户端。
* 序号 6 是指客户端的操作都会通知服务器端。
* 实线是 Mojang 用它的黑魔法帮你搞定了。
* 虚线是 Mojang 表示这个你要自己来。

````java
public class MyContainer extends Container {
    // 此方法必须覆写，因为父类里这是个抽象方法。
    @Override
    public boolean canPlayerInteractWith(EntityPlayer player) {
        return true;
        // 返回 false 的时候会给你关掉 GUI。
    }
}
````

### 且慢！我不需要和服务器打交道啊！
那就直奔 `GuiContainer` 好了。想想看，一本游戏内置的手册多数时候不需要服务器端有什么操作吧……

### 组成GUI的元件 (Components)
说是这么说，其实能称得上 Components 的东西真的不多。
#### 背景
#### 文本框
`GuiTextField`
#### 按钮
`GuiButton`
#### 滚动菜单
#### 滑块
#### 勾选框是啥来着？
你可以使用按钮来模拟勾选框。

### 那如果我的 GUI 里还要和服务器打交道呢？

你需要一个 `Container`。不需要的话也许也可以，但是很多事情就需要重新从零开始写。

### 等等！我怎么打开GUI？
````java
import javax.annotation.Nullable;
import net.minecraft.entity.player.EntityPlayer;
import net.minecraft.world.World;
import net.minecraftforge.fml.common.network.IGuiHandler;

public enum MyGUIHandler implements IGuiHandler {
    INSTANCE;

    private MyGUIHandler() {
        NetworkRegistry.INSTANCE.registerGuiHandler(ExampleMod.instance, this);
    }

    @Nullable //感谢 mezz 的 MinecraftForge/MinecraftForge#3550
    @Override
    public Object getServerGuiElement(int ID, EntityPlayer player, World world, int x, int y, int z) {
        return null;
    }
    @Nullable //感谢 mezz 的 MinecraftForge/MinecraftForge#3550
    @Override
    public Object getClientGuiElement(int ID, EntityPlayer player, World world, int x, int y, int z) {
        return null;
    }
}
````

以及发挥点想象力。并非只有ID可以存储数据，最后的三个 `x`、`y`、`z` 并非总是坐标——它们也是可以拿来存储数据的。换言之，你在返回对应的 GUI 对象时最多可以用到 4 个整数的信息（总长度128 bit）来确定应该打开哪个 GUI…… [参考MinecraftForge/MinecraftForge#3228](https://github.com/MinecraftForge/MinecraftForge/issues/3228)。  
最后只需要在有 EntityPlayer 的地方这么做：

````java
//playerIn是个EntityPlayer
//5不是arbitary number，是上面IGuiHandler中的id，具体含义由实现决定
//worldIn是个World
//最后的三个xyz没有强制要求是坐标，可用于传入别的数据
playerIn.openGui(ExampleMod.instance, 5, worldIn, pos.getX(), pos.getY(), pos.getZ());
````
