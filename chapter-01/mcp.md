# MCP

首先我们需要回答一个问题：

> Minecraft 是一个商业软件，诚然它是 Java 写的，没有人能阻止某个人对其进行逆向工程，但我们为什么还能看到未经混淆的代码？甚至还能对着这玩意写 Mod？这不可能是 Mojang 干出来的事情。

事实上 Mojang 的确从未放出过 Minecraft 的真正源码（即便是[在 19w36a 发布时宣布放出含有原始类名、方法名和字段名的 ProGuard 的混淆记录][ref-19w36a]），所以首先我们要明确一个事实：我们一直在讨论的并不是 Minecraft 源码的真正形态，毕竟只有 Mojang 员工才能看到这种商业机密嘛。直接对 Minecraft 的 jar 进行反编译也只能得到混淆后的反编译结果。它往往长这样：

[ref-19w36a]: https://www.minecraft.net/en-us/article/minecraft-snapshot-19w36a

```java
// 并非 Minecraft 本体反编译结果，仅供说明使用
public class a extends b {
    private c c1;
    private final d d1;
    public a(e par1, f par2) {
        this.d1 = g(par1);
        this.c1 = h(par2)
    }

    private d g(e par1) {
        return ...;
    }

    public c h(f par1) {
        return ...;
    }
}
```

我们当然可以直接对着这个写 Mod，但没有人会这么做，因为这玩意的可读性是无穷小。<!-- 0.999... = 1 -->  
但有那么一些眼尖的人忽然注意到，某些地方（比如 `toString` 的实现、异常信息、本地化键、享元对象注册名及其他各种字符串常量）仿佛在暗示这些被混淆的类的真实身份。我们可以先根据这些提示来确定一部分类的名称。然后，对于那些没有明显提示的混淆名，我们可以想象我们是 Mojang 员工，正在写 Minecraft，换做是我们又会怎么给这个类/字段/方法/方法参数命名。毕竟，虽然名字混淆了，但逻辑还在那里。如果我们就这样顺藤摸瓜逐个人力翻译下去会发生什么？  
MCP（Mod Coder Pack）就是这样诞生的——很久<!-- TODO 多久？ -->之前，有一帮先驱者们率先开始了对 Minecraft 这个商业软件的人力反混淆工程。最终的成果便是一套完整的反混淆映射表（“mapping”），通称 MCP Mapping。它代表了一票社区贡献者站在局外人的视角上对 Minecraft 底层代码的理解<black>虽然参与人力反混淆的人并不多</black>。说得夸张一点，我们甚至可以认为映射表是猜出来的（当然，事实上是基于现有线索推理出来的）。  

显然，MCP 给出的也只是一套“参考解读”。MCP 的解读代表的是 Minecraft 当前底层的现状，不代表真实情况，更不代表未来的变化。正因为此，MCP 的理解有可能与 Mojang 员工实际想表达的意思有偏差，换言之 Mojang 员工们实际使用的名称可能和 MCP 大相径庭。与此同时，这也意味着 MCP 给出的名称是有可能比 Mojang 员工使用的名称更准确的。

## MCP 的版本号

本指南使用的是适用于 1.12.+ 的 `stable_39` MCP Mapping，其中 `stable_39` 是这个映射表的版本号。  
MCP 分两种版本：稳定版（`stable`）和快照版（`snapshot`）。  
稳定版通常对于一个特定版本的 Minecraft 只有一个，通常在 MCP 升级目标 Minecraft 版本之前发布。例如 MCP 在发布适用于 Minecraft 1.12.x 的 `stable_39` 在 2018 年 8 月 13 日发布之后，开始更新适用于 1.13 的快照版本 MCP。稳定版 MCP 代表了一套稳定的对 Minecraft 底层的解读，在短时间内不会发生变化，这也是为什么本指南使用 `stable_39` 的原因。  
快照版则是美国东海岸时间每天凌晨三点（对应北京时间当日下午三点，有夏令时则是当日下午四点）定时发布，囊括当前 MCP 所有最新的变化~~，自然地对于 Modder 来说相当于三天两头 breaking change~~。快照版的版本号以日期命名，比如在 `stable_39` 发布之前最后一个适用于 Minecraft 1.12.x 的 MCP 快照版本也是在 2018 年 8 月 13 号发布的，它的版本号是 `snapshot_20180813`。

## 不可避免的二进制不兼容更新与 SRG 名

