## 键盘输入

因为 Minecraft 是基于 LWJGL 的，所以 Minecraft 也是通过 LWJGL 的 `org.lwjgl.input.Keyboard` 来获得相关信息的。然而不幸的是 Minecraft 把相关的逻辑全部写死在了 `Minecraft.runTickKeyboard`（`func_184118_az`）这个私有方法里，所以理论上我们什么都做不了。

### 事件：`InputEvent.KeyboardInputEvent`

但 FML 在 `runTickKeyboard` 的结尾处，处理原版快捷键之前 patch 进去一个事件——`InputEvent.KeyboardInputEvent`。通过它我们就能做一些有趣的事情了。

```java
@SubscribeEvent
public static void onKeyPressed(InputEvent.KeyboardInputEvent event) {
    // 这个事件不能取消，也没有 Result，仅是一个 Callback 的存在。
}
```

### 快捷键（KeyBinding）

`InputEvent.KeyboardInputEvent` 最典型的用途就是 Mod 们的自定义快捷键。  
Minecraft 有一个专门负责快捷键的类叫 `KeyBinding`，但 Minecraft 把快捷键处理写死在了 `Minecraft` 类下的一个方法里，所以我们只能通过 `InputEvent.KeyboardInputEvent` 来处理我们自己的 `KeyBinding` 了。  
除此之外，Forge 还给 `KeyBinding` patch 进去了一个非常简单的组合键的支持。

```java
import net.minecraftforge.fml.client.registry.ClientRegistry;

/*
 * 参数依次是 String description, IKeyConflictContext context, KeyModifier modifier, int key, String category。
 * - description 是快捷键名称的本地化键。
 * - context 是 Forge patch 进去的，用于决定快捷键在什么情况下和别的快捷键冲突。
 *   Forge 提供了三个实现，即 KeyConflictContext 这个 enum 下的三个值：UNIVERSAL、
 *   IN_GAME 和 GUI，分别代表“全局适用”，“只在实际游戏中，但没有打开 GUI 时适用”和“只在打
 *   开 GUI 时适用”。如此一来，如果有两个快捷键设定的按键是一样的，但其中一个 context 是 GUI，
 *   另一个是 IN_GAME，那么这两个快捷键就不会被看作是互相冲突的。
 * - modifier 用于提供非常基础的组合键支持，只有四种可能：CTRL、ALT、SHIFT 和 NONE。
 *   NONE 代表原版风格的没有组合键，其他三种都代表组合键支持。不支持三种及以上的组合键。
 *   对于 CTRL，Forge 能正确将其处理为 macOS 上的 Command 键。CTRL、ALT 和 SHIFT 都不
 *   对左右作区分。
 *   举例：这里我们指定了 MY_HOTKEY 的默认键位是 CTRL+K，在 macOS 上则是 CMD+K。
 * - key 参考 Keyboard 类下的常量字段们。
 * - category 原版按键设定中把按键们分成了若干类别，就是这个了。
 *   传入的字符串也充当该类别的本地化键。
 */
public static final KeyBinding MY_HOTKEY = new KeyBinding("key.my_mod.hotkey_1", KeyConflictContext.IN_GAME, KeyModifier.CONTROL, Keyboard.KEY_K, "key.category.mymod");

// 注册快捷键。
// 没有调用的时间限制，但建议在 FMLInitializationEvent 发布时调用。
public static void init() {
    ClientRegistry.registerKeyBinding(MY_HOTKEY);
}

@SubscribeEvent
public static void onKeyPressed(InputEvent.KeyboardInputEvent event) {
    if (MY_HOTKEY.isPressed()) {
        // 处理快捷键逻辑
        // 或者 sendPacketToServer（见第十一章）
    }
}
```

有一点需要注意，因为键盘输入是完全发生在玩家的客户端上的，所以如果你希望逻辑服务器能得知玩家按下了某个按键并以此做出响应，需要客户端向服务器发送数据包。关于发包相关的内容可参考第十一章中 [`SimpleNetworkWrapper`](../chapter-11/forge-extension/simple-network-wrapper.md) 和 [`FMLEventChannel`](../chapter-11/forge-extension/fml-event-channel.md) 两章的内容。

### GUI

在 `GuiScreen` 类下有一个名为 `handleKeyboardInput`（`func_146282_l`）的方法可以用来处理当前 GUI 中用户的键盘输入。但实际上通常覆写的方法是同在 `GuiScreen` 类下的 `keyTyped`（`func_73869_a`），因为 `handleKeyboardInput` 会在确认输入有效之后调用它：

```java
// keyId 的值的含义可参考 LWJGL 的 Keyboard 类下的常量字段。
@Override
public void keyTyped(char inputChar, int keyId) {

}
```

#### 事件

甚至 Forge 在这里都 patch 进去两个事件：`GuiScreenEvent.KeyboardInputEvent.Pre` 和 `GuiScreenEvent.KeyboardInputEvent.Post`。取消 `GuiScreenEvent.KeyboardInputEvent.Pre` 可以阻止目标 `GuiScreen` 的 `handleKeyboardInput` 继续执行原版逻辑，而 `GuiScreenEvent.KeyboardInputEvent.Post` 仅是作为逻辑执行结束后的 Callback 存在。
