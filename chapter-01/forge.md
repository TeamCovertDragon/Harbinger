## Forge

有了 MCP 后，开发 Minecraft 的 Mod 就变得简单了许多，因为我们现在知道那些被“加密”（混淆）的底层代码都是什么东西了。但 Forge 是什么？

### 历史

一个非常常见的说法是“Forge 是个 API”。  
实际上这个说法并不十分严谨。多数时候，API 只包括公开的接口与方法，不包括它们的底层实现。在一个基于 Minecraft 和 Forge 的开发环境中，我们可以随意访问 Minecraft 的底层，如此看来 Forge 似乎不只是纯粹的 API。

实际上也的确如此，今天的 Forge 是由两个项目合并而成的，一个叫 MinecraftForge，另一个叫 Forge Mod Loader（简称 FML）。  
先说 FML。顾名思义，FML 是一个“Mod 加载器”。它介入了 Minecraft 的底层，能让你在 Minecraft 中一口气装一大堆 Mod，在你的电脑撑不住前想装多少装多少。  
MinecraftForge 的组成则有些复杂。它实际上包含了两部分：

  1. 对 Minecraft 底层的修改及因此暴露出的公开方法。当有多个 Mod 因为同时修改 Minecraft 的某一个部分而互相冲突时，Forge 可以介入 Minecraft 的底层来提供一套令这些 Mod 不再冲突的解决方案。
  2. 一些相对来说独立于 Minecraft 之外的系统。这些系统看起来和 Minecraft 之间没有多少耦合，但能极大改善 Mod 之间的兼容性，代表系统有流体、事件总线和矿物词典。这些小系统比其他 Forge 的组成部分更接近真正的 API。

因此，Forge 更像是一套完整的工具包（toolkit），或者说框架（framework）。在它的基础上开发 Mod 可以加快开发速度，同时能自动获得对其他 Mod 的良好兼容。也因此，在 Forge 这个大框架下，Mod 之间已经没有多少真正意义上的兼容性问题了，多数时候我们所说“兼容”问题是指跨 Mod 的交互不符合用户的预期，或者 Mod 在更新版本时引入的二进制不兼容更新。前者需要 Mod 开发者之间的协调，而后者则是软件开发中依赖地狱的一个简化版本。
