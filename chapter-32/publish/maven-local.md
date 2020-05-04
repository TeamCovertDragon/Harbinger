# 发布到本机 Maven 仓库

一个 Maven 仓库的本质是有特殊目录格式的目录，或称文件夹，可通过一串 URL 索引。

所以你大可以把你电脑上某个目录改装成 Maven 仓库。不过我们甚至连改装都不用——因为 `mavenLocal()` 已经在那了。

## `publishToMavenLocal`

在命令行中执行

```
./gradlew publishToMavenLocal
```

然后你的 Mod 就会去本机的 `mavenLocal()` 仓库里了。
一般来说，这个仓库的真实位置是 `${USER_HOME}/.m2/`，也就是说：

  - 对于 Windows，是 `%USERPROFILE%/.m2/`。基本上就是 `C:\Users\[用户名]\.m2`。
  - 对于 Linux、macOS 等深受 Unix 影响的系统，是 `$HOME/.m2/`
    - 或者说是 `~/.m2`，如果你用的 Shell 支持展开 `~`。