# 起点

## 开发环境部署
其实部署一个基于 Forge 的 Mod 的开发环境还是很简单的。比以前不知道简单到哪里去了。

  1. 首先下载 Forge 的 Mod Development Kit（MDK）。打开 [Forge的下载页面][ref-forge-home]，页面顶部有一个“Download Latest”，下面有一行小字写着 `1.12.2-14.23.5.2836`，再往下看一点，那个写着 Mdk 的链接便是。
  2. 解压下载到的 zip。务必要能看见诸如 `build.gradle`、`gradlew`、`gradlew.bat`、`.gitignore` 等文件。
  3. 有鉴于本指南使用的 MCP 版本是 `stable_39`，请用文本编辑器打开 `build.gradle`，找到：
  ```groovy
  minecraft {
      //...
      mapping = "..."
      //...
  }
  ```
     [将 `mapping = "..."` 改成 `mapping = "stable_39"`](../chapter-01/forgegradle.html)。
  4. 根据你的具体情况不同，在命令行中，切换到 MDK 的目录，然后：

    - Windows（CMD、PowerShell 等）：`gradlew.bat setupDecompWorkspace`。注意权限。
    - 某种 Linux 发行版，或 macOS：`./gradlew setupDecompWorkspace`。注意权限。
    - 其他系统：很抱歉，Minecraft 所依赖的 LWJGL 只有针对 Windows、macOS 和 Linux 三类系统的 native lib。
  5. 根据你使用的 IDE 不同，执行下列对应命令后，在对应的 IDE 中以 Gradle 项目的形式导入项目：

    * Eclipse：在 `setupDecompWorkspace` 后接上 `eclipse`
    * IntelliJ IDEA：恭喜你，在这里你不需要加任何东西。
    * 其他 IDE：不好意思你需要找对应的 Gradle 插件来解决问题，请查阅相关资料获得更多帮助。在这里的话就什么也不用加好了。
  6. 回车运行。若看到 `BUILD SUCCESSFUL` 字样则表示大功告成，否则重复步骤 3. 和 4.，直到看到那个 `BUILD SUCCESSFUL` 为止。
  7. 在你使用的 IDE 中以 Gradle 项目的形式导入工程目录。

[ref-forge-home]: https://files.minecraftforge.net/

### 常见问题

是的，因为各种乱七八糟的原因，部署环境的过程总是会有各种莫名其妙的问题。一般情况下， `--debug`、`--stacktrace` 直接莽拿到的信息足够 debug 用了。但有一点请注意：部署环境的过程中，因为涉及到几轮 mapping 和下载 Minecraft 的 assets，所以在没有预先部署好的环境留下的缓存的情况行，这个过程不会特别快。要有耐心。

然而，由于某些特殊原因，即使你有足够的耐心也不一定能部署成功。遇到这种情况时，你可以尝试通过为 Gradle 配置代理。[相关资料][ref-zzzz-tutorial]很容易找到，这里不再赘述。  

此外，还要明确一点：不是所有的错误都和网络有关系。请不要盲目尝试各种所谓的解决方案——虽然，直接删了 `.gradle` 缓存目录可以解决 90% 的非网络因素引发的问题。

[ref-zzzz-tutorial]: https://fmltutor.ustc-zzzz.net/1.1-%E9%85%8D%E7%BD%AE%E4%BD%A0%E7%9A%84%E5%B7%A5%E4%BD%9C%E7%8E%AF%E5%A2%83.html#%E9%85%8D%E7%BD%AE%E5%B7%A5%E4%BD%9C%E7%8E%AF%E5%A2%83

### 在 Eclipse 中运行 Minecraft

不需要任何额外操作，成功导入项目后你应该能找到对应的 Configuration 了。只需要“Run As…”或者“Debug As…”即可。

### 在 IDEA 中运行 Minecraft

因为某些原因，你需要手动配置你的项目才能在 IDEA 中运行 Minecraft 并进行调试。幸运的是 ForgeGradle 提供了一个简化这个操作的 task，它可以帮你生成所有必须的配置：

```bash
$ ./gradlew genIntellijRuns
```

运行完之后你便能在 "Run…" 中找到名为“Minecraft Client”和“Minecraft Server”的配置了。  
注意：这个 task 必须在项目已经导入进 IDEA 之后运行！<black>你以为刚才比 Eclipse 用户少打一行就算完了吗（笑）</black>

## 入口类

和一般的教程不一样，这里的写法有些特殊：

```java
// Mod主类的标记。其中只有modid是必填项。
//
// modid 不允许使用大写字母，最大长度 64，不能为空。
// 推荐在 modid 中只使用小写字母、数字、连字符和下划线，换言之即只使用满足正则表达式
// [a-z0-9\-_]{1,64} 的 modid。
// 发现不符合要求的 modid 时，会抛出 IllegalArgumentException 阻止游戏启动。
//
// name 没有限制。若不填则直接使用 modid 充当 name，但仍然会产生警告。
// 一般来说，name 都满足正则表达式 [\w ]+。但也有少许例外。
//
// version 没有限制，若不填则使用 1.0，并产生警告。
// 对于 version 来说，***强烈***推荐只用数字和点，比如只有 MAJOR.MINOR.PATCH 的 SemVer。
//
// useMetadata = true 让 Forge 以 mcmod.info 里的信息为准。它的格式在“Mod 的元数据”一节
// 会有详细说明。
@Mod(modid = "my_mod", name = "My First Mod", version = "0.0.0", useMetadata = true)
public enum MyMod {
    INSTANCE;

    // Mod 主类实例的“工厂”。
    // 别的 Mod 开发教程肯定使用的是@Mod.Instance
    // 这里之所以这么写，是因为主类是 enum，换言之父类是 java.lang.Enum
    // 因此，FML 直接 Class.newInstance() 的话一定会报错，java.lang.Enum 没有零参构造器
    @Mod.InstanceFactory
    public static MyMod getInstance() {
        return INSTANCE;
    }

    // 一个入口方法。
    // Mod的加载大致分为 6 个阶段，每个阶段均有一个事件代表，
    // 而 @Mod.EventHandler 注解则标记该方法将订阅此事件。
    // Mod 加载时的六个阶段对应的事件，以时间顺序排列：
    //    FMLConstructionEvent
    //    FMLPreInitializationEvent
    //    FMLInitializationEvent
    //    FMLInterModComms.IMCEvent
    //    FMLPostInitializationEvent
    //    FMLLoadCompleteEvent
    // 其中，PreInit.、Init.、IMCEvent 和 PostInit. 是最常用的四个事件，
    // 剩下两个事件只有在相当少见的情况下才会用到，大可暂时无视。
    @Mod.EventHandler
    public void preLoad(FMLPreInitializationEvent event) {
        System.out.println("Hello, Forge");
    }
}
```
