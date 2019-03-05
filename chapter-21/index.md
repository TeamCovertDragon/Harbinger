## 合成表

Minecraft 从 1.12 起引入了使用技术性 JSON 文件的原版工作台合成表定义。Forge 在它的基础之上引入了一些新的功能，并让历史悠久的 OreDictionary 系统也能用在这个新格式中。

### 一个简单的无序合成的例子：泥土换钻石

```json
{
  "type": "minecraft:crafting_shapeless",
  "group": "my_mod:example",
  "result": { "item": "minecraft:diamond_block", "count": 64 },
  "ingredients": [
      { "item": "minecraft:dirt", "data": 0 },
      { "item": "minecraft:dirt", "data": 0 }
  ]
}
```

### 合成表放哪？

Minecraft 原定于 1.12 引入的数据包跳票到了 1.13 才放出，但 Mojang 仍然在 1.12 中把合成表全转换成了 JSON 格式。Mojang 选择的存放位置是 `assets/minecraft/recipes/`。  
自然地，Forge 也将 JSON 视作推荐标准，并提供了对应的支持。与原版的路径相对应，Mod 的合成表只需要放在 `assets/[modid]/recipes/` 里就能被 Forge 扫描到并自动加载。`modid` 自然就是你的 Mod 的 id。有一点要注意，虽然这个路径和资源包的路径如出一辙，但合成表不是资源包的一部分。设定上讲，它是数据包的一部分，但显然 1.12 没有数据包，只能先这样将就了。（实际上原版和 Forge 都硬编码了这个路径。）
