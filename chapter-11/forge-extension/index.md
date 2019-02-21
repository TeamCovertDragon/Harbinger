## Forge 对原版模型系统的扩展

这是一个有 Mod 的世界。在这样的世界里方块不再是常识。我们也许需要更复杂的变换，要支持平移、缩放和旋转。原版的方块状态的声明某种意义上十分啰嗦，重复内容太多，有简化的空间。甚至，立方体也许并不能表达我们想要的形状——从三角形的侧面、到各种棱锥、再到圆柱和球。Forge 在原版模型的基础上提供了若干非常实用的工具，包括：

  - 一个更干净整洁且功能强大的方块状态定义（Forge BlockState V1 格式）
  - 对 Blitz3D（`*.b3d`）和 Wavefront OBJ（`*.obj`）两种格式的模型文件的支持

使用这些工具，基本能覆盖 99% 的对方块或物品模型奇奇怪怪的需求。有鉴于刚需的存在，Forge 还拥有下列工具用于实现更复杂的模型（比如带动画效果的模型）：

  - [`FastTESR`](fast-tesr.md)
  - [动画状态机、`AnimationTESR` 与 `AnimationModelBase`](animation.md)
  - [`TileEntityItemStackRenderer`](teisr.md)
