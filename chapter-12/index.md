## 特种模型

Minecraft 中有那么一些东西看上去并不是一般的方块模型能做到的。比如附魔台上的那本悬浮的书，你靠近的时候就一定会面朝你打开。再比如箱子有开关的动画效果。还有旗帜的图案。自然也包括所有实体的模型。  
这些模型有一个共同特点：它们都在某种意义上靠近底层。箱子、附魔台和旗帜使用了名为 `TileEntitySpecialRenderer` 的原版类（很多人直呼这个叫 `TESR`，本指南中也会大量使用这个称呼）。所有实体的模型则都基于名为 `Render` 的类。两者都直接使用 `GlStateManager` 来进行绘制。`GlStateManager` 中封装了大量使用 `GL11` 系列固定渲染管线的操作，这也是为什么说“它们靠近底层”的原因。

### 关于性能

是的，有一个很常见的说法是 TESR 对性能影响巨大。毕竟 TESR 和 TileEntity 的刷新频率是一样的（一秒最多 20 次），服务器上 TileEntity tick 一次，客户端这边对应的 `TESR`（如果有的话）也得重新绘制一次。这样的东西一多，后果自不必说。更何况 TESR 还挺常见的——比如原版箱子就有一个。堆满箱子的游戏后期存档应该是原版玩家的日常吧…… 固定管线的问题就不提了，因为提了也解决不了 Minecraft 现在还需要用这玩意的问题。

### Necessary Evil 和 Forge 的应对

但 TESR 的确有它存在的意义。箱子、末影箱、附魔台的动画效果自不必说，它们不可能用有限数量的方块状态枚举出来。还有一个应用场景是储物桶：很多 Mod 的储物桶都会在桶上渲染一个当前内容物的图标，这个更不可能只用方块状态枚举出来。它们必须“动态地”渲染出来，而非利用方块状态，将所有的可能性与一个固定的模型联系在一起。  
Forge 自然是有应对方案的：[`FastTESR`][ref-0710bdf3]。类如其名，一个比原版 TESR 快的 TESR。快的原理和它的处理方式有关：

>   \* TESRs can now be batched - look at TESR.renderTileEntityFast + TE.hasFastRenderer.  
> \- RainWarrior (fry)

简单来说，在 Forge patch 后的实现中，它用了一个独立的 `Tessellator` 用于存放渲染数据。`renderTileEntityFast` 的实际意义也只是将需要渲染的数据丢进那个 `Tessellator` 的 `BufferBuilder` 中，然后交由 `RenderGlobal` 一次性全部绘制。同样的理由，`FastTESR` 中的 `render` 方法有 `final` 修饰符，有 `abstract` 的是 `renderTileEntityFast`。  
这个思路实际上和原版的方块模型的渲染有点类似。

<!-- TODO 也许我们应该问六花把他的那个图直接贴这里，或者第六章的什么地方 -->

[ref-0710bdf3]: https://github.com/MinecraftForge/MinecraftForge/commit/0710bdf3f5a64e5fe1c725a30421b2c7523dca44
