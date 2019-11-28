# Capability

让我们再来看一下 `TileEntity`：我们可以给它附加诸如存储物品等等的功能，具体使用的时候只需要转型就可以了：

```java
// 假想的情景，不要照抄
TileEntity tile = world.getTileEntity(pos);
if (tile instanceof Something) { // 假定这个 TileEntity 实现了名为 Something 的接口
    ((Something)tile).interact(...);
}
```

但不同的功能需要的接口也不尽相同，这些接口自然是没办法统一的。我们可能会写出这种东西：

```java
if (tile instanceof Something) {
    // …
} else if (tile instanceof AnotherThing) {
    // …
} else if (tile instanceof SomethingElse) {
    // …
} else {
    // …
}
```

不难发现：我们总是在检查某个 `TileEntity` 是否支持某种功能（以 `instanceof` 的形式呈现）。类似的情况实际上也出现在了 `ItemStack`、`Entity` 甚至是 `World` 和 `Chunk` 上。我们希望给这些东西追加一些功能，并且希望别人能通过指定的接口使用这些功能。但是新的问题也浮出水面：

```java
// 如果 Something 并不存在会发生什么？
if (tile instanceof Something) {
    // …
}
```

若 `Something` 是来自别的 Mod 的接口，而你希望你的 Mod 不依赖别的 Mod 就能运行，上面这样的写法，一般来说就行不通了。  
有办法解决这个问题吗？
让我们再审视一遍需求：“为游戏对象扩展新功能，并允许其他人使用”。也就是说，只要我们能让其他人拿到这些功能对应的接口就可以了。我们能不能这么做：

```java
if (tile.hasFeature("Something")) {
    Something sth = tile.getFeature("Something");
}
```

这便是 Capability 系统存在的理由：
将功能转化为可反复使用的零件，用作某一个更大的“实体”（可以是狭义上的实体、TileEntity 甚至是物品）的基础，同时避免过强的耦合。

## `Capability<?>`：Token

上文中我们写下了这么一段代码：

```java
if (tile.hasFeature("Something")) {
    Something sth = tile.getFeature("Something");
}
```

在 Capability 系统中，它实际上是这样的：

```java
if (tile.hasCapability(CapabilitySomething.CAP_TOKEN, EnumFacing.EAST)) {
    Something sth = tile.getCapability(CapabilitySomething.CAP_TOKEN, EnumFacing.EAST);
}
```

我们通过一个 `CapabilitySomething.CAP_TOKEN` 来标记我们需要的功能，或者说，这是我们“能力”（“capability”）的标记（token）。
它的类型是 `Capability<Somthing>`。`Something` 便是实际功能的接口。  
有了这样一个标记，我们就能从其他地方查询并请求这个功能的接口。这个“其他地方”指的是实现了 `ICapabilityProvider` 的类的实例。

## `ICapabilityProvider`：不同功能的容器

`ICapabilityProvider` 便是各种不同功能的实现整合在一起的结果。
你可以问它“支持不支持 xxx”（`hasCapability`），也可以问它“要 xxx 功能的接口”（`getCapability`）。  
在 Forge patch 过若干原版类后，这些原版类实现了 `ICapabilityProvider`，意味着我们可以[通过某种方式](attach-cap.md)给它们追加功能。这些实现了 `ICapabilityProvider` 的类包括但不限于：

  - `TileEntity`
  - `Entity`
  - `ItemStack`
  - `Village`
  - `World`
