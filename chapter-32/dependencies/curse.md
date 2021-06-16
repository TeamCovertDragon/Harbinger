# CurseMaven

[CurseMaven](https://github.com/Wyn-Price/CurseMaven) 是个把整个 CurseForge 包装成一个 Maven 仓库的 Gradle 插件。
有了 CurseMaven 你就能轻松解决“这个 Mod 在 CurseForge 上发布，但是没有提供任何 Maven 仓库”的棘手问题。就像这样：

```groovy
buildscript {
  repositories {
    maven {
      url = "https://plugins.gradle.org/m2/"
    }
    maven {
      name = "forge"
      url = "http://files.minecraftforge.net/maven"
    }
  }
  dependencies {
    classpath 'com.wynprice.cursemaven:CurseMaven:2.1.1' // 声明我们会用到 CurseMaven，版本 2.1.1
    classpath 'net.minecraftforge.gradle:ForgeGradle:2.3-SNAPSHOT'
  }
}

apply plugin: 'net.minecraftforge.gradle.forge'
apply plugin: 'com.wynprice.cursemaven' // 加载 CurseMaven 这个插件

dependencies {
  // 这里我们声明了 JEI 的依赖，并使用 deobfCompile 自动转换 mapping。
  // CurseMaven 最终会去这个地址下载 jar，注意结尾的数字：
  // https://www.curseforge.com/minecraft/mc-mods/jei/files/2724420
  deobfCompile "curse.maven:jei:2724420"

  // 另一个例子：Astral Sorcery。我们要使用的是这个版本的 Astral Sorcery：
  // https://www.curseforge.com/minecraft/mc-mods/astral-sorcery/files/2920254
  // 那么我们这么声明：
  deobfCompile "curse.maven:astral-sorcery:2920254"
}

// 其余无关内容略去
```