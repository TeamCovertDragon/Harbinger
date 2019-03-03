## 序：《异教与隐士》 <!-- "Proselyte and Hermit" -->

> If I have to do this, I will do this right. - Yunru

------

[MONITORING BEGIN]  
[INJECTING TO SECURED CONNECTION, PLEASE STAND BY...]  
[BATTLE CONTROL OFFLINE]  
[TAPPING PROCESS RETURNS ZERO, INITIALIZING LISTENING THREAD ON PORT 443...]  
[ESTABLISHING BATTLEFIELD CONTROL, STAND BY...]  
[BATTLEFIELD CONTROL ONLINE]  

……  
……  

_握手开始。这将决定我们之间的对话能否成立。_

_按访问级从严到宽列出 Java 编程语言中所有可能的访问级修饰符。_

`private` <（没有修饰符）< `protected` < `public`。

_简述 Java 泛型的实现原理？_

类型擦除到指定边界，默认为 `Object`。

_简述 `throw` 和 `throws` 的区别。_

`throw` 用于抛出异常；`throws` 用于在方法声明中注明允许抛出的 checked 异常。

_`synchronized` 关键字作用？_

标记有 `synchronized` 的方法在执行时会对 `this` 加锁，直到方法执行完毕并释锁。亦可使用 `synchronized` 来声明临界区，以指定需要拿到锁才能执行的代码块。

_JLS 8 中哪两个关键字没有任何用途？_

`const` 和 `goto`。这两个关键字在 C++ 中均有使用：前者标记常量、Accessor、常量方法等，后者用于跳出循环至指定标签位置。

_`void foo(List<String> bar)` 和 `void foo(List<Ingeter> bar)` 能否在一个类中共存？_

如前所述，Java 的泛型由类型擦除实现，所以不能。它们的方法签名均为 `foo(Ljava/util/List;)V`。

_`boolean contains(E e) { for (E e1 : this.data) { if (e1.equals(e)) return true; } return false; }` 的时间复杂度？_

$$\Theta(n)$$。

_通常所说的 Stack Overflow（栈溢）中的 Stack 是指？_

程序的运行栈，通常是调用一个方法时，所有局部变量的存放地点。因此栈溢是指在递归过深等情况下栈上存满数据导致的错误。

_试用一句话解释递归，不要求精确。_

调用自身的方法称之为递归方法，而调用自身这个动作即是递归。

_试解释享元模式（Flyweight Pattern）_

在存在大量重复对象的场景下，通过在合适的时机复用相同对象以节约内存等资源的设计模式。

_Tu fui ego eris. - A latin proverb, means "You will be what I was."_

光阴似箭。时不我待。  

_Excellent, you got what I was trying to say and thus passed the examination._  
_Welcome to the world of Minecraft Modding, proselyte._

谢谢。

