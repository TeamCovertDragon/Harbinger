## 国际化与本地化（Internationalization & Localization，简称 I18n/L10n）

Minecraft 是一个面向世界的游戏。它没有任何的特定的文化背景。但我们并不能无视 Minecraft 的玩家来自世界各地，说各种各样的语言的问题。为此 Minecraft 引入了国际化与本地化支持。国际化的意思是“为本地化打基础”，也就是一堆让目标软件（比如 Minecraft）能支持多种语言的基础代码；本地化大部分时候是在翻译，不过本地化有时还需要考虑时间与日期格式、货币单位、书写习惯、乃至文化差异。

### 汉化工

如果你有汉化 Mod 的经验，你大概会知道，Minecraft 的语言文件都位于 `assets/[modid]/lang` 下。这个在最初写第一个物品和方块时提到过。
但这里想说的事情是这样的，语言文件中的键是没有统一标准的。换句话说，哪怕你的 `en_us.lang` 这么写：

```
我想要什么键就可以有什么键.我说过这是真的.我重复一遍，这是真的=我都说了这是真的你不信
```

这样的语言文件也是成立的，虽然正常的 Modder 都会告诉你这不是个好习惯。

```java
assert I18n.format("我想要什么键就可以有什么键.我说过这是真的.我重复一遍，这是真的").equals("我都说了这是真的你不信")
```

那些有一定标准的键，都和 Minecraft 的底层有关系。比如物品的本地化键都是 `item.` 开头，就是因为 `Item` 的 `getTranslationKey` 总是会给键加上 `item.` 的前缀再返回给你。

#### `zh_CN.lang` vs `zh_cn.lang`

1.11 开始，资源包格式版本号更新到了 3。自此，资源包中所有文件名强制使用小写字母（正则表达式 `[a-z0-9_]+`）。所以你应该使用的是 `zh_cn.lang`。

|资源包格式版本 |文件名       |Minecraft 版本|
|:------      |:------     |:------      |
|1            |`zh_CN.lang`|1.6          |
|1            |`zh_CN.lang`|1.7          |
|2            |`zh_CN.lang`|1.8          |
|2            |`zh_CN.lang`|1.9          |
|2            |`zh_CN.lang`|1.10         |
|3            |`zh_cn.lang`|1.11         |
|3            |`zh_cn.lang`|1.12         |
|4            |`zh_cn.json`|1.13         |

<!-- Mojang 在 1.13 把这玩意改成了 json，笔者也很无奈啊 -->

这里有一个小坑。所有的 Forge Mod 都相当于一个资源包，所以在 resource 目录下可以有一个 `pack.mcmeta`。但 FML 会在找不到这个文件（`java.io.FileNotFoundException`）的时候直接补充一个 `pack_format` 是 `2` 的 `pack.mcmeta` 上去，然后 1.12 的 Minecraft 底层中居然还有对 `pack_format == 2` 的资源包的向后兼容（`LegacyV2Adapter`，Notch 名 `ceq`）。于是——你有可能会发现 `zh_cn.lang` 加载不出来。解决方法是把下面这个覆盖 `resource/pack.mcmeta` 里的所有内容（记得把 `description` 字段里的内容换一下）：

```json
{
    "pack": {
        "pack_format": 3,
        "description": "Resources used by My Mod"
    }
}
```

不过为什么不直接用 `zh_CN.lang`？因为 1.13 没这个向后兼容了。为了以后着想，请用小写文件名。

#### 两个 `I18n`

你可能已经注意到了：有两个名为 I18n 的类。他们分别是：

  * `net.minecraft.client.resource.I18n`
  * `net.minecraft.util.text.translation.I18n`

这两个类完全不一样。`client` 包下的 `I18n` 只能在物理客户端上使用；虽然 `util` 下的 `I18n` 亦可在物理服务器上使用，但它被打上了 `@Deprecated` 的注解。

你应当用客户端专用的那个版本的 `I18n`。`util` 下的 `I18n` 不应被使用；事实上在 1.13 中它也直接消失了。究其原因，是因为逻辑服务器上不应有本地化相关的内容，毕竟那是面向机器的，不是面向用户的。如果你遇到了“需要在服务器上本地化”的需求，不妨考虑一下你的设计上是否有改进的空间。
