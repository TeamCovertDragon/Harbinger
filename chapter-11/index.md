## 物理端、逻辑端与网络IO

### 为什么？
从 1.3 开始，Minecraft 的单人游戏的实现变成了模拟一台地址是 127.0.0.1（或者说 localhost）的 Minecraft 服务器，说白了就是你电脑兼职开服，于是乎你不网络 IO 也得网络 IO 了。  
事情是这样，客户端是绝对不知道服务器发生什么的，反之亦然。比如说，TileEntity 在服务器上的业务逻辑，客户端对此是一无所知的；同样，玩家按下了键盘上的一个键，服务器也是对此毫不知情的。唯一能让两者互相知道发生了什么的方法，就是网络通信。

### 业务端判定
原版很多地方的业务逻辑，服务器和客户端是耦合在一起的……（也许这个词笔者用错了，但暂时先这样）。举个例子，方块右击的时候给玩家发一句话，在实际游戏中你会发现显示了两次——这时再加上 `FMLCommonHandler` 的 `getEffectiveSide` 的输出就能见真相了：一次来自服务器，一次来自客户端。  
怎么办呢。其实刚才提到了一个解决办法就是 `FMLCommonHandler.getEffectiveSide`。这个方法其实是不推荐的——它实际上是个 hack，此前是通过检查线程名判定是服务器端还是客户端；现在这个方法的实现是 Forge 在 patch Minecraft 底层后，两个线程及派生出的线程隶属不同的 `ThreadGroup`（分别对应客户端和服务器），以此为基础检查逻辑侧。新的实现使 这个方法显得不那么 hack 了，但也只是让它显得不 hack 了而已。  
最稳妥也是最高效的办法，从 1.6.4 到 1.12.2，就没有变过——检查 `World` 实例的 `isRemote`：

````java
                        // Last Remote
world.isRemote == true  //  客户端，意即“这个世界是被遥控的”
world.isRemote == false // 服务器端，意即“这个世界不是被遥控的”
````

### 关于物理端与 `@SideOnly`
就这个问题，LambdaInnovation 的 WeAthFold 曾经有一篇文章做过详细阐述，但他博客搬过好几次，博文随之没了，只[在 MCBBS 上留有一份镜像](http://www.mcbbs.net/thread-579069-1-1.html)。
[Forge 自己的文档](http://mcforge.readthedocs.io/en/latest/concepts/sides/)讲得也很清楚，可惜不是中文的。  
在这里我们只做一个简单描述，有问题的话大可以参考 ForgeDocs。

`@SideOnly` 是一个给 MCP 用的注解，用于在合并 Minecraft 的客户端和服务器的代码为一个 jar 后能正确区分来源。  
它的真正含义这样的：“此方法仅在指定物理端出现，若当前生产环境与给定业务端不匹配，则移除此方法”。业务端由此注解下的 `value` 给出，为枚举型 `Side`，有效值为 `SERVER` 和 `CLIENT`。  
换言之，如果有这样一个方法：

````java
@SideOnly(Side.CLIENT)
public void foo() {
    // Do whatsoever
}
````

那么这个方法在服务器上，就会被 FML 彻底抹去。这里的“彻底抹去”是指在物理服务器上调用时会抛出 `NoSuchMethodException`。抹去的原理可参考第三十章的内容。另外这个注解还可以加在 Class、Field 上，效果类似。  
有一点要注意——这个所谓的“服务器”是指真正的物理服务器，并不是逻辑上的服务器。换言之，这里的“服务器”是指你执行 `java -jar -Xmx4G -Xms4G -XX:+UseG1GC -Dfile.encoding=UTF8 minecraft_server.jar nogui` 时的那个服务器（`minecraft_server.jar`）。  
这里再加一个例子来正确阐述这个注解的效果：

```java
@SideOnly(Side.SERVER)
public void bar() {}
```

对于这个方法来说，若你使用 `gradle runClient` 启动客户端并试图调用此方法，就会抛出 `NoSuchMethodException`，因为你运行的是客户端，而注解告诉 FML 的是这个方法应该只在服务器上有。反之，使用 `gradle runServer` 并调用此方法并不会有任何问题，因为此时你的生产环境是服务器，不是游戏客户端。

实际上，这个注解的存在是因为你的 Mod 可以不经任何修改，直接同时适用于服务器和客户端。  
在你的开发环境里，你会注意到，那个forgeSrc Jar 中，似乎还有服务器的类。玄机就在这里——ForgeGradle 在部署开发环境的时候，确实是把两个 Jar 合并在了一起（`mergeJar` task），但如果有必要再次分开那怎么办？
这个注解就派上用场了。对这个注解的使用最终可以让一个类／方法／字段有以下三种可能性：
 * 没有 `SideOnly` 注解。代表这个类／方法／字段在服务器和客户端同时存在。
 * `@SideOnly(Side.CLIENT)`。代表这个类／方法／字段只应该在客户端软件中存在。
 * `@SideOnly(Side.SERVER)`。代表这个类／方法／字段只应该在服务器端软件中存在。
现在我们清楚了合并后的 Jar 中所有的类／方法／字段的来源。那么我们就可以简单地让服务器和客户端共用一个 Jar 了。基于这个 Jar 开发的 Mod 也就变成了服务器客户端通用的 Mod。
这也是为什么这个注解的 Javadoc 里写着“不建议使用”。这个类本身不是为了 Mod 开发而准备的——虽然你用了也确实有效果。

对比一下 `World.isRemote` 不难发现，`World.isRemote` 是基于**逻辑端**的，而 `@SideOnly` 是基于**物理端**的。  
说白了，这个的差别还是因为单机模式从 Minecraft 1.3 开始变成了本地模拟服务器...  
这样做的一个好处在于，Minecraft 的服务器和客户端两个不同的 Jar 可以有更多一样的代码，方便开发。实际上，在 Minecraft 1.3 之前的版本上，服务器用的 Mod 的文件和客户端不一样。但随着 1.3 开始单机游戏以 `IntegratedServer` 的形式实现，Mojang 也合并了两套不同的代码。自此，ForgeGradle 等工具便可以将服务器和客户端用的两个 jar 合并为一个，从而允许开发者在开发 Mod 时把客户端专有的内容（渲染等）和服务器端专有的内容（某些命令等）放在同一个 jar 中，从而方便开发。
但这样做就有问题了：你不可能在服务器调用渲染，你也不可能要求客户端玩家启动游戏前同意 EULA（文字游戏：在你购买 Minecraft 时你就已经同意了，不信？请回想一下你是不是跳过了什么？）。`@SideOnly` 因此应运而生。

### 物理端代理（`@SidedProxy`）与依赖注入

同样的，在你的 Mod 主类中：

````java
@SidedProxy(serverSide = "ServerProxy", clientSide = "ClientProxy")
public static Proxy myProxy;
````
和 `@SideOnly` 一样，这个也是基于**物理端**而非逻辑端的。这样可以让你的 Mod 的某些方法在物理服务器上和在物理客户端上有不同的行为。

````java
public class CommonProxy {
    public void foo() {
        System.out.println("CommonProxy says foo");
    }
}

public class ServerProxy extends CommonProxy {
    public void foo() {
        System.out.println("ServerProxy says foo");
    }
}

public class ClientProxy extends CommonProxy {
    public void foo() {
        System.out.println("ClientProxy says foo");
    }
}
````
