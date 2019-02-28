## 总目录

* 前言
  * [写在前面：这是什么？](README.md)
  * [序：《异教与隐士》](preface/index.md)

### 正文

* [1. 站在巨人的肩膀上](chapter-01/index.md)
  * [1.1 MCP](chapter-01/mcp.md)
  * [1.2 Forge](chapter-1/forge.md)
  * [1.3 ForgeGradle](chapter-01/forgegradle.md)
* [2. 一切的开始](chapter-02/index.md)
  * [2.1 Mod 元数据](chapter-02/metadata.md)
* [3. 事件](chapter-03/index.md)
* [4. 物品](chapter-04/index.md)
  * [4.1 物品进阶](chapter-04/advanced/index.md)
    * [4.1.1 Meta-hack](chapter-04/advanced/meta-hack.md)
    * [4.1.2 食物](chapter-04/advanced/food.md)
    * [4.1.3 工具及武器等](chapter-04/advanced/tool.md)
    * [4.1.4 护甲](chapter-04/advanced/armor.md)
    * [4.1.5 投掷物](chapter-04/advanced/throwable.md)
* [5. 方块](chapter-04/index.md)
  * [5.1 方块状态](chapter-05/block-state.md)
  * 5.2 高级的方块
    * [5.2.1 矿石](chapter-05/advanced/ore.md)
    * [5.2.2 作物](chapter-05/advanced/crop.md)
    * [5.2.3 树苗](chapter-05/advanced/sapling.md)
    * [5.2.4 红石信号敏感的方块](chapter-05/advanced/redstone-sensitive.md)
    * [5.2.5 类似空气的方块](chapter-05/advanced/air-like.md)
  * [5.3 掉落概论](chapter-05/block-drop.md)
* [6. 注册表](chapter-06/index.md)
* [7. 物理端、逻辑端与网络 IO](chapter-07/index.md)
  * [7.1 TileEntity 内建的解决方案](chapter-07/tile-entity-sync.md)
  * [7.2 Container 内建的解决方案](chapter-07/container-sync.md)
  * [7.3 实体的解决方案](chapter-07/entity-sync.md)
  * 7.4 Forge 的扩展
    * [7.4.1 `SimpleNetworkWrapper`](chapter-07/forge-extension/simple-network-wrapper.md)
    * [7.4.2 `FMLEventChannel`](chapter-07/forge-extension/fml-event-channel.md)
* [8. 实体](chapter-08/index.md)
* [9. 方块实体（TileEntity）](chapter-09/index.md)
  * [9.1 实例](chapter-09/examples/index.md)
    * [9.1.1 箱子，或别的种类的物品容器](chapter-09/examples/inventory.md)
    * [9.1.2 流体储罐](chapter-09/examples/tank.md)
    * [9.1.3 打粉机](chapter-09/examples/grinder.md)
* [10. 附魔](chapter-10/index.md)
* [11. 渲染概论](chapter-11/index.md)
  * [11.1 方块及物品模型](chapter-11/baked/index.md)
    * [11.1.1 自定义物品模型映射](chapter-11/baked/custom-mesh.md)
    * [11.1.2 染色](chapter-11/baked/tint.md)
  * [11.2 自定义模型](chapter-11/custom-baked/index.md)
    * [11.2.1 `IBakedModel`](chapter-11/custom-baked/baked.md)
    * [11.2.2 `IModel`](chapter-11/custom-baked/unbaked.md)
    * [11.2.3 `ICustomModelLoader`](chapter-11/custom-baked/loader.md)
    * [11.2.4 `ItemOverrideList`](chapter-11/custom-baked/item-override.md)
  * [11.3 实体模型](chapter-11/index.md)
    * [11.3.1 `ModelBase`](chapter-11/encapsulated-model.md)
    * [11.3.2 相关事件](chapter-11/events.md)
  * [11.4 TESR](chapter-11/tesr.md)
  * [11.5 Render Layer](chapter-11/render-layer.md)
  * [11.6 Forge 的扩展](chapter-11/forge-extension/index.md)
    * [11.6.1 Forge BlockState V1](chapter-11/forge-extension/forge-v1.md)
    * [11.6.2 其他格式的模型](chapter-11/forge-extension/other-format.md)
    * [11.6.3 `FastTESR`](chapter-11/forge-extension/fast-tesr.md)
    * [11.6.4 动画状态机](chapter-11/forge-extension/animation.md)
    * [11.6.5 `TileEntityItemStackRenderer`](chapter-11/forge-extension/teisr.md)
* [12. 药水与状态效果](chapter-12/index.md)
  * [12.1 药水与属性修饰符](chapter-12/attributes-modifier.md)
* [13. 国际化与本地化](chapter-13/index.md)
  * [13.1 文本组件（`ITextComponent`）](chapter-13/text-component.md)
