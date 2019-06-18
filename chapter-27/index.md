# 流体

在 Minecraft Mod 社区发展的早先时候<!-- TODO 具体是多早？-->，就有很多 Mod（比如 BuildCraft、Railcraft、Thermal Expansion 等）开始引入流体的概念。当时最先出现的是液体，但随着蒸汽等气体的引入，Modder 们发现这两者在代码上的差别基本可以忽略不计。在经过大量 Modder 的打磨后，Forge 的流体系统有了今天的样子。

<!-- 冷知识：曾经这套系统在 Forge 里叫液体（Liquid）。-->

## 第一个流体

```java
// 构造器中第一个参数是流体的唯一识别 ID，第二个参数是材质位置，第三个则是流动时的材质。
// 此外，Fluid 还有如下几个构造器：
//   - String name, ResourceLocation still, ResourceLocation flow, int color
//     允许指定流体的“颜色”，颜色以 ARGB 的格式出现。
//   - String name, ResourceLocation still, ResourceLocation flow, java.awt.Color color
//     同上，但颜色以 java.awt.Color 的形式出现。
//   - String name, ResourceLocation still, ResourceLocation flow, ResourceLocation overlay
//     允许指定流体的遮罩纹理。
//   - String name, ResourceLocation still, ResourceLocation flow, ResourceLocation overlay, int color
//     同时允许指定流体的“颜色”和遮罩纹理，颜色以 ARGB 的格式出现。
//   - String name, ResourceLocation still, ResourceLocation flow, ResourceLocation overlay, java.awt.Color color
//     同上，但颜色以 java.awt.Color 的形式出现。
public static final Fluid myFluid = new Fluid("example_fluid", new ResourceLocation("example_mod:example_fluid")).setGaseous(true).setDensity(Integer.MAX_VALUE);

// 此方法会返回一个 boolean。
// 返回 true 代表注册成功。
// 返回 false 则代表“已有同名流体注册”，但 Forge 仍然保留了一份此流体对象的引用，
// 这样一来即便注册失败，仍可以在 new FluidStack 时使用这个注册失败的版本。
// 使用注册失败的 Fluid 来创建 FluidStack 时，Forge 会自动将其转换到转注册成功的
// 那个同名 Fluid 对象上去。
FluidRegistry.registerFluid(myFluid);
```
