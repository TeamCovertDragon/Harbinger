## 流体

在 Minecraft Mod 社区发展的早先时候<!-- TODO 具体是多早？-->，就有很多 Mod（比如 BuildCraft、Railcraft、Thermal Expansion 等）开始引入流体的概念。当时最先出现的是液体，但随着蒸汽等气体的引入，Modder 们发现这两者在代码上的差别基本可以忽略不计。在经过大量 Modder 的打磨后，Forge 的流体系统有了今天的样子。

<!-- 冷知识：曾经这套系统在 Forge 里叫液体（Liquid）。-->

### 第一个流体

````java
// 构造器中第一个参数是流体的唯一识别 ID，第二个参数是材质位置。
// 同时有一个三参数构造器可用，此时第二个参数是静止时的材质，第三个是流动时的材质。
public static final Fluid myFluid = new Fluid("example_fluid", new ResourceLocation("example_mod:example_fluid")).setGaseous(true).setDensity(Integer.MAX_VALUE);

// 此方法会返回一个 boolean。
// 返回 true 代表注册成功。
// 返回 false 则代表“已有同名流体注册”，但 Forge 仍然保留了一份此流体对象的引用，
// 这样一来即便注册失败，仍可以在 new FluidStack 时使用这个注册失败的版本。
FluidRegistry.registerFluid(myFluid);
````
