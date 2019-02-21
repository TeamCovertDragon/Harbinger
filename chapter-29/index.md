## 跨 Mod 兼容，及扩展 Mod 的制作

可以说，这是现在整合包存在的基石。Mod 之间要互相打交道，如何正确处理这些问题便是本章的话题。

### Gradle

基于 Forge 的 Mod 开发环境是基于 Gradle 的。所以导入其他 Mod 的方式和正常的引入第三方 jar 的方式毫无差别。

```groovy
dependencies {
    deobfCompile "mezz.jei:jei_1.12.2:4.13.1.220"

    runtime group: 'mezz.jei', name: 'jei_1.12.2', version: '4.13.1.220'
}
```

不，其实还是有一点小差别的：`deobfCompile` 和 `deobfProvided`。这两个 `configuration` 都是 ForgeGradle 提供的，它们的存在和 MCP 有关系，具体细节会在[第二十九章](../chapter-29/index.md)讲到。

### 那我想添加对某某 Mod 的兼容！怎么做！

不好意思没有定式。这种东西应该直接读对应的文档。

### 正确声明依赖

虽然跨 Mod 兼容没有定式，但 FML 依然允许你指定你自己的 Mod 的加载顺序。

```java
@Mod(
        modid = "my_mod",
        name = "Example Mod",
        version = "0.1.0",
        dependencies = "required-after:mod_a;after:mod_b;before:mod_c;required-client:mod_d"
)
```

<!-- https://github.com/MinecraftForge/MinecraftForge/pull/4403 -->
<!-- TODO 这样解释未免太粗暴了 -->

|Directive|含义|
|:------|:------|
|`required`|声明对指定 Mod 的硬依赖。缺失对应 Mod 时 FML 会提示缺失 Mod。|
|`after`|声明在指定 Mod 之后加载。若声明了 Mod 的版本范围，则会在 Mod 版本不对时提示缺失 Mod。不安装对应 Mod 时不会提示错误。|
|`before`|声明在指定 Mod 之前加载。若声明了 Mod 的版本范围，则会在 Mod 版本不对时提示缺失 Mod。不安装对应 Mod 时不会提示错误。|
|`server`|声明只在物理服务器端使用此依赖项。|
|`client`|声明只在物理客户端使用此依赖项。|

### 一些不成文的规矩

  * 矿物词典名一定形如 `ingotIron`、`gemDiamond`，即把形态放前面，物质类别放后面，小写驼峰。
  * 在 `PostInitializationEvent` 中处理跨 Mod 兼容。实际上这个的意义不是特别大了，但在少数情况下还是相当有用的。
