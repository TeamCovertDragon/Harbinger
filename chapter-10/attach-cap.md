# 追加 Capability

为 `ItemStack`、`Entity` 这样的对象追加新的功能并不是通过 `ICapabilityProvider` 完成的，而是一个事件：`AttachCapabilitiesEvent<? extends ICapabilityProvider>`。

## 组合模式（Composite Pattern）与 `AttachCapabilitiesEvent`

Forge 在 patch 上述原版类时还会让它们在初始化时发布一个名为 `AttachCapabilitiesEvent` 的事件，通过这个事件收集其他 Mod 为这些对象追加的新功能。

```java
@SubscribeEvent
public static void addCap(AttachCapabilitiesEvent<Entity> event) {
    if (event.getObject() instanceof EntityPlayer) {
        event.addCapability(new ResourceLocation("my_mod", "extra_inv"), new ICapabilityProvider() {
            @Override
            public boolean hasCapability(Capability<?> cap, EnumFacing facing) {
                return cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY;
            }
            @Override
            public <T> T getCapability(Capability<T> cap, EnumFacing facing) {
                if (cap == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY) {
                    return  CapabilityItemHandler.ITEM_HANDLER_CAPABILITY.cast(...);
                } else {
                    return null;
                }
            }
        })
    }
}
```

等一下，那个 `ResourceLocation` 和 `ICapabilityProvider` 是怎么回事？不是说这是“不同功能的实现整合在一起的结果”吗？为什么我们还要实现 `ICapabilityProvider`？而且是实现了一个只提供一种功能（`IItemHandler`）的 `ICapabilityProvider`？

很明显，订阅这个事件的人很有可能不止一个，但是这些新加的功能最终需要整合到一起。我们需要：

  - 一个特殊的 `getCapability`，它遍历所有 Mod 追加的新功能并返回合适的接口。
  - 一个特殊的 `hasCapability`，它遍历所有 Mod 追加的新功能并在找到对应的实现时返回 `true`，否则返回 `false`。

听起来就像套娃一样，不是吗？有请 `CapabilityDispatcher`。  
`CapabilityDispatcher` 便是这样一个将若干 `ICapabilityProvider` 整合在一起的 `ICapabilityProvider`。为了区分来源，每一个 `ICapabilityProvider` 都有名字，也就是那个 `ResourceLocation`。在方便区分来源的同时，也为保存数据提供了方便。
