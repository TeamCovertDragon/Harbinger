### 对其他格式的模型的支持

自然地，Minecraft 没有内建对其他格式的模型支持。出于某些原因，Forge 提供了两种格式的支持——Blitz3D 和 Wavefront OBJ。

关于这两种格式的信息可参考：
  - https://en.wikipedia.org/wiki/Blitz_BASIC#Blitz3D
  - https://en.wikipedia.org/wiki/Wavefront_.obj_file

#### 使用

首先要告知对应的加载器：“我需要用到特殊模型”

```java
// 需要使用 B3D 格式模型的话需要调用这个。
B3DLoader.addDomain(myModID);

// 需要使用 OBJ 格式模型的话需要调用这个。
OBJLoader.addDomain(myModID);
```

在 `ModelRegistryEvent` 发布时调用就好。然后照常指定模型文件资源位置即可。注意最后的 `.obj` 扩展名被明确声明出来了；对于 `b3d` 格式的模型同理。这个扩展名是 Forge 识别的依据；不指明的话 Forge 会默认这是一个普通的 Minecraft 方块模型，然后去尝试找一个 `.json` 文件。  
对于 Wavefront OBJ 模型来说，对应的 `.mtl` 文件也应位于同一目录下。

```json
{
    "forge_marker": 1,
    "variants": {
        "normal": {
            "model": ".../my_model.obj"
        }
    }
}
```
