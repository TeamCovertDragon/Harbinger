# Mod 的构建与发布

因为 Forge 的 MDK 是基于 Gradle 和 ForgeGradle 插件的，所以构建 Mod 的过程和构建普通 Java 程序并无二致：

```
$ ./gradlew build
```

或者在 Windows 上：

```
> gradlew.bat build
```

实际上主流 IDE 的 Gradle 支持插件中都应该有执行指定 Gradle task 的功能，所以实际上不一定非得要用命令行，在 IDE 里操作就可以了。  
构建结果位于 MDK 目录的 `build/libs` 目录下。

## 发布

既然 Mod 的构建使用的是标准的 `build` task，那我们自然也可以用常规的方法来发布 Mod，比如手动复制出来发布，或是利用 CI 自动化测试构建，然后推送到某个 maven repo 中去。
