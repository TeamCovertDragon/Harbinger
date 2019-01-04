### `CommandTreeBase`

[MinecraftForge/MinecraftForge#3193][Forge#3193] 引入了一个比较实用的基类：`CommandTreeBase`，可用于非常快速地实现“子命令”的概念。

````java
public class MyCommandTree extends CommandTreeBase {
    public MyCommandTree() {
        this.addSubcommand(new CommandBase() {
            // ... 省略对 CommandBase 的实现
        });
    }

    @Override
    public String getCommandName() {
        return "";
    }

    @Override
    public String getCommandUsage(ICommandSender sender) {
        return "";
    }
}
````

[Forge#3193]: https://github.com/MinecraftForge/MinecraftForge/pull/3193
