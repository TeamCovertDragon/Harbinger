### 网络IO

### 为什么？
不为什么。  
从 1.3 开始，Minecraft 的单人游戏的实现变成了模拟一台地址是 127.0.0.1（或者说 localhost）的 Minecraft 服务器，说白了就是你电脑兼职开服，于是乎你不网络 IO 也得网络 IO 了。  
事情是这样，客户端是绝对不知道服务器发生什么的，反之亦然。比如说，TileEntity 在服务器上的业务逻辑，客户端对此是一无所知的；同样，玩家按下了键盘上的一个键，服务器也是对此毫不知情的。唯一能让两者互相知道发生了什么的方法，就是网络通信。

### 业务端判定
原版很多地方的业务逻辑，服务器和客户端是耦合在一起的...（也许这个词笔者用错了，但暂时先这样），举个例子，方块右击的时候给玩家发一句话，在实际游戏中你会发现显示了两次——这时再加上 `FMLCommonHandler` 的 `getEffectiveSide` 的输出就能见真相了：一次来自服务器，一次来自客户端。  
怎么办呢。其实刚才提到了一个解决办法就是 `FMLCommonHandler.getEffectiveSide`。这个方法其实是不推荐的——它实际上是个 hack，此前是通过检查线程名判定是服务器端还是客户端；现在 Forge 预留了两个 ThreadGroup 的实例，分别对应客户端和服务器，这才使这个方法显得不那么 hack 了，但也只是让它显得不 hack 了而已。  
最稳妥的办法，从1.6.4到1.10.2，就没有变过——检查 `World` 实例的 `isRemote`：

````java
                        // Last Remote
world.isRemote == true  //   客户端，意即“这个世界是被遥控的”
world.isRemote == false // 服务器端，意即“这个世界不是被遥控的”
````

### 关于Server和Client那些不得不说的事情：SideOnly
就这个问题，LambdaInnovation 的 WeAthFold 曾经有一篇文章做过详细阐述，但他博客搬过好几次，博文随之没了（非也。[他居然在 MCBBS 上发过……](http://www.mcbbs.net/thread-579069-1-1.html)）
MinecraftForge [自己的文档](http://mcforge.readthedocs.io/en/latest/concepts/sides/)讲得也很好，可惜不是中文的。  
所以笔者就再讲一遍好了- -看得懂英文的大可不管笔者码的字，直接看上文给的 ForgeDocs。  

`@SideOnly` 是一个给 MCP 用的注解，用于在合并 Minecraft 的客户端和服务器的代码为一个 jar 后能正确区分来源。  
它的真正含义，在笔者看来是这样的：“此方法仅在指定业务端出现，若当前生产环境与给定业务端不匹配，则移除此方法”。业务端由此元注解下的 `value` 给出，为枚举型 `Side`，有效值为 `SERVER` 和 `CLIENT`。  
什么意思呢？  
换言之，如果有这样一个方法：

````java
@SideOnly(Side.CLIENT)
public void foo() {}
````

那么这个方法在服务器上，就会被FML彻底抹去。这里的“彻底抹去”是指调用时会抛出 `MethodNotFoundException`。抹去的原理可参考第二十章的内容。另外这个注解还可以加在 Class、Field 上，效果类似。  
有一点要注意——这个所谓的“服务器”是指真正的物理服务器，并不是逻辑上的服务器。这里再加一个例子来正确阐述这个注解的效果：

````java
@SideOnly(Side.SERVER)
public void bar() {}
````

对于这个方法来说，若你使用 `gradle runClient` 启动客户端并试图调用此方法，就会抛出 `MethodNotFoundException`，因为你运行的是客户端，而注解告诉 FML 的是这个方法应该只在服务器上有。反之，使用 `gradle runServer` 并调用此方法并不会有任何问题，因为此时你的生产环境是服务器，不是游戏客户端。

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
这样做的一个好处在于，Minecraft 的服务器和客户端两个不同的 Jar 可以有更多一样的代码，方便开发。如果读者记性还好的话，应该记得在1.3之前，服务器用的Mod和客户端不一样吧。之所以突然可以用一样的Mod，那还是要拜ForgeGradle的`mergeJar`所赐——它将两个不同的Jar合并为一个，从而允许开发者可以将Mod代码放进同一个Jar中。但这样做就有问题了：你不可能在服务器调用渲染，你也不可能要求客户端玩家启动游戏前同意EULA（文字游戏：在你购买Minecraft时你就已经同意了，不信？请回想一下你是不是跳过了什么？）。这是笔者对于 `@SideOnly` 的一点理解。

同样的，在你的Mod主类中：

````java
@SidedProxy(serverSide = "", clientSide = "")
public static Proxy myProxy;
````
和 `@SideOnly` 一样，这个也是基于**物理端**而非逻辑端的。这样可以让你的 Mod 在服务器和客户端上有不同的加载流程。
仍然是多态和动态绑定：

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
