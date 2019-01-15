## 跨 Mod 兼容，及扩展 Mod 的制作

可以说，这是现在整合包存在的基石。Mod 之间要互相打交道，如何正确处理这些问题便是本章的话题。

### Gradle

基于 Forge 的 Mod 开发环境是基于 Gradle 的。所以导入其他 Mod 的方式和正常的引入第三方 jar 的方式毫无差别。

```groovy
dependencies {
    deobfCompile "net.mezz:jei:3.1.3.13:api"

    runtime "net.mezz:jei:3.1.3.13"
}
```

不，其实还是有一点小差别的：`deobfCompile` 和 `deobfProvided`。这两个 `configuration` 都是 ForgeGradle 提供的，它们的存在和 MCP 有关系，具体细节会在[第二十九章](../chapter-29/index.md)讲到。

### 那我想添加对某某 Mod 的兼容！怎么做！

不好意思没有定式。这种东西应该直接读对应的文档。  
唯一一件有定式的事是：如何正确声明对某个 Mod 的依赖关系。

```
dependencies = "required-after:mod_a;after:mod_b;before:mod_c;required-client:mod_d"
```

<!-- TODO 解释这串字符串的含义-->

### 一些不成文的规矩

漫长的岁月里社区居然进化出一堆不成文的规矩...比如

  * 矿物词典名一定形如 `ingotIron`、`gemDiamond`，即把形态放前面，物质类别放后面，小写驼峰。
  * 在 `PostInitializationEvent` 中处理跨 Mod 兼容。实际上这个的意义不是特别大了，但在少数情况下还是相当有用的。
