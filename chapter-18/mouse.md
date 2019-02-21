## 鼠标输入

因为 Minecraft 是基于 LWJGL 的，所以 Minecraft 也是通过 LWJGL 的 `org.lwjgl.input.Mouse` 来获得相关信息的。然而不幸的是 Minecraft 把相关的逻辑全部写死在了 `Minecraft.runTickMouse`（`func_184124_aB`）这个私有方法里，所以理论上我们什么都做不了。

### 事件

但实际上 Forge patch 过了这个方法——甚至这里有两个事件可以用：一个是 Forge 的 `MouseEvent`，另一个是 FML 的 `InputEvent.MouseInputEvent`。`MouseEvent` 比 `InputEvent.MouseInputEvent` 发布得早。  
为什么会有两个事件？如果你看过这两个事件的源码的话，你会发现这两个事件基本没有人关注——换言之，这可能是一个历史遗留问题。

#### `MouseEvent`

一个可以取消的事件，在这里你能拿到鼠标的坐标、相对于上一次 polling 的坐标变化量、滚轮滚动的 delta、左中右键的情况（如果有）。它发布的时间是原版执行 `runTickMouse` 所有具体逻辑之前，取消掉它即可让原版逻辑完全不执行。

```java
// 经实测，这样可以让实际游戏时左、中、右键完全失效。
@SubscribeEvent
public static void onMousePolling(MouseEvent event) {
    event.setCanceled(true);
}
```

#### `InputEvent.MouseInputEvent`

一个不可以取消的事件，在整个 `runTickMouse` 逻辑执行完之后会发布，没有任何有用信息，相当于是一个 Callback。但你仍然能通过 LWJGL 的 `Mouse` 类拿到相关信息。

### GUI

在 `GuiScreen` 类下有一个名为 `handleMouseInput`（`func_146274_d`）的方法可以用来处理当前 GUI 中用户的键盘输入。但实际上通常覆写的方法是同在 `GuiScreen` 类下的 `mouseClicked`（`func_73864_a`），因为 `handleMouseInput` 会在确认输入有效之后调用它：

```java
@Override
public void mouseClicked(int mouseX, int mouseY, int mouseButton) {
    // 处理具体逻辑
}
```

大量 `Gui` 类的派生类下都有一个名为 `mouseClicked` 的方法，但这些方法接受的参数不尽相同。大部分 `mouseClicked` 都会接受三个 `int`，分别代表鼠标光标 X、Y 和按下去的键（左中右键）。如果你要处理鼠标输入的地方是你的 GUI，大可以在这里处理。

除此之外还有名为 `mouseReleased` 和 `mouseClickMove` 的方法。顾名思义，前者适用于鼠标按键松开的情况，后者则是在拖拽光标的时候会调用。

#### 事件

甚至 Forge 在这里都 patch 进去两个事件：`GuiScreenEvent.MouseInputEvent.Pre` 和 `GuiScreenEvent.MouseInputEvent.Post`。取消 `GuiScreenEvent.MouseInputEvent.Pre` 可以阻止目标 `GuiScreen` 的 `handleMouseInput` 继续执行原版逻辑，而 `GuiScreenEvent.MouseInputEvent.Post` 仅是作为逻辑执行结束后的 Callback 存在。
