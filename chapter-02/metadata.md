### Mod 的元数据

Forge 允许你给你的 Mod 提供一个元数据描述文件，用于 Forge 在游戏里内建的 Mod 列表显示时显示各种有用的信息，比如简介啦、作者名单、鸣谢信息、网站啦什么的。  
这个文件叫 `mcmod.info`，实际上是个 JSON，应放置在开发环境的 `src/main/resources/` 目录下。它长这样：

```json
[
    {
        "modid": "example_mod",
        "name": "Example Mod",
        "description": "Just a demo mod",
        "version": "${version}",
        "mcversion": "${mcversion}",
        "url": "http://example.net",
        "updateUrl": "",
        "authorList": ["Some random guy"],
        "credits": "Forge guys",
        "logoFile": "logo.png",
        "screenshots": [],
        "useDependencyInformation": true,
        "requiredMods": [],
        "dependencies": []
    }
]
```

`logoFile` 可以用来放你的 Mod 的 banner 或 logo。效果奇佳。注意不要太大，不然放不下。查找 `logoFile` 时的 `/` 是 jar 内部的根目录，对于源码来说，那就是 `src/main/resources/` 目录。和 `mcmod.info` 放一块就可以了。  
此外，`updateUrl` 字段已 deprecated，没有任何效果。可以直接删除。  
`useDependencyInformation` 的作用会在第 28 章讲到。

### 数组？
你应该注意到了这个 JSON 的最外层是个数组。这意味着，如果你的一个 jar 里有两个 `@Mod` 定义的 Mod，你可以利用这个数组把它们的元数据塞一起。

```json
[
    {
        "modid": "mod_1",
        "...": "..."
    },
    {
        "modid": "mod_2",
        "...": "..."
    }
]
```
