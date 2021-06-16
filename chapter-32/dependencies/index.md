# Gradle 的依赖管理

写 Minecraft Mod 的时候时常会需要跟其他 Mod 打交道，引用其他的 jar 在所难免。

## 引入新的依赖项目

[和普通的基于 Gradle 的 Java 项目完全一致][ref-1]。就像这样：

```groovy
repositories {
    // 我们在这里声明阿里云的 maven 镜像站。
    maven {
        url = "https://maven.aliyun.com/repository/central"
    }
}

dependencies {
    // Efficient Java Matrix Library (EJML) 是个专门解决线性代数相关问题的库。
    // 网站：http://ejml.org/
    compile 'org.ejml:all:0.30'
}
```

## 我怎么确定哪个 Maven 镜像有[Mod 名字]？

很遗憾这个问题非常棘手。
一般来说，Mod 发布页或者它的源码仓库的 README 会提及 Maven 相关的信息，但奈何不是所有 Modder 都有时间折腾这个。

天无绝人之路，你仍然有这么几个选择：

  - [CurseMaven](./curse.md)
  - [使用 `mavenLocal()`](./local.md)


[ref-1]: https://docs.gradle.org/4.10.2/userguide/declaring_dependencies.html

