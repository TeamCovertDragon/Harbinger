## 音效控制

Minecraft 中有各种各样的音效——某个维度的背景音乐、某个实体游荡时的声音、方块破坏的声音、等等。每一个这样的音效都对应着一个 `SoundEvent` 对象。

```java
import net.minecraft.util.SoundEvent;

public static final SoundEvent A_NEW_SOUND = new SoundEvent(new ResourceLocation("my_mod", "a_new_sound"));
```

这些 `SoundEvent` 也是需要注册的。原版使用的 `SoundEvent` 对象都可以在 `net.minecraft.init.SoundEvents` 这个类中找到。

```java
// 注意到注册名和构造时传入的那个“音效名”不是一回事。
// 通常，为简单起见，一般会在这两个地方使用同样的名字。
@SubscribeEvent
public static void onSoundEvenrRegistration(RegistryEvent.Register<SoundEvent> event) {
    event.getRegistry().register(A_NEW_SOUND.setRegistryName(new ResourceLocation("my_mod", "a_new_sound")));
}
```

### `sounds.json`

但是 `SoundEvent` 这个对象的构造器只接受一个 `ResourceLocation` 作为参数。那它为什么能代表一个具体的声音文件？因为具体的音效定义是在 [`assets/[mod_id]/sounds.json`](https://minecraft-zh.gamepedia.com/Sounds.json) 中出现的。

```json
{
    "a_new_sound": {
        "category": "block",
        "subtitle": "my_mod.sound.a_new_sound",
        "sounds": [
            "my_mod:music/one_new_sound",
            "my_mod:music/another_new_sound"
        ]
    }
}
```

具体来说，名为 `a_new_sound` 的 JSON Object 指向了之前创建的 `A_NEW_SOUND` 对象，可以注意到这个名字和构造器中传入的 `ResourceLocation` 的 `path` 部分是一样的。
然后看一下这个 JSON Object 中的各个字段：

  - `category` 代表了这个音效的“类别”。
  - `subtitle` 是指开始播放声音时右下角弹出的文字说明的本地化键。
  - `sounds` 字段定义了具体的音效，注意到这是一个 JSON 数组，它的含义是“在需要使用音效时，随机从指定数组中选一个音频并使用”。该字段只能为包含 JSON String 的数组，其中：
    - `my_mod:sound/one_new_sound` 代表 `assets/my_mod/sounds/music/one_new_sound.ogg` 这个文件。
    - `my_mod:music/another_new_sound` 则代表 `assets/my_mod/sounds/music/another_new_sound.ogg` 这个文件。

是的，Minecraft 使用的声音文件统一为 `ogg` 格式。Minecraft 并不支持其他格式的音频文件，所以只能先把其他格式文件转码为 `ogg` 格式才能在 Minecraft 中使用。（当然你也可以考虑塞个解码器进去，但那样无异于绕开绝大部分 Minecraft 的音频管理机制。）  

### 在哪里会用到 `SoundEvent`？

用到 `SoundEvent` 的地方其实不少，但其实挺集中的，主要是方块音效、实体音效、唱片机相关及 `World` 下的几个方法。

#### `SoundType`

方块的诸多音效（放置、破坏、点击、行走、跌落）均由 `SoundType` 控制，在这个类下有若干原版方块使用的 `SoundType` 实例，但如果你需要一个特殊的 `SoundType`，就需要一个新的 `SoundType` 实例了。`SoundType` 的构造器接受五个 `SoundEvent` 参数，依次代表：方块被破坏时的音效、玩家行走在上面时的音效、放置时的音效、被点击时的音效和玩家掉落在上面时的音效。

#### `World.playSound`

这个方法有三个版本：

|Serage|签名/参数列表|解释|
|:------|:------|:------|
|`func_184133_a`|`void playSound(EntityPlayer player, BlockPos pos, SoundEvent sound, SoundCategory category, float volume, float pitch)`|`func_184148_a` 的三个 `double` 换成 `BlockPos` 的版本。|
|`func_184134_a`|`void playSound(double x, double y, double z, SoundEvent sound, SoundCategory category, float volume, float pitch, boolean distanceDelay)`|不需要玩家实体的版本。只在物理客户端上有用。|
|`func_184148_a`|`void playSound(EntityPlayer player, double x, double y, double z, SoundEvent sound, SoundCategory category, float volume, float pitch)`|只在逻辑服务器上有用。也因此可以被服务器端插件等利用。表面上它需要一个玩家实体，但实际上你可以传入 `null`。|

用这个方法可以播放任意音效。

#### `World.playEvent`

特化版本的 `World.playSound`，用途非常有限，属于原版网络通信的一部分，如果不是复用原版逻辑的话就没什么用。而且这个方法也不只是关于音效的，它还和很多原版的粒子特效有关系。  
这个方法的签名如下：

```java
void playEvent(EntityPlayer player, int eventType, BlockPos pos, int data)
```

`eventType` 和 `data` 的解释可参考这里：https://github.com/MinecraftForge/MinecraftForge/pull/5323  
一旦此 PR 合并，相关信息将会作为 `Constants.WorldEvent` 的常量字段及 JavaDoc 出现。

#### `SPacketSoundEffect`

或者也可以直接从服务器上给客户端发个数据包过去。

<!-- 具体示范：施工中。 -->

#### `World.playRecord` 与 `ItemRecord`

`ItemRecord` 是原版十余个唱片的物品所使用的类，当手持对应的 `ItemStack` 右击唱片机时它会进而调用 `World.playRecord`。简单复用 `ItemRecord` 类便可制作新的唱片物品。

```java
new ItemRecord("my_mod.my_first_record", MySoundEvents.RECORD_1);
```
