# 本地 Maven 仓库

Maven 不一定非得是在互联网的某个角落里，也可以是在你自己的电脑上。
比如所谓的 `mavenLocal()` 仓库。

```groovy
repositories {
    mavenLocal()
}

dependencies {
    deobfCompile 'my_mod:my_lib:0.1.0'
}
```

需要搭配 [`publishToMavenLocal` 任务](../publish/local.md)使用。

## 这个仓库究竟在我电脑上的什么地方？

按照 [Gradle 手册][ref-1] 的说法，一般来说是 `${USER_HOME}/.m2/`。也就是说：

  - 对于 Windows，是 `%USERPROFILE%/.m2/`。基本上就是 `C:\Users\[用户名]\.m2`。
  - 对于 Linux、macOS 等深受 Unix 影响的系统，是 `$HOME/.m2/`
    - 或者说是 `~/.m2`，如果你用的 Shell 支持展开 `~`。

[ref-1]: https://docs.gradle.org/current/userguide/declaring_repositories.html#sub:maven_local