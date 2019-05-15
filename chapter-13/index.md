# 国际化与本地化（Internationalization & Localization，简称 I18n/L10n）

Minecraft 是一个面向世界的游戏。它没有任何特定的文化背景。我们并不能忽视 Minecraft 的玩家来自世界各地，使用多种多样的语言。为此 Minecraft 引入了国际化与本地化支持。国际化的意思是“为本地化打基础”，也就是一堆让目标软件（比如 Minecraft）能支持多种语言的基础代码；本地化大部分时候是在翻译，不过本地化有时还需要考虑时间与日期格式、货币单位、书写习惯、乃至文化差异。

## 汉化工

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

## 两个 `I18n`

你可能已经注意到了：有两个名为 I18n 的类。他们分别是：

  * `net.minecraft.client.resource.I18n`
  * `net.minecraft.util.text.translation.I18n`

这两个类完全不一样。`client` 包下的 `I18n` 只能在物理客户端上使用；而 `util` 下的 `I18n` 虽可在物理服务器上使用，但它被打上了 `@Deprecated` 的注解，且在物理服务端使用时语言代码被强制为 `en_us`。

你应当用客户端专用的那个版本的 `I18n`。`util` 下的 `I18n` 不应被使用；事实上在 1.13 中它也直接消失了。究其原因，是因为逻辑服务器上不应有本地化相关的内容，毕竟那是面向机器的，不是面向用户的。如果你遇到了“需要在服务器上本地化”的需求，不妨考虑一下你的设计上是否有改进的空间。

#### 添加国际化时的注意事项

笔者在翻译模组的过程中遇到了非常多国际化方面的问题，这里罗列出模组作者常犯的两个错误：

##### 重名键问题

和我们预想的不同，Minecraft 是将所有的语言文件处理成一个统一的 HashMap，即 `net.minecraft.client.resources.Locale` 类下的 `Map<String, String> properties` 字段，这意味着如果两个模组使用了相同的键，那么就会在翻译上出现冲突，显示不正确的字符。

一种比较推荐的方式是在键中添加本模组的 id 进去，比如想要为某个 GUI 按钮添加国际化支持，我们就可以这样写：

```
button.example_mod.next_page=Next Page
```

另一个容易被忽视的地方是方块和物品的 `setTranslationKey` 方法，我们在[第四章](https://harbinger.covertdragon.team/chapter-04/#%E5%91%83%E8%BF%99%E7%89%A9%E5%93%81%E5%90%8D%E5%AD%97%E4%B8%8D%E5%AF%B9)中介绍过它。为其传入字符串时就需要注意，最好添加上本模组的 id 作为语言文件的键，防止不必要的冲突。

##### 滥用国际化

适当的国际化能够减少语言带来的障碍，然而部分地方滥用国际化也会导致各种问题，更不符合设计理念。

- 国际化是面向普通玩家的，所以后台日志、F3 调试信息这样面向开发者的东西不应该添加国际化；
- 国际化只应该出现在客户端，不应当影响游戏的内部机制。切勿调用国际化来作为配置文件的键，甚至作为游戏注册的参考字符。

#### 特殊的语言文件格式

这是 Forge 为 Minecraft 添加的一个新的特性，任何带有 `#PARSE_ESCAPES` 注释的语言文件（注释单独出现在语言文件的任意一行均可），就会以 `Java Properties` 格式，`UTF-8` 编码进行读取。相比较于简单的 Minecraft 默认语言文件格式， `Java Properties` 格式具有更高的灵活性，能够多行书写，插入 Unicode 转义字符等等。

关于  `Java Properties` 格式，可以参考[此处](https://en.wikipedia.org/wiki/.properties)了解更多的特性。
