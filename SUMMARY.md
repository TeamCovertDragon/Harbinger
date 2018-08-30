## 目录

### 正文

* [1. 一切的开始](chapter-1/index.md)
* [2. 事件](chapter-2/index.md)
* [3. 物品](chapter-3/index.md)
* [4. 方块](chapter-4/index.md)
  * [4.1 方块状态](chapter-4/block-state.md)
  * [4.2 掉落概论](chapter-4/block-drop.md)
* [5. 注册表](chapter-5/index.md)
* [6. 模型](chapter-6/index.md)
  * [6.1 方块及物品模型](chapter-6/vanilla/index.md)
    * [6.1.1 染色](chapter-6/vanilla/tint.md)
  * [6.2 Forge 的扩展](chapter-6/forge-extension/index.md)
    * [6.2.1 Forge BlockState V1](chapter-6/forge-extension/forge-v1.md)
    * [6.2.2 其他格式的模型](chapter-6/forge-extension/other-format.md)
  * [6.3 自定义模型](chapter-6/custom-model/index.md)
    * [6.3.1 `IBakedModel`](chapter-6/custom-model/index.md#Baked%20Model)
    * [6.3.2 `IModel`](chapter-6/custom-model/forge-extension.md)
* [7. 实体](chapter-7/index.md)
* [8. 药水与状态效果](chapter-8/index.md)
* [9. 附魔](chapter-9/index.md)
* [10. 方块实体（TileEntity）](chapter-10/index.md)
* [11. 特种模型](chapter-11/index.md)
  * [11.1 TESR](chapter-11/tesr/index.md)
  * [11.2 Forge 的扩展](chapter-11/forge-extension/index.md)
    * [11.2.1 FastTESR](chapter-11/forge-extension/fast-tesr.md)
    * [11.2.2 Animation State Machine](chapter-11/forge-extension/animation.md)
    * [11.2.3 `TEISR`](chapter-11/tesr/teisr.md)
  * [11.3 实体模型](chapter-11/entity-model/index.md)
* [12. 用户交互界面（GUI）](chapter-12/index.md)
* [13. 国际化与本地化（I18n & L10n）](chapter-13/index.md)
  * [13.1 文本组件（`ITextComponent`）](chapter-13/text-component.md)
* [14. 网络 IO](chapter-14/index.md)
  * [14.1 TileEntity 内建的解决方案](chapter-14/tile-entity-sync.md)
  * [14.2 Container 内建的解决方案](chapter-14/container-sync.md)
  * [14.3 `SimpleNetworkWrapper`](chapter-14/built-in-solution/simple-network-wrapper.md)
  * [14.4 `FMLEventChannel`](chapter-14/built-in-solution/fml-event-channel.md)
* [15. 音效控制](chapter-15/index.md)
* [16. 粒子效果](chapter-16/index.md)
* [17. 世界生成](chapter-17/index.md)
  * [17.1 `IWorldGenerator`](chapter-17/fml-world-gen-interface.md)
* [18. 结构生成](chapter-18/index.md)
* [19. 新维度](chapter-19/index.md)
* [20. 合成表](chapter-20/index.md)
  * [20.1 Forge 矿物词典（Ore Dictionary）](chapter-20/ore-dictionary.md)
  * [20.2 Forge 的扩展](chapter-20/forge-extension/index.md)
    * [20.2.1 `IConditionFactory`](chapter-20/forge-recipe-condition.md)
    * [20.2.2 `IIngredientFactory`](chapter-20/forge-ingredient-factory.md)
    * [20.2.3 `IRecipeFactory`](chapter-20/forge-recipe-factory.md)
  * [20.3 重新实现 `IRecipe`](chapter-20/custom-recipe.md)
  * [20.4 扩展篇：熔炉配方及燃料值](chapter-20/vanilla-furnace.md)
* [21. 进度（Advancement）](chapter-21/index.md)
* [22. 战利品表（Loot Table）](chapter-22/index.md)
* [23. 命令](chapter-23/index.md)
  * [Forge 的扩展：`CommandTreeBase`](chapter-23/command-tree.md)
* [24. Forge 权限系统](chapter-24/index.md)
* [25. Forge 配置文件系统](chapter-25/index.md)
* [26. Forge 流体系统](chapter-26/index.md)
* [27. Forge Capability 系统](chapter-27/index.md)
  * [27.1 Forge 内建的 Capability](chapter-27/built-in/index.md)
    * [27.1.1 `IItemHandler`](chapter-27/built-in/item.md)
    * [27.2.2 `IFluidHandler`, `IFluidHandlerItem`](chapter-27/built-in/fluid.md)
    * [27.2.3 `IEnergyStorage`](chapter-27/built-in/energy.md)
    * [27.2.4 `IAnimation`](chapter-27/built-in/animation.md)
  * [27.2 属于你自己的第一个 Capability](chapter-27/custom.md)
* [28. 跨 Mod 兼容及 Add-on 制作](chapter-28/index.md)
  * [28.1 跨 Mod 通信（Inter-Mod Communication，IMC）](chapter-28/imc.md)
  * [28.2 使用第三方 Capability](chapter-28/3rd-party-cap.md)
* [29. MCP](chapter-29/index.md)

<!--
待考虑：
1. 调试：
 - Crash report 内容追加（`ICrashCallable`）
 - F3 debug 界面内容追加
 - 原版内置的 profiler /debug 命令
 - Logger 的使用
 - Eclipse/IDEA 的调试器？

2.键盘及鼠标输入
 - 热键注册
 - 如何追踪鼠标位置？

3. 数据迁移
 - 注册表系统自带的重映射（RegistryEvent.MissingMapping<T>)
 - 原版的 DataFix 及 Forge 的扩展
 -->

### 实战

* [31. 实战：？？？](chapter-31/index.md)
* [32. 实战：微波炉](chapter-32/index.md)
* [33. 实战：移动电源](chapter-33/index.md)
* [34. 实战：Kotlin](chapter-34/index.md)
* [35. 实战：？？？](chapter-35/index.md)
* [36. 实战：？？？](chapter-36/index.md)
* [37. 实战：多方块结构](chapter-37/index.md)
* [38. 实战：？？？](chapter-38/index.md)
* [39. 实战：？？？](chapter-39/index.md)
* [40. 实战：？？？](chapter-40/index.md)

### 附录

* [41. 附录一：FML 生命周期](chapter-41/index.md)
* [42. 附录二：？？？](chapter-42/index.md)
