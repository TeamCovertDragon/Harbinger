## 文本组件（`ITextComponent`）

Minecraft 其实是有一点富文本（Rich text）的概念的：它支持粗体、斜体、下划线、删除线、十六种颜色、本地化、甚至点击特定文本后的回调。这些都是通过名为文本组件（`ITextComponent`）的机制完成的。

### 应用场景：需要翻译的左下角聊天窗口提示

开发 Mod 的时候难免会遇到需要给玩家发送聊天消息的需求。但如果这个消息需要翻译怎么办？有鉴于玩家消息本质上就是文本组件，那么我们用 `TextComponentTranslation` 就好了。

```java
// 构造器里把 Translation Key 放进去即可。
player.sendMessage(new TextComponentTranslation("a.translation.key"));
```

或者，高端一点的可以加点特技：

```java
player.sendMessage(new TextComponentTranslation("a.translation.key")
    .setStyle(new Style()
        .setHoverEvent(new HoverEvent(HoverEvent.Action.SHOW_TEXT, new TextComponentTranslation("another.translation.key")))
    )
);
```
