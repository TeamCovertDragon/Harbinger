# 附录 I：相关开发资源

虽然 Harbinger 力图覆盖 Mod 开发中会遇到的种种问题及它们的解答，但 Harbinger 本身并不能取代其他开发资源的地位——历史悠久的 Mod 的源码、互联网上某个角落里的贴子、甚至是书店里静静躺着的一本 Java 相关书籍，诸如此类，不胜枚举。
这一附录的意义便是列出那些有重要参考价值的资源。在遇到问题止步不前时，你也许可以从这里找到灵感。

这样一个列表也决定了它不可能绝对客观。尽管如此，我们力求通过拓宽列表的广度来抵消主观评价带来的影响。

此外，参考其他 Mod 的源码之前，请务必留意你要参考的项目的许可证——如果你决定复制粘贴，这些许可证便有影响你的项目的可能！

## 那些历史<!-- 相对 -->悠久的 Mod 的源码

  - Charset：https://github.com/CharsetMC/Charset
    - 可参考的内容
      - 如何在不影响原版进度的情况下屏蔽原版工作台合成（位于 Charset 的 Storage-Chest 模块中）
      - 高内聚的模块化设计
  - 林业（Forestry）：https://github.com/ForestryMC/ForestryMC
    - 可参考的内容
      - 如何在物品上塞大量数据（蜜蜂、树苗、蝴蝶等）
      - 自动收割作物的农场
    - 劣势
      - 有数个抽象层，需要花时间阅读
  - 沉浸工程（Immersive Engineering）：https://github.com/BluSunrize/ImmersiveEngineering
    - 可参考的内容
      - 多方块结构的控制及渲染
      - 一套相对简单的电网实现
    - 劣势
      - 有时候方法体会非常长，导致可读性略微下降
      - 自定义授权，可能对有许可方面要求的用户不甚友好
  - JEI（Just Enough Items)：https://github.com/mezz/JustEnoughItems
    - 可参考的内容
      - 简单干净的物品管理器界面 Overlay 实现
      - API 的架构
  - 群峦传说（TerraFirmaCraft）：https://github.com/TerraFirmaCraft/TerraFirmaCraft
    - 可参考的内容
      - 如何替换主世界生成器
      - 如何生成大矿脉
      - 如何生成起伏巨大的地形
      - 气候系统
  - 匠魂（Tinkers' Construct）https://github.com/SlimeKnights/TinkersConstruct
    - 可参考的内容
      - 一套高效的拼接模型的实现，用于实现匠魂工具及武器的“组合”模型
      - 多方块结构（冶炼炉等）的实现
    - 劣势
      - 时常也需要查阅它的前置（Mantle）
  - 暮色森林（Twilight Forest）https://github.com/TeamTwilight/twilightforest
    - 可参考的内容
      - 如何写新维度
      - 如何写新 Boss 怪
      - 如何写大型结构的生成
