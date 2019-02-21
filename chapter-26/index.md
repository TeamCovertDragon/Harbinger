## 配置文件

走到这一步的读者大概已经发现了，很多东西其实都是有自定义的空间的。某个机器使用的常量，某种世界生成的开关，某个客户端特效的精确控制，不胜枚举。所以你大概需要一个配置文件。Forge 正好提供了这样一套工具用来快速搞定这个需求。

### 一个简单示范：MyModConfig.java

最简单的创建配置文件的方法是使用这套基于注解的系统。

```java
@Config(modid = "my_mod_id") // 相当于入口标记一样的东西。`modid` 一定要填你的 mod id。
@Config.LangKey("config.my_mod.general") // 这个用于本地化，稍后会讲
public final class MyModConfig {

    // 最简单的一个选项就是这样声明的。
    // 反映到真正的配置文件中，会是 `B:think=false`。
    public static boolean think = false;

    @Config.Comment("Hey I am foo") // 有了这个就会多一个注释。
    @Config.LangKey("config.my_mod.general.foo") // 本地化用，稍后会讲
    @Config.Name("Foo") // 默认配置选项名是字段名，如果需要别的名字就用这个。
    @Config.RangeInt(min = 1, max = 10000) // 整数值支持限定范围。
    @Config.RequireWorldRestart // meta 标记，代表需要重进存档才会生效
    @Config.RequiresMcRestart // meta 标记，代表需要重启游戏才会生效
    public static int foo = 0;

    @Config.Comment("Hey I am bar")
    @Config.LangKey("config.my_mod.general.bar")
    @Config.Name("Bar")
    @Config.RangeDouble(min = 1.0, max = 10000.0) // 和 RangeInt 一个意思，不过是给 double 的。
    @Config.RequireWorldRestart
    @Config.RequiresMcRestart
    public static double bar = 0;

    // 数组也是支持的。
    public static String[] strArr = new String[] { "test" };
}
```
