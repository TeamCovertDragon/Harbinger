# 可视化配置文件编辑界面

但是直接编辑配置文件往往容易出问题。能不能做一个在游戏中就能修改的界面？实际上 Forge 自己就有这么一套机制。  
首先需要实现 `IModGuiFactory`：

```java
package my_mod;

public class MyModConfigGuiFactory implements IModGuiFactory {
    @Override
    public void initialize(Minecraft mc) {
        // 一般情况下，留空即可。
    }

    @Override
    public boolean hasConfigGui() {
        return true; // 当然要返回 true。
    }

    @Override
    public GuiScreen createConfigGui(GuiScreen parent) {
        return new GuiConfig(parent, Collections.singletonList(ConfigElement.from(MyModConfig.class)), "my_mod_id", false, false, "First line", "Second line");
    }

    @Override
    public Set<RuntimeOptionCategoryElement> runtimeGuiCategories() {
        // 这个方法已经被 deprecated 了，但是迟迟没删除，而且并没有正确实现。
        // 所以我们就不管它了。目前来看，返回 null 不会有问题，但慎重起见，还是
        // 返回一个正常的 Set 比较稳妥。
        return Collections.emptySet();
    }

}
```

然后你需要 `MyModConfigGuiFactory` 的 caonical name，并把它填入 `@Mod` 中：

```java
@Mod(modid = "my_mod", name = "ExampleMod", version = "0.0.0",
    guiFactory = "my_mod.MyModConfigGuiFactory")
```

重启游戏。在主菜单中找到 `Mods` 按钮，然后在打开的 Mod 列表界面左侧找到你的 Mod，此时下面的 `Config` 按钮应该已经可用了。点那个就能看到效果了。

## 本地化

回想一下 `@Config.LangKey` 这个注解：这个注解就是用来提供这个界面的国际化与本地化支持的。每一个选项的显示名称会优先显示通过 `@Config.LangKey` 指定的键的翻译结果，若没有则会使用 `@Config.Name` 指定的名称。  
除此之外，在这个注解之后加上 `.tooltip` 可以指定光标悬浮在选项上时显示的 Tooltip 内容。换言之，如果你有这样一个配置选项：

```java
@Config.LangKey("config.my_mod.general.thing")
public static int thing = 1;
```

那么它对应的 Tooltip 的本地化键是 `config.my_mod.general.thing.tooltip`。
