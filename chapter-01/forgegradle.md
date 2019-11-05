# ForgeGradle

ForgeGradle 是 Forge 开发团队推出的 Gradle 插件，主要用途就是简化基于 MCP+Forge 的 Minecraft Mod 开发的流程。

## 为什么？

思考一下 Mod 开发和发布的流程。通常的情况下，玩家面对的 Minecraft 使用的是混淆名，但是开发者在开发 Mod 时不太可能硬啃混淆名，而是使用某种形式的反混淆名（比如 MCP）。这样一来，当开发者编译 Mod 时，编译器是基于反混淆后的代码，而非玩家实际上使用的混淆代码进行编译的。如此一来，如果不先把 Mod 所引用的 Minecraft 底层代码混淆回去，Mod 是肯定无法运行的。同样的，为了方便开发 Mod，首先你要对 Minecraft 进行反混淆。

再往后退一步思考：开发 Minecraft 的 Mod 首先需要 Minecraft 它本身。别忘了 Minecraft 是一个商业软件，你不可能直接从 Maven Central、JCenter、Sonatype 之类的地方下载到它。能下载到它的地方只有 Mojang 自己的服务器。

此外，在开发环境运行 Minecraft 还需要下载它所有的 assets，同时还要提供正确的启动参数，基本上等同于写一个 Minecraft 的启动器（除了启动的入口是 IDE 的 Run/Debug 以及两个 Gradle Task，下文会详细说明）。

显然，这些 Mod 开发必须经过的流程是可以自动化的。

## ForgeGradle 能干什么？

  - 部署开发环境（下载 Minecraft、反编译及反混淆 Minecraft、下载 assets），有三种：
    - 基本上所有 Mod 开发者在平时开发 Mod 时都会使用的带反编译出的源码的开发环境（`setupDecompWorkspace` task)
    - 持续集成服务器（CI）经常使用的用于自动构建 Mod 的环境（`setupCIWorkspace` task）
    - 不太常用的仅有反混淆后的 Minecraft 的开发环境（`setupDevWorkspace` task）
  - 依赖处理
    - 用于反混淆依赖项目的 scope（`deobfCompile` 和 `deobfProvided`），在开发跨 Mod 兼容或扩展 Mod 时经常用到
  - IDE 相关
    - 自动生成 IntelliJ IDEA 的 Run/Debug Configuration（`genIntellijRuns` task）
  - 连带当前项目一起运行 Minecraft（不常用）：
    - 运行服务器（`runServer` task）
    - 运行客户端（`runClient` task）
  - 在执行 Gradle Java 插件的 `build` task 时可自动完成对源码的重混淆

## 面向用户的配置

ForgeGradle 需要配置的东西并不多，最常用的选项都可以在 `build.gradle` 的 `minecraft` block 中找到：

```
minecraft {
    version = "1.12.2-14.23.5.2836"

    runDir = "run"

    mappings = "stable_39"

    makeObfSourceJar = true
}
```

  - `version` 代表 Minecraft 和 Forge 版本。
  - `runDir` 代表通过 IDE/Gradle 运行 Minecraft 时，Minecraft 的根目录。这里的路径是相对于项目根目录来说的。
  - `mappings` 代表 MCP 版本号。关于 MCP 版本号的说明[请参阅 MCP 一节](mcp.md#)。
