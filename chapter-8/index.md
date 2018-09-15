## 状态效果与药水

首先从一个新的状态效果（亦称药水效果）开始：

```java
public class MyNewPotion extends Potion {
    public MyNewPotion(boolean isBad, int hexColor) {
        super(isBad, hexColor);
        // isBad 决定了这个效果是不是个“坏东西”，参考饥饿、中毒、反胃、迟缓、虚弱等。
        // hexColor 是写成一个 int 的 RGB。
    }
}
```

因为药水也是注册表管理的，所以我们用和方块、物品注册差不多的方式注册：

```java
@SubscribeEvent
public static void onPotionRegistration(RegistryEvent.Register<Potion> event) {
    event.getRegistry().registerAll(new MyNewPotion().setRegistryName("example_mod", "my_potion"));
}
```

### 给玩家加上

只是测试有没有注册成功的话，可以使用命令 `/effect <玩家名> example_mod:my_potion 1 60` 来获得等级 1，持续 1 分钟的你的效果。  
如果需要加入到相关的逻辑的话，你需要 `EntityLivingBase` 下的 `addPotionEffect`（`func_70690_d`）：

```java
int duration = 1200; // 这次单位是 tick
int amplifier = 0; // 0 代表等级 1
entityPlayer.addPotionEffect(new PotionEffect(myPotion, duration, amplifier));
```

### 图标

原版药水效果的图标是集中在一张纹理上的。原版默认的逻辑是根据 `Potion` 类下的 `setIconIndex`（`func_76399_b`）和 `getStatusIconIndex`（`func_76392_e`）来确定该用哪一个图标。其中，`getStatusIconIndex` 使用的 `int` 是 `x + (y * 8)` 的结果，x 和 y 自然是坐标。  
但不幸的是，这张纹理是定死的，准确地说这些图标其实都在玩家物品栏的那张纹理上。所以这两个方法实际上没什么卵用了。Forge 提供了一套解决方案：`renderInventoryEffect` 和 `renderHUDEffect`。前者负责玩家物品栏里的图标绘制；后者负责正常游戏时 HUD 中药水效果图标的绘制。使用方法如下：

```java
public class MyNewPotion extends Potion {
    public MyNewPotion(boolean isBad, int hexColor) {
        super(isBad, hexColor);
        // isBad 决定了这个效果是不是个“坏东西”，参考饥饿、中毒、反胃、迟缓、虚弱等。
        // hexColor 是写成一个 int 的 RGB。
    }

    // func_76400_d
    // 返回 false 时触发 Forge patch 后的逻辑，即调用 renderInventoryEffect 和 renderHUDEffect
    @Override
    public boolean hasStatusIcon() {
        return false;
    }

    @Override
    public void renderInventoryEffect(int x, int y, PotionEffect effect, Minecraft mc) {
        // 绘制逻辑
    }

    @Override
    public void renderHUDEffect(int x, int y, PotionEffect effect, Minecraft mc, float alpha) {
        // 绘制逻辑
    }
}
```
