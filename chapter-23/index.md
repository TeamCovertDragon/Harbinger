## 命令

Minecraft 有一个命令系统。这个系统的功能就是它的字面意思：以纯文本的形式给 Minecraft 这个程序下达奇奇怪怪的命令，要求其完成。  
因为是纯文本，对大多数玩家来说，命令系统的入口就是普通的聊天窗口。所有以 `/` 开头的输入都会被视作是一条命令。关于原版命令的内容在 [Minecraft Wiki](https://minecraft-zh.gamepedia.com/%E5%91%BD%E4%BB%A4) 上已有详细介绍，这里不做赘述。

### 一个 Hello, world 风格的命令

一个 `ICommand` 的实现代表了一个具体的命令。所以首先要实现 `ICommand` 接口：

````java
// 我们选择直接继承 CommandBase，因为从头实现 ICommand 是个很费劲的事情。
public class MyCommand extends CommandBase {

    // 必须覆写。这一部分将作为命令的名字而存在。
    // 换言之，该命令的第一部分将会是 /mycommand。
    @Override
    public String getCommandName() {
        return "mycommand";
    }

    //必须覆写
    @Override
    public String getCommandUsage(ICommandSender sender) {
        // 实际上这里应该使用 I18n 来保证支持国际化。 TODO I18n
        return "/mycommand - my first command";
    }

    // 必须覆写
    @Override
    public void execute(MinecraftServer server, ICommandSender sender, String[] args) throws CommandException {
        // 命令的执行逻辑全部在这里发生。
        sender.sendMessage(new TextComponentString("Hello, world"));
    }

    // 可以不覆写，但默认权限要求是 4（即游戏管理员），所以请按需覆写
    @Override
    public int getRequiredPermissionLevel() {
        return 0; // 0 代表任何人都能用
    }
}
````

### 关于 `ICommandSender`

这个接口代表了“发出命令的那个实体”。这里的“实体”不是指第七章讨论的那个实体，而是指更一般的“某个切实存在的对象”的概念。这么一个接口的存在实际上说明发出命令的方式不止聊天窗口。根据发出命令的方式的不同，它实际上可以是：

  - 全体实体（`Entity`）
    - 这包括了 `EntityPlayer` 乃至 `FakePlayer`。
  - 命令方块（`CommandBlockBaseLogic`），具体来说是这两个：
    - 方块形态的命令方块（`TileEntityCommandBlock$1`）
    - 实体形态的命令方块矿车（`EntityMinecartCommandBlock$1`）
  - 服务器（`MinecraftServer`）
    - 包括 `IntegratedServer` 和 `DedicatedServer`。
  - 每 tick 执行[函数](function.md)的 `FunctionManager.gameLoopFunctionSender`（`field_193073_g`）（`FunctionManager$1`）。
  - 进度系统奖励函数时也会使用一个特殊的 `ICommandSender`（`AdvancementRewards$1`）
  - RCon 远端连接（`RConConsoleSource`）。
  - 告示牌（`TileEntitySign$1` 和 `TileEntity$2`）。

是不是快晕过去了？实际上你不需要在意 `ICommandSender` 的具体类型。举个例子，你调用 `sendMessage` 方法，如果这个 `ICommandSender` 根本没可能看到这个消息的话，这个方法也就会是 no-op，无需对 `ICommandSender` 的类型有任何猜测。

### 关于[目标选择器][ref-target-selector] `@p`、`@r`、`@a`、`@e`、`@s`

你不需要在 `execute` 中处理这些东西，因为这些东西都会被 Minecraft 提前帮你展开好。具体来说，是 Minecraft 首先会根据目标选择器获取一个符合要求的 `List<Entity>`，然后，对于你收到的参数来说，原本是目标选择器的地方会变成一个字符串形式的 UUID，这个 UUID 代表了一个确定的实体。这个 UUID 即是 `Entity.getUniqueID`（`func_110124_au`）的返回值；Forge 对 `Entity` 类的 patch 中也有一个历史遗留的 `getPersistentID` 方法会返回这个 UUID。不过你不需要手动写根据 UUID 查 `Entity` 的方法——`CommandBase.getEntity`（`func_184884_a`）就可以搞定这个问题了。

[ref-target-selector]: https://minecraft-zh.gamepedia.com/%E5%91%BD%E4%BB%A4#.E7.9B.AE.E6.A0.87.E9.80.89.E6.8B.A9.E5.99.A8

### 注册

实际上命令是纯服务器端的东西，这也是很多服务器端插件的功能都是围绕命令展开的原因——它不需要让客户端安装任何东西，客户端只需要能发送聊天消息就能使用这些服务器上有的命令。

```java
// 一般的命令需要这样注册。请注意，这个事件是基于逻辑服务器的。
@Mod.EventHandler
public void onServerStarting(FMLServerStartingEvent event) {
    event.registerServerCommand(new MyCommand());
}
```

但 Forge 提供了一套基于客户端的命令注册，这样一来可以添加一些只在客户端上才有意义的命令（比如渲染相关的命令——服务器上渲染啥？）。

````java
// 客户端专有的命令可以考虑走这个
ClientCommandRegistry.registerCommand(new MyCommand());
````
