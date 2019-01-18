## 音效控制

Minecraft 中有各种各样的音效——某个维度的背景音乐、某个实体游荡时的声音、方块破坏的声音、等等。每一个这样的音效都对应着一个 `SoundEvent` 对象。

```java
import net.minecraft.util.SoundEvent;

public static final SoundEvent A_NEW_SOUND = new SoundEvent(new ResourceLocation("my_mod", "a_new_sound"));
```

这些 `SoundEvent` 也是需要注册的。原版使用的 `SoundEvent` 对象都可以在 `net.minecraft.init.SoundEvents` 这个类中找到。

```java
// 注意到注册名和构造时传入的那个“音效名”不是一回事。
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
然后看一下这个 JSON Object 中的各个字段：`category` 代表了这个音效的“类别”；`subtitle` 是指开始播放声音时右下角弹出的文字说明的本地化键；而最关键的 `sounds` 字段定义了具体的音效，其中 `my_mod:sound/one_new_sound` 代表 `assets/my_mod/sounds/music/one_new_sound.ogg` 这个文件，而 `my_mod:music/another_new_sound` 则代表 `assets/my_mod/sounds/music/another_new_sound.ogg` 这个文件。  
是的，Minecraft 使用的声音文件统一为 `ogg` 格式。
