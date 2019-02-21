## 可视化配置文件编辑界面

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
@Mod(modid = "example_mod", name = "ExampleMod", version = "0.0.0",
    guiFactory = "my_mod.MyModConfigGuiFactory")
```

重启游戏。在主菜单中找到 `Mods` 按钮，然后在打开的 Mod 列表界面左侧找到你的 Mod，此时下面的 `Config` 按钮应该已经可用了。点那个就能看到效果了。
