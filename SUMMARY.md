## 目录

### 正文

* [1. 一切的开始](chapter-1/index.md)
  * [1.1 Mod 元数据](chapter-1/metadata.md)
* [2. 事件](chapter-2/index.md)
* [3. 物品](chapter-3/index.md)
  * [3.1 物品进阶](chapter-3/advanced/index.md)
    * [3.1.1 Meta-hack](chapter-3/advanced/meta-hack.md)
    * [3.1.2 食物](chapter-3/advanced/food.md)
    * [3.1.3 工具及武器等](chapter-3/advanced/tool.md)
    * [3.1.4 护甲](chapter-3/advanced/armor.md)
    * [3.1.5 投掷物](chapter-3/advanced/throwable.md)
* [4. 方块](chapter-4/index.md)
  * [4.1 方块状态](chapter-4/block-state.md)
  * 4.2 高级的方块
    * [4.2.1 矿石](chapter-4/advanced/ore.md)
    * [4.2.2 作物](chapter-4/advanced/crop.md)
    * [4.2.3 树苗](chapter-4/advanced/sapling.md)
    * [4.2.4 红石信号敏感的方块](chapter-4/advanced/redstone-sensitive.md)
    * [4.2.5 类似空气的方块](chapter-4/advanced/air-like.md)
  * [4.3 掉落概论](chapter-4/block-drop.md)
* [5. 注册表](chapter-5/index.md)
* [6. 模型](chapter-6/index.md)
  * [6.1 方块及物品模型](chapter-6/vanilla/index.md)
    * [6.1.1 自定义物品模型映射](chapter-6/vanilla/custom-mesh.md)
    * [6.1.2 染色](chapter-6/vanilla/tint.md)
  * [6.2 Forge 的扩展](chapter-6/forge-extension/index.md)
    * [6.2.1 Forge BlockState V1](chapter-6/forge-extension/forge-v1.md)
    * [6.2.2 其他格式的模型](chapter-6/forge-extension/other-format.md)
  * [6.3 自定义模型](chapter-6/custom-model/index.md)
    * [6.3.1 `IBakedModel`](chapter-6/custom-model/baked.md)
    * [6.3.2 `IModel`](chapter-6/custom-model/unbaked.md)
    * [6.3.3 `ICustomModelLoader`](chapter-6/custom-model/loader.md)
    * [6.3.4 `ItemOverrideList`](chapter-6/custom-model/item-override.md)
* [7. 实体](chapter-7/index.md)
* [8. 药水与状态效果](chapter-8/index.md)
  * [8.1 药水与属性修饰符](chapter-8/attributes-modifier.md)
* [9. 附魔](chapter-9/index.md)
* [10. 方块实体（TileEntity）](chapter-10/index.md)
* [11. 网络 IO](chapter-11/index.md)
  * [11.1 TileEntity 内建的解决方案](chapter-11/tile-entity-sync.md)
  * [11.2 Container 内建的解决方案](chapter-11/container-sync.md)
  * [11.3 实体的解决方案](chapter-11/entity-sync.md)
  * [11.4 `SimpleNetworkWrapper`](chapter-11/built-in-solution/simple-network-wrapper.md)
  * [11.5 `FMLEventChannel`](chapter-11/built-in-solution/fml-event-channel.md)
* [12. 特种模型](chapter-12/index.md)
  * [12.1 TESR](chapter-12/tesr.md)
  * [12.2 Forge 的扩展](chapter-12/forge-extension/index.md)
    * [12.2.1 `FastTESR`](chapter-12/forge-extension/fast-tesr.md)
    * [12.2.2 动画状态机](chapter-12/forge-extension/animation.md)
    * [12.2.3 `TileEntityItemStackRenderer`](chapter-12/forge-extension/teisr.md)
  * [12.3 实体模型](chapter-12/entity-renderer.md)
* [13. 国际化与本地化（I18n & L10n）](chapter-13/index.md)
  * [13.1 文本组件（`ITextComponent`）](chapter-13/text-component.md)
* [14. 用户交互界面（GUI）](chapter-14/index.md)
* [15. 音效控制](chapter-15/index.md)
* [16. 粒子效果](chapter-16/index.md)
* [17. 世界生成](chapter-17/index.md)
  * [17.1 `IWorldGenerator`](chapter-17/fml-world-gen-interface.md)
* [18. 结构生成](chapter-18/index.md)
* [19. 新维度](chapter-19/index.md)
* [20. 合成表](chapter-20/index.md)
  * [20.1 Forge 的扩展](chapter-20/forge-extension/index.md)
    * [20.1.1 矿物词典（Ore Dictionary）](chapter-20/forge-extension/ore-dictionary.md)
    * [20.1.2 `IConditionFactory`](chapter-20/forge-extension/condition.md)
    * [20.1.3 `IIngredientFactory`](chapter-20/forge-extension/ingredient-factory.md)
    * [20.1.4 `IRecipeFactory`](chapter-20/forge-extension/recipe-factory.md)
    * [20.1.5 常量池（`_constants.json`）](chapter-20/forge-extension/constants.md)
  * [20.3 重新实现 `IRecipe`](chapter-20/custom-recipe.md)
  * [20.4 扩展篇：熔炉配方及燃料值](chapter-20/vanilla-furnace.md)
* [21. 进度（Advancement）](chapter-21/index.md)
  * [21.1 自定义进度触发条件](chapter-21/forge-extension/custom-criterion.md)
  * [21.2 自定义 Item Predicate](chapter-21/forge-extension/custom-item-predicates.md)
* [22. 战利品表（Loot Table）](chapter-22/index.md)
  * [22.1 战利品表条件（Loot Condition）](chapter-22/condition.md)
  * [22.2 战利品表函数（Loot Function）](chapter-22/function.md)
* [23. 命令](chapter-23/index.md)
  * [23.1 函数（`.mcfunction`）](chapter-23/function.md)
  * [23.2 Forge 的扩展：`CommandTreeBase`](chapter-23/command-tree.md)
* [24. Forge 权限系统](chapter-24/index.md)
  * [24.1 `IPermissionHandler`](chapter-24/permission-handler.md)
* [25. Forge 配置文件系统](chapter-25/index.md)
  * [25.1 Forge 可视化配置文件编辑界面](chapter-25/config-gui.md)
  * [25.2 `Configuration` 类](chapter-25/raw-config.md)
* [26. Forge 流体系统](chapter-26/index.md)
  * [26.1 `Fluid` 与 `FluidStack`](chapter-26/fluid-stack.md)
  * [26.2 流体方块](chapter-26/block.md)
  * [26.3 流体容器](chapter-26/container/index.md)
    * [26.3.1 物品形式的容器](chapter-26/container/item.md)
    * [26.3.2 方块形式的容器](chapter-26/container/block.md)
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

4. ForgeGradle
  - minecraft {}
    - version
    - mapping
    - useDepAt
  - deobfCompile, deobfProvided
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
