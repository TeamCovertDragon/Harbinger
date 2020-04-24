# Gradle 速查指南

[Gradle](https://gradle.org) 是个构建工具。
果你之前用过 [Maven](http://maven.apache.org/)，那 Gradle 本质上就是个功能更强的 Maven。

Gradle 的存在让我们可以直接一行 `gradle build` 命令就能搞定编译（甚至是发布）而不用头疼别的什么。

而这一章附录则收录一些在使用 Gradle 甚至是 ForgeGradle 时会遇到的常见需求解决方案。

## 速查指南索引

  - [依赖管理](./dependencies/index.md)
    - [CurseMaven](./dependencies/curse.md)
    - [`mavenLocal()`](./dependencies/local.md)
  - [自动化](./automation/index.md)
    - 读取配置文件
    - 自动生成 `MANIFEST.MF`
  - 信息安全
    - Jar 签名
  - [构建与发布](./publish/index.md)
    - Shadow/Shade
    - [`publishToMavenLocal` 任务](./publish/local.md)
    - CurseGradle

