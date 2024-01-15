## 总目录

### 前言

* 前言
  * [写在前面：这是什么？](README.md)
  * [序：《异教与隐士》](preface/index.md)

### 正文

* [1. 站在巨人的肩膀上](chapter-01/index.md)
  * [1.1 MCP](chapter-01/mcp.md)
  * [1.2 Forge](chapter-01/forge.md)
  * [1.3 ForgeGradle](chapter-01/forgegradle.md)
* [2. 一切的开始](chapter-02/index.md)
  * [2.1 Mod 元数据](chapter-02/metadata.md)
  * [2.2 Mod 的构建与发布](chapter-02/build.md)
* [3. 事件](chapter-03/index.md)
* [4. 物品](chapter-04/index.md)
  * [4.1 `ItemStack`](chapter-04/item-stack.md)
  * [4.2 附魔](chapter-04/enchantment.md)
  * [4.2 物品进阶](chapter-04/advanced/index.md)
    * [4.2.1 Meta-hack](chapter-04/advanced/meta-hack.md)
    * [4.2.2 食物](chapter-04/advanced/food.md)
    * [4.2.3 工具及武器等](chapter-04/advanced/tool.md)
    * [4.2.4 护甲](chapter-04/advanced/armor.md)
    * [4.2.5 投掷物](chapter-04/advanced/throwable.md)
* [5. 方块](chapter-05/index.md)
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
  * [7.2 实体的解决方案](chapter-07/entity-sync.md)
  * 7.3 Forge 的扩展
    * [7.3.1 `SimpleNetworkWrapper`](chapter-07/forge-extension/simple-network-wrapper.md)
    * [7.3.2 `FMLEventChannel`](chapter-07/forge-extension/fml-event-channel.md)
* [8. 实体](chapter-08/index.md)
  * [8.1 AI](chapter-08/ai/index.md)
  * [8.2 生物实体属性](chapter-08/attribute/index.md)
    * [8.2.1 属性修饰符](chapter-08/attribute/modifier.md)
* [9. 方块实体（TileEntity）](chapter-09/index.md)
  * [9.1 `ITickable`](chapter-09/ticking.md)
* [10. Forge Capability 系统](chapter-10/index.md)
  * [10.1 实例：TileEntity 的物品栏](chapter-10/item-cap.md)
  * [10.2 追加 Capability](chapter-10/attach-cap.md)
  * [10.3 Capability 数据的持久化](chapter-10/persistence.md)
  * [10.4 你自己的 Capability](chapter-10/new-cap.md)
* [11. 渲染概论](chapter-11/index.md)
  * [11.1 方块及物品模型](chapter-11/baked/index.md)
    * [11.1.1 自定义物品模型映射](chapter-11/baked/custom-mesh.md)
    * [11.1.2 纹理与纹理动画](chapter-11/baked/texture.md)
    * [11.1.2 染色](chapter-11/baked/tint.md)
    * [11.1.3 方块状态映射](chapter-11/baked/block-state-json.md)
    * [11.1.4 `IStateMapper`](chapter-11/baked/state-mapper.md)
  * [11.2 自定义模型](chapter-11/custom-baked/index.md)
    * [11.2.1 `IBakedModel`](chapter-11/custom-baked/baked.md)
    * [11.2.2 `IModel`](chapter-11/custom-baked/unbaked.md)
    * [11.2.3 `ICustomModelLoader`](chapter-11/custom-baked/loader.md)
    * [11.2.4 `ItemOverrideList`](chapter-11/custom-baked/item-override.md)
  * [11.3 实体模型](chapter-11/entity-model/index.md)
    * [11.3.1 `ModelBase`](chapter-11/entity-model/encapsulated-model.md)
    * [11.3.2 相关事件](chapter-11/entity-model/events.md)
  * [11.4 TESR](chapter-11/tesr.md)
  * [11.5 Render Layer](chapter-11/render-layer.md)
  * [11.6 Forge 的扩展](chapter-11/forge-extension/index.md)
    * [11.6.1 Forge BlockState V1](chapter-11/forge-extension/forge-v1.md)
    * [11.6.2 其他格式的模型](chapter-11/forge-extension/other-format.md)
    * [11.6.3 `FastTESR`](chapter-11/forge-extension/fast-tesr.md)
    * [11.6.4 动画状态机](chapter-11/forge-extension/animation.md)
    * [11.6.5 `TileEntityItemStackRenderer`](chapter-11/forge-extension/teisr.md)
* [12. 药水与状态效果](chapter-12/index.md)
  * [12.1 状态效果](chapter-12/potion.md)
  * [12.2 状态效果与属性修饰符](chapter-12/attributes-modifier.md)
  * [12.3 药水类型](chapter-12/potion-type.md)
* [13. 国际化与本地化](chapter-13/index.md)
  * [13.1 文本组件（`ITextComponent`）](chapter-13/text-component.md)
  * [13.2 注意事项](chapter-13/note.md)
* [14. 用户交互界面（GUI）](chapter-14/index.md)
  * [14.1 Widget](chapter-14/widget/index.md)
    * [14.1.1 文本输入](chapter-14/widget/text-field.md)
    * [14.1.2 按钮](chapter-14/widget/button.md)
    * [14.1.3 列表](chapter-14/widget/list.md)
  * [14.2 游戏主界面与 HUD](chapter-14/main-screen/index.md)
  * [14.3 Toast](chapter-14/toast/index.md)
  * [14.4 `Container` 与数据同步](chapter-14/container.md)
