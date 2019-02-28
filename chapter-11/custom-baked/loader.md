## 模型加载器：`ICustomModelLoader`

`ICustomModelLoader`，顾名思义，一个加载 `IModel` 的东西。正确实现并注册的 `ICustomModelLoader` 拥有根据给定 `ResourceLocation` 产生一个原始的 `IModel` 的能力。

```java
public class ABrandNewModelLoader implements ICustomModelLoader {

    @Override
    public void onResourceManagerReload(IResourceManager manager) {
        // 用于资源刷新时刷新内部数据。若没有数据需要刷新，直接留空即可。
    }

    @Override
    public boolean accepts(ResourceLocation location) {
        // 这个方法决定了该加载器能加载哪些模型。
        // 一个 location 只可能有一个 ICustomModelLoader 能加载它。
        // 如果有两个 ICustomModelLoader 的这个方法对同一个 location 返回了 true，
        // 则会抛出异常。所以在实现这个方法时，务必比较足够多的信息。
        return false;
    }

    @Override
    public IModel loadModel(ResourceLocation location) {
        // 这个就是加载 IModel 的方法了。没什么可说的。
        return something; // 你大概不能返回 null。
    }
}
```

注册 `ICustomModelLoader` 也很简单：

```java
ModelLoaderRegistry.registerLoader(new ABrandNewModelLoader());
```
