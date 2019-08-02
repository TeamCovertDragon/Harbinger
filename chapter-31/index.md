# 附录 I：相关开发资源

虽然 Harbinger 力图覆盖 Mod 开发中会遇到的种种问题及它们的解答，但 Harbinger 本身并不能取代其他开发资源的地位——历史悠久的 Mod 的源码、互联网上某个角落里的贴子、甚至是书店里静静躺着的一本 Java 相关书籍，诸如此类，不胜枚举。
这一附录的意义便是列出那些有重要参考价值的资源。在遇到问题止步不前时，你也许可以从这里找到灵感。

这样一个列表也决定了它不可能绝对客观。尽管如此，我们力求通过拓宽列表的广度来抵消主观评价带来的影响。

## 那些历史悠久的 Mod 的源码

  - CraftTweaker：https://github.com/CraftTweaker/CraftTweaker
    - 优势：老牌整合制作工具 MineTweaker 的正统续作，包括为数不少的看似简单实则非常困难的功能的实现
    - 优势：MIT 授权
    - 劣势：黑魔法众多，多数时候你应该让用户直接用 CraftTweaker 而不是自己写一个
  - 林业（Forestry）：https://github.com/ForestryMC/ForestryMC
    - 优势：LGPL v3 授权
    - 优势：最早在物品上塞大量数据的 Mod 之一（养蜂系统）
    - 优势：自动收割作物的农场
    - 劣势：有数个抽象层，需要花时间阅读
  - 沉浸工程（Immersive Engineering）：https://github.com/BluSunrize/ImmersiveEngineering
    - 优势：多方块结构的控制及渲染
    - 优势：一套相对简单的电网实现
    - 劣势：有时候方法体会非常长，导致可读性略微下降
    - 劣势：自定义授权，可能对有许可方面要求的用户不甚友好
  - JEI（Just Enough Items)：https://github.com/mezz/JustEnoughItems
    - 优势：MIT 授权
    - 优势：简单干净的物品管理器界面 Overlay 实现
    - 优势：API 设计清楚，文档齐全，正确搭配 SemVer 迭代并废弃旧版 API，可实现可选兼容
  - 匠魂（Tinkers' Construct）https://github.com/SlimeKnights/TinkersConstruct
    - 优势：MIT 授权
    - 优势：一套高效的拼接模型的实现，用于实现匠魂工具及武器的“组合”模型
    - 优势：多方块结构（冶炼炉等）的实现
    - 劣势：时常也需要查阅它的前置（Mantle，同为 MIT 授权）
