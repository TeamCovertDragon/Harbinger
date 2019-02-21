## Forge 权限系统

是的。Forge 内置了一个非常简易的权限系统。有鉴于 Forge 的一贯原则，它只是一套有完整功能的 API 而已，并不影响游戏功能，甚至它自带的占位用实现都是假定 OP 有所有权限的（参考原版命令系统）。  
虽然说简易，但有鉴于这套系统是基于权限名、玩家和上下文（`IContext`）的，所以它还是比较 versatile 的。

### 基于上下文的权限查询

```java
String permissionKey = "a.random.permission.key";
if (PermissionAPI.hasPermission(player.getGameProfile(), permissionKey, new PlayerContext(player))) {
    // 在有权限的时候执行操作
}
```

`PermissionAPI.hasPermission` 会将这个调用代理给当前的 `IPermissionHandler.hasPermission`。无需关心接口的实现，只要它不是 `null` 就好。

### 权限注册

```java
String permissionKey = "a.random.permission.key";
PermissionAPI.registerNode(permissionKey, DefaultPermissionLevel.OP, "My first permission");
```

最后一个参数是描述，可选。中间的参数实际上是给这套系统的默认实现用的，目的是尽可能贴近原版行为（顺带让 `PermissionAPI` 持有的 `IPermissionHandler` 引用永远不是 `null`）。
