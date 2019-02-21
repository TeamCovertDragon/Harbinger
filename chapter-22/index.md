## 进度（Advancement）

进度（Advancement）是 1.12 引入的，旨在取代旧版本中成就系统的新机制。既然是为取代成就而生，那么它的功能也就和成就基本一致了。不过它实际上功能要比成就强大，比如触发进度后的 callback（给予经验、执行指令、从战利品表中抽奖等）、支持布尔逻辑组合的判据（Criteria）系统、自动生成的树状展示图等。

> 插一句话，criteria 是 criterion 的复数形式。这个算是比较少见的一类不规则变化，和古典希腊语有关系。  

### 第一个进度

自然地，进度文件也是 JSON。和工作台合成一样，因为数据包跳票，所以被迫放在 `assets/minecraft/advancements` 目录下。Forge/FML 在 patch 原版底层后可从 Mod 各自的文件的 `assets/[modid]/advancements` 目录中加载 Mod 的进度。

```json
{
    "display": {
        "icon": { "item": "...", "nbt": "..." },
        "title": "First Step",
        "frame": "goal",
        "background": "my_mod:...",
        "description": "Hey! This is my first advancement",
        "show_toast": false,
        "announce_to_chat": false,
        "hidden": false
    },
    "criteria": {
        "do_something": {
            "trigger": "...",
            "conditions": { "...": "..." }
        }
    },
    "rewards": {
        "recipes": [ "..." ]
    }
}
```

其中 `title` 和 `description` 除了可以是 String 外，还可以是 `/tellraw` 或 `/title` 使用的 [JSON 文本][ref-raw-json-text]（实质是第十三章中提到的 `ITextComponent` 序列化成 JSON 后的形式）。这里不多做赘述。

[ref-raw-json-text]: https://minecraft-zh.gamepedia.com/%E5%91%BD%E4%BB%A4#JSON.E6.96.87.E6.9C.AC

### 第二个进度

WTF？为什么要讲这个？只有一个例子不够吗？  
还真不够。刚才我们只是新建了一个进度标签页上的根进度，难道我们不需要像以前的成就树一样弄个进度树吗？

```json
{
    "display": {
        "icon": { "item": "...", "nbt": "..." },
        "title": { "translate": "advancement.my_mod.second_step.title" },
        "frame": "task",
        "description": { "translate": "advancement.my_mod.second_step.description" },
        "show_toast": false,
        "announce_to_chat": false,
        "hidden": false
    },
    "parent": "my_mod:first_step",
    "criteria": {
        "a": { "...": "..." },
        "b": { "...": "..." },
        "c": { "...": "..." }
    },
    "requirements": [ [ "a", "b" ], "c" ],
    "rewards": {
        "loot": [ "..." ],
        "experience": 233,
        "function": "..."
    }
}
```

对比第一个进度 JSON，可以发现第二个进度 JSON 没有 `display.background`。那个字段是给根进度准备的，用于决定整个进度标签页的背景纹理。  
同时第一个进度 JSON 中没有 `requirements` 字段。这实际上有一点布尔逻辑在里面。

#### 布尔逻辑

`requirements` 字段定义了进度的具体触发条件。

```json
"requirements": [ [ "a", "b" ], "c" ]
```

上述 JSON 相当于 `(a || b) && c`（`(a ∨ b) ∧ c`、`(a+b).c` 或者 `a.c+b.c`）；换言之，即“必须达成 `c`，对于 `a` 和 `b` 则任选一个达成即可”。  
`requirements` 字段是可选的。当没有 `requirements` 字段时，默认为 `criteria` 字段中定义的所有条件都必须完成，等价于写 `"requirements": [ "a", "b", "c", ... ]`（`a ∧ b ∧ c ∧ ...`）。

#### 有哪些 Criterion 可用？

https://minecraft-zh.gamepedia.com/%E8%BF%9B%E5%BA%A6#.E8.A7.A6.E5.8F.91.E5.99.A8.E5.88.97.E8.A1.A8

#### 能自己写 Criterion 吗？

当然可以。参考[“自定义进度触发条件”](forge-extension/custom-criterion.md)一节获得详细信息。同时，对于 `minecraft:inventory_changed` 等牵扯到物品判定的 criteria，还有一个 Forge patch 进去的[自定义 Item Predicate 的系统](forge-extension/custom-item-predicates.md)可用。

#### 奖励？

不幸的是，进度只有四种奖励可用，且 Forge 暂未提供任何扩展：

  - `experience` - 经验值，即直接给予玩家经验值
  - `recipes` - 原版工作台合成，即直接为玩家解锁该合成配方
  - `loot` - 战利品表，即从指定表中抽取物品作为奖励
  - `function` - [函数](../chapter-23/function.md)，即直接以玩家身份执行此函数。
