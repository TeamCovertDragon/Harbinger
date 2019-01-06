## 实现 `IPermissionHandler`

是的，权限的注册和检查最终都是它来完成的。只需要正确实现它并通过 `PermissionAPI` 覆盖就可以影响权限检查的行为。

### 装饰器模式

虽说任何 Mod 都可以覆盖当前生效的 `IPermissionHandler`，但把自己不能处理的行为代理给之前的 `IPermissionHandler` 总不是件坏事。毕竟，直接返回 `false` 或 no-op 显得太过武断了。

```java
public class MyPermissionHandler implements IPermissionHandler {
    // TODO Delegated methods
}
```