前文说到 MCP 有版本号。为什么会有一个版本号？很明显，因为是社区对 Minecraft 底层的理解，某个人提出的第一个名称并不一定就是最准确的，因此 Mapping 不可避免地需要更新，以更好地反映 Minecraft 的底层逻辑。  
但这样一来马上就会出现 binary compatibility 的问题——任何名字都可以变来变去，那我们岂不是三天两头就要 breaking change 一次？  
虽然没有直接证据，但 Modder 们普遍认为上述问题正是 MCP 引入两种不同的映射名的理由：

  - 形如 `func_12345_a`、`field_67890_b` 的 SRG 名。它的特点是“对于某个签名没有变化的方法或字段来说，其 SRG 名会始终保持一致”。
    - 根据 Lex 的说法，SRG 是 [Searge's][ref-searge] [RetroGuard][ref-retroguard] 的缩写，而 [RetroGuard][ref-retroguard] 是一个历史悠久的 Java 混淆兼优化工具。
    - Searge 作为 MCP 的创始人，在原版 RetroGuard 的基础上进行了些许修改，[形成了一个 RetroGuard 的 fork][ref-searge-retroguard]，用于逆向 Minecraft 本身的混淆。
    - 没错，Searge 现在是 Mojang 员工，负责 Java 版 Minecraft 的开发。有鉴于 Searge 本人的知名度，有相当多的社区成员认为 SRG 其实是 SeaRGe 的缩写，即 Searge 这个名字去掉所有元音字母的结果。
    <!-- TODO 我们需要进一步查证 SRG 的真实含义。 -->
  - 有特定含义，一般情况下都是英文词组的 MCP 名，方便用于实际的 Mod 开发。
  - 虽然不是映射名，但一般都会把某个 SRG 名或 MCP 名对应的混淆名作“Notch 名”，以 Minecraft 的创始人 Notch 命名。

[ref-searge]: https://minecraft.gamepedia.com/Searge
[ref-retroguard]: https://web.archive.org/web/20051028230519/http://www.retrologic.com/retroguard-related.html
[ref-searge-retroguard]: https://github.com/ModCoderPack/Retroguard

下表给出了基于 `stable_39` 的 Notch-SRG-MCP 名对照表，可以注意到类名没有独立存在的 SRG 名：

| Notch (1.12) | SRG                                            | MCP                                      |
| :----------- | :--------------------------------------------- | :--------------------------------------- |
| `bhz`        | `net.minecraft.client.Minecraft`               | `net.minecraft.client.Minecraft`         |
| `bhz.a`      | `net.minecraft.client.Minecraft.func_99999_d`  | `net.minecraft.client.Minecraft.run`     |
| `bhz.C`      | `net.minecraft.client.Minecraft.field_71425_J` | `net.minecraft.client.Minecraft.running` |

换言之，MCP 保证“不论 MCP 名怎么改来改去，SRG 名都不会有变动，哪怕这个字段或方法最终因为 Minecraft 版本更新消失了都不会重新回收利用”。

## 跨版本匹配 SRG 名

同时，MCP 还尽力将不同版本中确定是相同方法的不同 Notch 名映射到同一个 SRG 名上去。下面五个名字指代的是同一个方法：

| MCP                                  | SRG                                           | 1.12 Notch | 1.11.2 Notch | 1.10 Notch |
| :----------------------------------- | :-------------------------------------------- | :--------- | :----------- | :--------- |
| `net.minecraft.client.Minecraft.run` | `net.minecraft.client.Minecraft.func_99999_d` | `bhz.a`    | `bes.a`      | `bcx.a`    |

确定两个不同版本的 Minecraft 中 Notch 名不同的两个方法/字段是不是同一个的依据是 descriptor。以 `WorldGenerator.geneate` 为例（为简明起见，这里不使用完整的 method descriptor，改用模仿 Java 中的方法声明的方式描述 method descriptor）：

| 游戏版本 |MCP       |SRG            |Method Descriptor                                       |
| :------ |:------   |:------        |:------                                                 |
| 1.7.10  |`generate`|`func_76484_a` |`void (World world, Random random, int x, int y, int z)`|
| 1.8     |`generate`|`func_180709_b`|`void (World world, Random random, BlockPos pos)`       |
| 1.12    |`generate`|`func_180709_b`|`void (World world, Random random, BlockPos pos)`       |

注意到 1.7.10 到 1.8 之间这个方法的参数发生了变化（三个 int 表示的坐标合并入 `BlockPos`），因此 `func_76484` 这个 SRG 名并没有被 1.8 中 MCP 名相同的方法继承下来，而是一个全新的方法 `func_180709_b` 获得了一样的 MCP 名。

## <ruby>重<rt>chóng</rt></ruby>混淆（Re-obfuscation，reobf）

这样一来，只需要 Mod 在最终编译时重新“混淆”回 SRG 名，就可以在一定程度上抵抗 MCP 更新引发的二进制不兼容问题，因为使用不会变化的 SRG 名保证了二进制兼容。这个过程通称“重混淆”（re-obfuscation，缩写 reobf）。  
在开发环境时我们可以继续使用 MCP 名以方便开发，但很不幸，MCP 名三天两头可能会有变动的问题最终需要 Modder 硬扛。唯一值得庆幸的事情是，MCP 是有稳定版发布的——别忘了，本教程使用的 MCP 版本叫 `stable_39`，那个 `stable` 便代表稳定版。通常，对于一个 Minecraft 大版本来说，只会有一个稳定版的 MCP 发布，此后的版本都会是针对下一个大版本的 Minecraft 的。这也是为什么本教程直接使用 `stable_39` 的原因。

## 但这还是逆向工程啊！

是的，这是逆向工程，所以 MCP 这样的工程仍然落在一个灰色偏黑的地带中。之所以 MCP 这样的工程能活到现在，还是要拜 Mojang 不表态的态度所赐。毕竟 Mojang 也清楚 User-Generated Content（UGC）是 Minecraft 成功的关键一环。
