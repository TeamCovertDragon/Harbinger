## 动画状态机（Animation State Machine）

WIP

### 动画状态机定义文件

<!-- fry (RainWarrior)'s legacy - https://gist.github.com/RainWarrior/964ed4692f4da1fd4964 -->

WIP

### Armature 定义文件

WIP

### 使用

有鉴于 [Capability](../../chapter-27/built-in/animation.md) 的使用，动画状态机可用于 TileEntity、物品（ItemStack）和实体上。关于 Capability 系统的细节在第二十七章有详细讨论，这里暂不作具体说明。

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

#### 应用于 TileEntity

#### 应用于物品

#### 应用于实体
