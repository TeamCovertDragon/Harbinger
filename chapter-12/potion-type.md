## 药水

原版的药水类型（`PotionType`）和状态效果（`Potion`，`PotionEffect`）之间并不是完全一一对应的，比如原版的水瓶实际上也是药水瓶——只是它的药水类型是“水”。类似地，“平凡的药水”、“粗制的药水”、“浑浊的药水”这三个也是独立存在的 `PotionType`。
更准确地说，原版的那个药水瓶能装的药水类型有它自己独立的注册表。自然地，Forge 也接管了这个注册表。

```java
@SubscribeEvent
public static void potionTypeRegistration(RegistryEvent.Register<PotionType> event) {
    // 注册
}
```
