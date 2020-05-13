# 动画状态机（Animation State Machine）

WIP

## 动画状态机定义文件

<!-- fry (RainWarrior)'s legacy - https://gist.github.com/RainWarrior/964ed4692f4da1fd4964 -->

WIP

## Armature 定义文件

WIP

## 使用

```java
public class CommonProxy {
    public void loadAnimationStateMachine(ResourceLocation location, ImmutableMap<String, ITimeValue> params) {
        return null;
    }
}

public class ClientProxy {
    @Override
    public void loadAnimationStateMachine(ResourceLocation location, ImmutableMap<String, ITimeValue> params) {
        return ModelLoaderRegistry.loadASM(location, params);
    }
}
```

有鉴于 [Capability](../../chapter-27/built-in/animation.md) 的使用，动画状态机可用于：

  - 方块（借助 `TileEntity`）
  - 物品（借助 `ItemStack` 和 `Item.initCapabilities`）
  - 实体（借助 `Entity` 和 `AnimationModelBase`）

具体的使用方式和其他基于 Capability 的接口大同小异。有鉴于这个功能只在物理客户端上有意义，使用它时也有一些小细节要注意。

### 应用于 TileEntity

WIP

### 应用于物品

WIP

```java
public final class ItemWithAnimatingModel extends Item {
    public ICapabilityProvider initCapabilities(ItemStack item, NBTTagCompound data) {
        return new DefaultItemAnimationCapabilityProvider(proxy.loadAnimationStateMachine(...));
    }
}
```

### 应用于实体

WIP
