## 函数（`.mcfunction`）

Minecraft 中的“函数”指的是一系列按顺序执行的多行命令的集合，以文本的形式保存，使用 `.mcfunction` 作为其扩展名。关于函数的内容在 [Minecraft Wiki](https://minecraft-zh.gamepedia.com/%E5%87%BD%E6%95%B0) 上已有详细介绍，这里不做赘述。

### 残酷的事实

Forge 并没有提供任何从 Mod 的 jar 中加载函数的功能。目前，函数唯一的加载入口是存档目录下的 `data/functions` 目录。  
换言之，作为 Modder，你基本无法在进度中使用函数作为奖励。
