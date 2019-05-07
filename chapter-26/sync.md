# 同步配置文件

要实现在玩家通过 GUI 修改之后立刻同步配置文件很简单，监听 `ConfigChangedEvent.OnConfigChangedEvent` 并调用 `ConfigManager::sync` 即可。

在 MyModConfig.java 中添加：

```java
@Mod.EventBusSubscriber(modid = YOUR_MODID)
public static class ConfigSyncHandler {
    @SubscribeEvent
    public static void onConfigChanged(ConfigChangedEvent.OnConfigChangedEvent event) {
        if (event.getModID().equals(YOUR_MODID)) {
            ConfigManager.sync(YOUR_MODID, Config.Type.INSTANCE);
        }
    }
}
```
