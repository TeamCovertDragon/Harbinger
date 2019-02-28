## 相关事件

有几个物理客户端上才会发布的事件和某些实体模型有关系。

### `RenderLivingEvent`

渲染某个 `EntityLiving` 时会发布的事件，有四个版本 ：

  - `RenderLivingEvent.Pre`：可取消，无结果，取消后可直接接管该实体的渲染。
  - `RenderLivingEvent.Post`：不可取消，无结果，可用于追加渲染一些东西。
  - `RenderLivingEvent.Specials.Pre`，可取消，无结果，在 `RenderLivingBase.renderName`（`func_177067_a`）被调用时首先发布，取消后可直接接管有关逻辑。可能是历史遗留，因为取消 `RenderLivingEvent.Pre` 后这个事件也不会发布。这里不推荐使用该事件。
  - `RenderLivingEvent.Specials.Post`，可取消，无结果。在 `RenderLivingBase.renderName`（`func_177067_a`）返回前发布，可用于追加渲染一些东西。可能是历史遗留，因为它发布之后会有 `RenderLivingEvent.Post` 可用。这里不推荐使用该事件。

### `RenderPlayerEvent`

渲染玩家实体相关的事件。也有四个版本：

  - `RenderPlayerEvent.Pre`：可取消，无结果，取消后可直接接管该玩家的渲染。
  - `RenderPlayerEvent.Post`：不可取消，无结果，可用于追加渲染一些东西。
  - ~~`RenderPlayerEvent.Specials.Pre`，已被废弃，且该事件没有在任何地方发布过。~~
  - ~~`RenderPlayerEvent.Specials.Post`，已被废弃，且该事件没有在任何地方发布过。~~

是的，和上面的 `RenderLivingEvent` 完全对应。但渲染玩家的时候只有 `RenderPlayerEvent` 会发布。`RenderLivingEvent` 只在渲染原版非玩家的生物实体时发布。

### `RenderItemInFrameEvent`

渲染物品展示框里的物品之前会发布这个事件，取消后即可直接接管当前的渲染。可以用来实现类似原版地图在物品展示框里的渲染效果。