* [15. 音效控制](chapter-15/index.md)
* [16. 粒子效果](chapter-16/index.md)
* [17. 资源包](chapter-17/index.md)
* [18. 用户输入](chapter-18/index.md)
  * [18.1 键盘输入](chapter-18/keyboard.md)
  * [18.2 鼠标输入](chapter-18/mouse.md)
    * [18.2.1 GUI 中的鼠标控制](chapter-18/mouse-in-gui.md)
* [19. 世界生成与新维度](chapter-19/index.md)
  * [19.1 地图特性（Feature）](chapter-19/world-gen-feature/index.md)
    * [19.1.1 矿石生成](chapter-19/world-gen-feature/ore.md)
    * [19.1.2 Retrogen](chapter-19/world-gen-feature/retro-gen.md)
  * [19.2 生物群系（Biome）](chapter-19/biome/index.md)
    * [19.2.1 复用地图特性](chapter-19/biome/feature.md)
    * [19.2.2 生物群系装饰器（Decorator）](chapter-19/biome/decorator.md)
    * [19.2.3 刷新生物](chapter-19/biome/natural-spawn.md)
    * [19.2.3 附加篇：关于骨粉](chapter-19/biome/flower-entry.md)
  * [19.3 大型结构](chapter-19/structure/index.md)
    * [19.3.1 `MapGenBase`](chapter-19/structure/base.md)
    * [19.3.2 `MapGenStructure`](chapter-19/structure/structure.md)
    * [19.3.3 `Template`](chapter-19/structure/template.md)
  * [19.4 新维度](chapter-19/dimension/index.md)
    * [19.4.1  可选的 `WorldType`](chapter-19/dimension/type.md)
    * [19.4.2 `WorldProvider`](chapter-19/dimension/dimension.md)
    * [19.4.3 `IChunkGenerator`](chapter-19/dimension/chunk-gen.md)
    * [19.4.4 `BiomeProvider`](chapter-19/dimension/biome-gen.md)
* [20. 村庄](chapter-20/index.md)
  * [20.1 村民职业](chapter-20/villager-profession.md)
  * [20.2 村庄结构](chapter-20/village-structure.md)
* [21. 合成表](chapter-21/index.md)
  * [21.1 Forge 的扩展](chapter-21/forge-extension/index.md)
    * [21.1.1 矿物词典（Ore Dictionary）](chapter-21/forge-extension/ore-dictionary.md)
    * [21.1.2 `IConditionFactory`](chapter-21/forge-extension/condition.md)
    * [21.1.3 `IIngredientFactory`](chapter-21/forge-extension/ingredient-factory.md)
    * [21.1.4 `IRecipeFactory`](chapter-21/forge-extension/recipe-factory.md)
    * [21.1.5 常量池（`_constants.json`）](chapter-21/forge-extension/constants.md)
  * [21.2 重新实现 `IRecipe`](chapter-21/custom-recipe.md)
  * [21.3 扩展篇：熔炉配方及燃料值](chapter-21/vanilla-furnace.md)
  * [21.4 扩展篇：酿造台配方](chapter-21/vanilla-brewing-stand.md)
  * [21.5 扩展篇：铁砧“配方”](chapter-21/forge-extension/anvil-events.md)
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
  * [26.3 同步配置文件](chapter-26/sync.md)
* [27. Forge 流体系统](chapter-27/index.md)
  * [27.1 `Fluid` 与 `FluidStack`](chapter-27/fluid-stack.md)
  * [27.2 流体方块](chapter-27/block.md)
  * [27.3 流体容器](chapter-27/container/index.md)
    * [27.3.1 物品形式的容器](chapter-27/container/item.md)
    * [27.3.2 方块形式的容器](chapter-27/container/block.md)
    * [27.3.3 流体容器之间的交互](chapter-27/container/interaction.md)
  * [27.4 流体渲染相关](chapter-27/render.md)
* [28. ？](chapter-28/index.md)
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

* [31. 附录 I：相关开发资源](chapter-31/index.md)
* [32. 附录 II：Gradle](chapter-32/index.md)
  * [依赖管理](chapter-32/dependencies/index.md)
    * [CurseMaven](chapter-32/dependencies/curse.md)
    * [`mavenLocal()`](chapter-32/dependencies/local.md)
  * 自动化
    * 读取配置文件
    * 自动生成 `MANIFEST.MF`
  * 信息安全
    * Jar 签名
  * 构建与发布
    * Shadow/Shade
    * `publishToMavenLocal` 任务
    * CurseGradle
* [33. 附录 III：？？？](chapter-33/index.md)
* [34. 附录 IV：？？？](chapter-34/index.md)
* [35. 附录 V：？？？](chapter-35/index.md)
* [36. 附录 VI：？？？](chapter-36/index.md)
* [37. 附录 VII：？？？](chapter-37/index.md)
* [38. 附录 VIII：？？？](chapter-38/index.md)

### 实战

* [39. 实战（上）](chapter-39/index.md)
* [40. 实战（下）](chapter-40/index.md)
  * [40.1 问题 1](chapter-40/practice-1.md)
  * [40.2 问题 2](chapter-40/practice-2.md)
  * [40.3 问题 3](chapter-40/practice-3.md)
  * [40.4 问题 4](chapter-40/practice-4.md)
  * [40.5 问题 5](chapter-40/practice-5.md)
  * [40.6 问题 6](chapter-40/practice-6.md)
  * [40.7 问题 7](chapter-40/practice-7.md)
  * [40.7 问题 8](chapter-40/practice-8.md)

### 后记

* 后记
  * [跋：《Aboard Harbinger Gunship》](afterword/index.md)
  * [写在后面：鸣谢名单](afterword/credits.md)
  * [参考书目](afterword/bibliography.md)
