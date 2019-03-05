## 状态效果

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

因为状态效果也是注册表管理的，所以我们用和方块、物品注册差不多的方式注册：

```java
@SubscribeEvent
public static void onPotionRegistration(RegistryEvent.Register<Potion> event) {
    event.getRegistry().registerAll(new MyNewPotion().setRegistryName("example_mod", "my_potion"));
}
```

### 给玩家加上

只是测试有没有注册成功的话，可以使用命令 `/effect <玩家名> example_mod:my_potion 1 60` 来获得等级 1，持续 60 秒的你的效果。  
如果需要加入到相关的逻辑的话，你需要 `EntityLivingBase` 下的 `addPotionEffect`（`func_70690_d`）：

```java
int duration = 1200; // 这次单位是 tick
int amplifier = 0; // 0 代表等级 1
entityPlayer.addPotionEffect(new PotionEffect(myPotion, duration, amplifier));
```

### 图标

原版药水效果的图标是集中在一张纹理上的。原版默认的逻辑是根据 `Potion` 类下的 `setIconIndex`（`func_76399_b`）和 `getStatusIconIndex`（`func_76392_e`）来确定该用哪一个图标。其中，`getStatusIconIndex` 使用的 `int` 是 `x + (y * 8)` 的结果，其中 x 和 y 是纹理上的坐标（u、v），需要通过 `index % 8` 和 `index / 8` 转换回来。  
但不幸的是，这张纹理是定死的，准确地说这些图标其实都在玩家物品栏的那张纹理上。所以这两个方法实际上没什么卵用了。Forge 提供了一套解决方案：`renderInventoryEffect` 和 `renderHUDEffect`。前者负责玩家物品栏里的图标绘制；后者负责正常游戏时 HUD 中药水效果图标的绘制。使用方法如下：

```java
public class MyNewPotion extends Potion {
    public MyNewPotion(boolean isBad, int hexColor) {
        super(isBad, hexColor);
    }

    // func_76400_d
    // 返回 false 时触发 Forge patch 后的逻辑，即调用 renderInventoryEffect 和 renderHUDEffect
    @Override
    public boolean hasStatusIcon() {
        return false;
    }

    @Override
    public void renderInventoryEffect(int x, int y, PotionEffect effect, Minecraft mc) {
        // 绘制逻辑，可直接交给 renderHUDEffect，此时 alpha = 1F
        this.renderHUDEffect(x, y, effect, mc, 1F);
    }

    @Override
    public void renderHUDEffect(int x, int y, PotionEffect effect, Minecraft mc, float alpha) {
        // 绘制逻辑
        mc.getTextureManager().bindTexture(textureResourceLocation);
        // func_146110_a
        // x, y 为绘制的起点，u, v 为纹理的起点，w, h 为选取的纹理的宽和高，texW 和 texH 代表整张纹理的宽和高
        Gui.drawModalRectWithCustomSizedTexture(x, y, u, v, w, h, texW, texH);
    }
}
```

实际上，有这些方法，你甚至可以绘制出比原版那些药水图标更华丽的特效出来。
