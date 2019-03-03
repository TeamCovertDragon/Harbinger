## 矿石生成

实际上我们没有必要为了生成矿石重新写一个 `WorldGenerator`——原版自己就有一个 `WorldGenMinable` 可用，用它就可以，而且自带对 cascading world gen 的处理，不需要你管了。当然了，面对特殊需求的话自然还是需要重新写一个的，不过那样的情况其实并不多见。  
但是，如前文所说，`WorldGenerator` 和世界生成其实没有什么联系——那我们在哪生成矿石？  

答案是一个叫 `OreGenEvent` 的事件。

```java
// 我们这里使用 OreGenEvent.Post 这个版本。它在所有矿石生成结束之后会发布。
@SubscribeEvent
public static void onOreGen(OreGenEvent.Post event) {
    // WIP
}
```

### 干涉矿石生成

`OreGenEvent` 除了 `OreGenEvent.Post` 外，还有另外两个变种：

  * `OreGenEvent.Pre`，不可取消，无结果，在原版矿石生成逻辑执行前发布，你可以在这里生成点别的矿石。
  * `OreGenEvent.GenerateMinable`，不可取消，有结果，在每一种原版矿石生成之前会发布，设定结果为 `Event.Result.DENY` 即可阻止对应的矿石生成。具体的待生成矿石的类型由 `OreGenEvent.GenerateMinable.EventType` 这个 enum 给出。

订阅这两个事件可以干涉原版的矿石生成。若某个 Mod 也发布了这些事件，你甚至可以干涉其他 Mod 的矿石生成。
