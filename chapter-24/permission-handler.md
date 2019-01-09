## 实现 `IPermissionHandler`

是的，权限的注册和检查最终都是它来完成的。只需要正确实现它并通过 `PermissionAPI` 覆盖就可以影响权限检查的行为。

### 但我需要实现 `IPermissionHandler` 吗？

很显然，`IPermissionHandler` 其实是用来实现更高级的权限管理的。想想 FTBUtils、LuckPerms、PermissionEX 的功能你就会明白了。如果你只需要支持权限控制，只需要在合适的时候调用 `PermissionAPI.hasPermission`，然后让用户使用某种专业的权限管理工具即可。即便不使用专业的管理工具，默认的基于 OP 概念的 `IPermissionHandler` 实现也够用了。

### 装饰器模式

虽说任何 Mod 都可以覆盖当前生效的 `IPermissionHandler`，但把自己不能处理的行为代理给之前的 `IPermissionHandler` 总不是件坏事。当然，不走装饰器模式也无妨——这样你可以接管所有的权限控制。

```java
public class MyPermissionHandler implements IPermissionHandler {

    private final IPermissionHandler parent;

    public MyPermissionHandler(IPermissionHandler parent) {
        this.parent = parent;
        // 其他初始化操作
    }
    // TODO Delegated methods
}
```

### 设定当前 `IPermissionHandler`

`IPermissionHandler` 必须在 `FMLPreInitializationEvent` 发布或更早的时候配置到位。在此之后尝试设定 `IPermissionHandler` 会报错。

```java
@Mod.EventHandler
public void onPreInit(FMLPreInitializationEvent event) {
    PermissionAPI.setPermissionHandler(new MyPermissionHandler(...));
}
```
