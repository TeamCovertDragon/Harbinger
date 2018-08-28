### 开发环境部署

#### 部署
其实部署一个基于 MinecraftForge 的 Mod 的开发环境还是很简单的。就现在来说，是这样。

 1. 首先下载 Mod Development Kit（缩写 MDK，1.7.10 及以前还叫 src）。打开[Forge的下载页面](http://files.minecraftforge.net/)，其中那个写着 Mdk 的链接便是。
 2. 下载得到zip，解压。务必要能看见诸如`build.gradle`、`gradlew`、`gradlew.bat`等文件。
 3. 根据你的具体情况不同，在命令行中，切换到 Mdk 的目录，然后：
   * 如果你有 Gradle：`gradle setupDecompWorkspace`
   * 如果你没有 Gradle：
     - Windows：`gradlew.bat setupDecompWorkspace`
     - 某种 Linux 发行版，或 macOS：`gradlew setupDecompWorkspace`
     - 其他系统：很抱歉，Minecraft 所依赖的 LWJGL 只有针对 Windows、macOS 和 Linux 三类系统的 native lib。
   * 如果你是 maven 用户：不好意思，笔者不是很确定你能不能只用 maven 来开发基于 MinecraftForge 的 Mod。
 4. 根据你使用的IDE不同：
   * Eclipse：在`setupDecompWorkspace`后接上`eclipse`
   * Intellij IDEA：在`setupDecompWorkspace`后接上`idea genIntellijRuns`
   * 其他IDE：不好意思你可能需要找对应的 Gradle 插件来解决问题。请查阅相关资料获得更多帮助。
 5. 回车运行。若看到 `BUILD SUCCESS` 字样则表示大功告成，否则重复步骤 3. 和 4. 直到成功为止。


#### 入口类

和一般的教程不一样，这里的写法有些特殊：

````java
//Mod主类的标记。其中只有modid是必填项。
//modid不允许使用大写字母，即snake_case。
//1.10中modid使用大写字母会使FML产生一个警告，1.11开始则抛出IllegalArgumentException阻止游戏启动。
//name没有限制。若不填则和modid一致。
@Mod(modid = "example_mod", name = "ExampleMod")
public enum ExampleMod {
    INSTANCE;

    //Mod主类实例的工厂。（工厂个头啊最后出来的东西用==比较都返回true）
    //别的Mod开发教程肯定使用的是@Mod.Instance
    //这里之所以这么写，是因为主类是enum
    //换言之，父类是java.lang.Enum
    //因此，FML直接Class.newInstance的话会炸掉，java.lang.Enum的构造器不是零参数
    @Mod.InstanceFactory
    public static ExampleMod getInstance() {
        return INSTANCE;
    }

    //一个入口方法。
    //Mod的加载大致分为6个阶段，每个阶段均代表一个事件，
    //而@Mod.EventHandler元注解则标记该方法将订阅此事件。
    //Mod加载时的六个事件，以时间顺序排列：
    //    FMLConstructionEvent
    //    FMLPreInitializationEvent
    //    FMLInitializationEvent
    //    FMLInterModComms.IMCEvent
    //    FMLPostInitializationEvent
    //    FMLLoadCompleteEvent
    @Mod.EventHandler
    public void preLoad(FMLPreInitializationEvent event) {
        System.out.println("Hello, MinecraftForge");
    }
````