* [14. 用户交互界面（GUI）](chapter-14/index.md)
* [15. 音效控制](chapter-15/index.md)
* [16. 粒子效果](chapter-16/index.md)
* [17. 资源包](chapter-17/index.m)
* [18. 用户输入](chapter-18/index.md)
  * [18.1 键盘输入](chapter-18/keyboard.md)
  * [18.2 鼠标输入](chapter-18/mouse.md)
* [19. 世界生成与新维度](chapter-19/index.md)
  * [19.1 `IWorldGenerator`](chapter-19/fml-world-gen-interface.md)
* [20. 村庄](chapter-20/index.md)
* [21. 合成表](chapter-21/index.md)
  * [21.1 Forge 的扩展](chapter-21/forge-extension/index.md)
    * [21.1.1 矿物词典（Ore Dictionary）](chapter-21/forge-extension/ore-dictionary.md)
    * [21.1.2 `IConditionFactory`](chapter-21/forge-extension/condition.md)
    * [21.1.3 `IIngredientFactory`](chapter-21/forge-extension/ingredient-factory.md)
    * [21.1.4 `IRecipeFactory`](chapter-21/forge-extension/recipe-factory.md)
    * [21.1.5 常量池（`_constants.json`）](chapter-21/forge-extension/constants.md)
  * [21.2 重新实现 `IRecipe`](chapter-21/custom-recipe.md)
  * [21.3 扩展篇：熔炉配方及燃料值](chapter-21/vanilla-furnace.md)
* [22. 进度（Advancement）](chapter-22/index.md)
  * [22.1 自定义进度触发条件](chapter-22/forge-extension/custom-criterion.md)
  * [22.2 自定义 Item Predicate](chapter-22/forge-extension/custom-item-predicates.md)
* [23. 战利品表](chapter-23/index.md)
  * [23.1 战利品表条件（Loot Condition）](chapter-23/condition.md)
  * [23.2 战利品表函数（Loot Function）](chapter-23/function.md)
* [24. 命令](chapter-24/index.md)
  * [24.1 函数（`.mcfunction`）](chapter-24/function.md)
  * [24.2 Forge 的扩展：`CommandTreeBase`](chapter-24/command-tree.md)
* [25. Forge 权限系统](chapter-25/index.md)
  * [25.1 `IPermissionHandler`](chapter-25/permission-handler.md)
* [26. Forge 配置文件系统](chapter-26/index.md)
  * [26.1 Forge 可视化配置文件编辑界面](chapter-26/config-gui.md)
  * [26.2 `Configuration` 类](chapter-25/raw-config.md)
* [27. Forge 流体系统](chapter-27/index.md)
  * [27.1 `Fluid` 与 `FluidStack`](chapter-27/fluid-stack.md)
  * [27.2 流体方块](chapter-27/block.md)
  * [27.3 流体容器](chapter-27/container/index.md)
    * [27.3.1 物品形式的容器](chapter-27/container/item.md)
    * [27.3.2 方块形式的容器](chapter-27/container/block.md)
* [28. Forge Capability 系统](chapter-28/index.md)
  * [28.1 Forge 内建的 Capability](chapter-28/built-in/index.md)
    * [28.1.1 `IItemHandler`](chapter-28/built-in/item.md)
    * [28.2.2 `IFluidHandler`, `IFluidHandlerItem`](chapter-28/built-in/fluid.md)
    * [28.2.3 `IEnergyStorage`](chapter-28/built-in/energy.md)
    * [28.2.4 `IAnimation`](chapter-28/built-in/animation.md)
  * [28.2 属于你自己的第一个 Capability](chapter-28/custom.md)
* [29. 跨 Mod 兼容及 Add-on 制作](chapter-29/index.md)
  * [29.1 跨 Mod 通信（Inter-Mod Communication，IMC）](chapter-29/imc.md)
  * [29.2 使用第三方 Capability](chapter-29/foreign-capabilities.md)
* 30\. <!-- ls: chapter-30: No such file or directory -->

<!--
待考虑：
1. 调试：
 - Crash report 内容追加（`ICrashCallable`）
 - F3 debug 界面内容追加
 - 原版内置的 profiler /debug 命令
 - Logger 的使用
 - Eclipse/IDEA 的调试器？

2. 数据迁移
 - 注册表系统自带的重映射（RegistryEvent.MissingMapping<T>)
 - 原版的 DataFix 及 Forge 的扩展

3. NBT
  - 读写
  - 转字节流？
  - JSON <-> NBT？虽然那个格式并不是严格的 JSON。
 -->

### 附录

* [31. 附录 I：？？？](chapter-31/index.md)
* [32. 附录 II：？？？](chapter-32/index.md)
* [33. 附录 III：？？？](chapter-33/index.md)
* [34. 附录 IV：？？？](chapter-34/index.md)
* [35. 附录 V：？？？](chapter-35/index.md)
* [36. 附录 VI：？？？](chapter-36/index.md)
* [37. 附录 VII：？？？](chapter-37/index.md)
* [38. 附录 VIII：？？？](chapter-38/index.md)

### 实战

* [39. 实战：？？？](chapter-39/index.md)
* [40. 实战：？？？](chapter-40/index.md)
