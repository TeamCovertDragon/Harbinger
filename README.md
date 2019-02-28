# Harbinger

Harbinger 是一套全面的，基于 Forge 的，中文 Minecraft Mod 开发指南。身为读者的你，需要记住以下事项：

* 本教程基于 Minecraft 1.12.2 Release 和足够新的 Forge。**如无特殊说明，本教程基于 Minecraft 1.12.2 Release 和 Forge 14.23.5.2772，MCP Mapping 版本使用 `stable_39`。本教程原则上不涉及任何旧版内容。**
* 本教程使用的目录分隔符（directory separator）统一为 `/`。
* 这里不是 Java 教程。你必须掌握至少一门基于 JVM 的编程语言。对零基础新人来说，Java 就可以，先去花点时间把 Java 学了。Scala 用户用 Scala 当然也是十分欢迎的，只是笔者不会 Scala。

  > 什么叫“降低门槛”？要我说，我已经很久没见到比国内MC圈的开发门槛还要低的圈子了，软件版和编程版的门槛已经低到不能再低了。至少对于我而言，我已经在无数个场合说过无数次了，学开发插件/Mod的时候，先花上几个月把Java学了，然后再学会很轻松。实际上呢？我接触过的连Java基本语法都没了解清楚就去学写插件/Mod的人，实在是太多太多了。Java实际上，甚至可以说是最简单的现代编程语言之一。至于多了解了解其他的编程语言，多学习学习一些计算机科学的知识，什么数据结构啊，设计模式啊，组成原理啊，这些本来应该是程序猿提升自我理应做的事情，对于他们来说，我真是想都不敢想。  
  >
  > 作者：Yanbing Zhao  
  > 链接：https://www.zhihu.com/question/63200488/answer/206584431  
  > 来源：知乎  
  > 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处  

* 要对面向对象的软件设计模式有一定认识。
  * 这一条比较重要，如果有对面向对象的认识，甚至对 Java 这门编程语言的要求都可以降下来。
* 你没有退路。要么一开始就不要做，要么一路走到黑，中途退出只会让你在新版本懵逼。
  * 对了，写多了 Minecraft 的 Mod 可能会有强烈的副作用，如果屏幕前的你打算以此入门编程，请三思。
  * 不要有任何怀疑，Forge 的设计是“只保持某个 Minecraft 主要版本内的二进制兼容”，跨版本兼容是不可能的。Minecraft 更新一次实际上就是 Forge 进行 Breaking change 的窗口，而且这个窗口开放的时间往往也很短。要知道 FAIL 已经停止开发了。<!-- FAIL 仓库的地址原本位于 https://github.com/MinecraftForge/FAIL -->
    * FAIL 是 Forge Abstraction Interpertation Layer（Forge 抽象与解释层）的首字母缩略词。
    * FAIL 这个单词本身的含义是“失败”。某种意义上的自嘲。
    * [事实上也的确停止开发了][ref-fail-status]。如果你真的很在意跨 Minecraft 版本的兼容性，[Sponge][ref-sponge-docs]。
* 最后，代码要保持整洁、高效。

另外，几个建议：
* 请务必时常关注 GitHub 上 MinecraftForge/MinecraftForge 的 commit 以及 Pull Requests。这是你获得第一手信息的最简单还最可靠的方式（更何况，如果你在国内，别的方式对你来说，不仅是奢望，效率还不怎么高）。
* 请务必时常关注 [Forge 自己的文档][ref-forge-doc]。有能力的读者，请务必考虑协助他们补全文档（位于[这里][ref-forge-doc-src]）。
  * 更新并不及时的[非正式中文翻译][ref-forge-doc-translation]，实在看不懂英语的话可以用来救急。
* 欧美社区的 Modder 很多都常驻在一些 Discord 服务器中，比如 Minecraft Mod Development，在这些 Discord 服务器里问他们问题也许能得到详尽的解答。
  * 英语要求不需要强调第二次。注意不规则拼写，拿不定的时候放出声读一读。
  * 海量 meme 警告，可以考虑潜水收图。
  * 遵守一切有关规定，发错频道后果自负。
  * 问问题时想清楚要问什么，不要拐弯抹角，[直接把最开始的问题拿出来][ref-xy]，而不是问“你觉得可行的方案如何去实现”。
  * [有问题直接问][ref-just-ask]，不要问“在吗，我有一个问题想请教一下”。
    * 但非常神奇的是，多数 Modder 对私信和 Ping 非常敏感，所以请不要这么做。
  * 善用各类粘贴箱（paste bin）。国内的话 [Ubuntu PasteBin][ref-ubuntu-paste-bin] 就可以。
  * 因为国内国外都有熊孩子和 troll，所以很多人可能并没有耐心。直接“Go to learn Java”的回复并不少见。

[ref-fail-status]: https://gist.github.com/xt9/55a04226728cebe729fe37cc7aa9d10f#fail-forge-abstraction-layer
[ref-sponge-docs]: https://docs.spongepowered.org/stable/zh-CN/
[ref-forge-doc]: http://mcforge.readthedocs.io/en/latest/
[ref-forge-doc-src]: https://github.com/MinecraftForge/Documentation
[ref-forge-doc-translation]: http://mcforge-cn.readthedocs.io/zh/latest/
[ref-ubuntu-paste-bin]: https://paste.ubuntu.com/
[ref-xy]: https://meta.stackexchange.com/a/66378
[ref-just-ask]: http://sol.gfxile.net/dontask.html

<!--
关于 XY 问题还可以参考：

  - http://xyproblem.info/
  - http://coolshell.cn/articles/10804.html
-->
