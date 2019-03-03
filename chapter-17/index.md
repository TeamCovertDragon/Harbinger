## 资源包

前文的部分章节提到了这些东西：

  * 纹理
  * 模型
  * 语言文件
  * 音效
  * 粒子效果贴图

原版是通过一个名叫资源包（Resource Pack）的系统来加载这些文件的。

### 刚需

从 Jar 中加载资源文件是很多 Java 编写的程序的日常。自然，最经典的解决方案是 `Class.getResourceAsStream`，但请考虑一下 Minecraft 曾经有一个名为“材质包”（Texture Pack）的系统，允许你为 Minecraft 里的方块更换纹理。这个系统在 Minecraft 1.6 时升级成了“资源包”，使得音效、语言文件等其他资源也可以更换。Mojang 为此引入了一整套从指定位置读取数据的系统——也就是这一章要讨论的内容。

### 再论 `ResourceLocation`

前文中有很多地方用到了名为 `ResourceLocation` 的类，包括但不限于：

  * 注册名
  * 模型路径
  * 绝大多数需要写形如 `minecraft:foo` 的 String 的地方

那为什么这个类叫 `ResourceLocation`？虽然没有任何第一手资料能说明这个名字的来历，但是我们不妨来看一下另外两个东西的全称：

  * 统一资源标识符（URI）：Uniformed Resource Identifier
  * 统一资源定位符（URL）：Uniformed Resource Locator

似曾相识，不是吗？  
所以我们大可以将 `ResourceLocation` 看作是一种类似 URI 的存在，它在整个 Minecraft 的系统中起“地址”的作用。你可以通过一个注册名拿到某个受注册表管理的对象，也可以用一个相对路径告知 Minecraft 将 GL 上下文使用的纹理切换到指定纹理上去。

### 不再硬编码资源位置

使用资源包最大的优势便是允许用户用第三方资源包覆盖同位置下的资源。  
若是仅从代码的角度来看，它实际上是一层挡住具体提取资源的方式的抽象，这样一来开发者只需要通过统一的资源包系统就能提取某个 bundle 或某个别的目录下的任意资源了。使用资源包系统也异常得简单：

```java
Minecraft.getMinecraft().getResourceManager().getResource(new ResourceLocation("...", "..."));
```