_Minecraft Modding 发展到今天，也出来了一堆框架，但没有一个框架算得上是 Comprehensive 的。当然，如果有，你就可以挑战[那幅漫画](https://xkcd.com/927/)去了，社区也就不会再天天拿这漫画说事了。  
读不懂这幅漫画的请直接看[解释](http://www.explainxkcd.com/wiki/index.php/927)。这里不讨论。_

不不不不，我知道这幅漫画的意思。

_那非常好。我原以为这年头没人看这种东西了……_

没有一个框架算得上是 Comprehensive 的？理由？

_之所以没有 Compreshensive 的 Framework，大概是因为 Minecraft 本身的种种制约吧。且看现在最 Robust 的两位，Bukkit 和 Sponge——前者曾一度因 DMCA takedown 被打得不成样子……_

Spigot 还在更新啊？

_我不是指 API 本身，而是 API 的实现——CraftBukkit、Spigot、Cauldron、et al.——被打得不成样子。多亏 Spigot 力挽狂澜而得以有现在，但现在早已不再是 Bukkit 的黄金时代。一定要说的话，我会用“白银时代”来描述现在的 Bukkit。而 Sponge 呢，则还在苦苦 debug、补 Documentation、以及 **API Breaking Change**。_

但是…… 隔壁还有一个 Forge 你怎么没提？

_啊不好意思忘记了。但有一个问题，Forge 是不是 API？  
不论是 Forge，还是 FML，都没承认过它自己只是 API。任何类似于“Forge 是 Minecraft 的 API”的说法，都是从方便理解这个角度出发的。_  

所以 Forge 是个什么呢？它是不是 Minecraft 的一个非正式 API？

_就“Forge 是不是 API”这个问题，我是这样认为的：Forge 比 FML 更符合 API 的定义。Forge 由一些抽象层以及实用工具类组成；FML 则纯粹是负责 Mod 加载以及共存的。如果单讨论 Forge，同时抛开 FML 和 ForgeGradle 不谈，那么 Forge 它本身在某种程度上确实是一个 API。但是，通常语境中的 Forge 同时包含了 FML 和 Forge，而且 FML 在 1.8 的时候也合并入了 Forge，其包名也从 `cpw.mods.fml` 变为 `net.minecraftforge.fml`。是的，FML 和 Forge 最开始不是同一批人开发的，但后来两者合作逐渐加深，直到改包名。  
总而言之，我们至少可以将 Forge 称之为“框架”。这个框架下有各种标准化的库，Mod 只要使用了这些标准化的库就能保证与其他 Mod 基本兼容。_  

那 FML 是……？

_Forge 在 GitHub 上的仓库的简介写到："Modifications to the Minecraft base files to assist in compatibility between mods." 这也就意味着，它是一个所谓的兼容层，即 Compatibility Layer。作为兼容层，Forge 的使命是：保证用户可以让各种各样的 Mod 安装在同一个游戏实例中，并能正常玩耍。通常，社区管这个游戏实例叫“整合包”，或者用欧美的说法，modpack。  
也许你对历史没什么了解。FML 的全称是 Forge Mod Loader。想当初 Mod 安装的流程基本都是“解压你下载到的 zip，然后把文件拖入 minecraft.jar 中”。这样一来，两个 Mod 就会因为同时修改一个文件而无法安装到一起。直到有了像是 Risugami 和 SDK 这样的 Mod 加载器，这时 Mod 开发者们只需要在这样的加载器的基础上开发 Mod 就可以了。因此，FML 是这个 Compatibility Layer 的基础——一个 Mod 加载器。
正因有 Fogre 和 FML，以及它的前辈——Risugami's ModLoader、SDK Loader、et alias，的存在，Modded Minecraft 社区才得以有了今天的样子。Behold, the Feed The Beast! Behold, the Technic! Behold, the ATLauncher!  
扯远了。但，不是 API 就意味着它的稳定性不如一个抽象化的 API 好。没有了稳定性的保证，Forge 就不得不面临一个问题：没有稳定的 Documentation。  
哎。可怜那些硬啃 Minecraft 反编译结果的 Modder。是先人的努力造就了今天的 MCP Mapping，也是先人们的努力造就了今天的社区。你看现在的 Forge，有很多东西都是社区互相妥协的结果。矿物词典、流体系统、如此种种。It is always about compatibility, and to a certain extent, "correctness". It was. It is. It will be._

原来如此。换言之，我们今天所拥有的一切，都是踩在先辈们的肩膀上才得到的吗。

_正是如此。  
也正因此，摆在你眼前的这本册子，的存在意义——一套全面的，基于 Forge 的，中文 Minecraft Mod 开发指南——才得以成立。_

That's it.

_Yes, that's it. 拒绝拖泥带水，我们开始吧。_

……  
……  

[EXCEPTION CAUGHT ! java.lang.IOException: java.lang.EOFException: Unexpected EOF]  
[TERMINATING LISTENING THREAD...]  
[RESTORING TO LAST NORMAL STATUS...]  
[EXPUNGING ALL TRACE...]  
[TERMINATION OF MONITORING]  
[PROCESS EXIT WITH SIGNAL OF -1]  
