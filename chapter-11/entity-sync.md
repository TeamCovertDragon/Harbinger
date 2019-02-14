## 实体数据同步

实体数据的同步主要依赖两种方式：`DataParameter<?>` 和 `IEntityAdditionalSpawnData`。

### `EntityDataManager` 与 `DataParameter<?>`

对于需要经常同步的数据，Minecraft 使用了一套（自己造的）名为 `EntityDataManager` 的系统。

<!-- TODO (3TUSK): 这系统到底怎么用来着？ -->

### `IEntityAdditionalSpawnData`

实体的数据在客户端与服务器之间同步时，难免会遇到需要重新生成实体的情况。  
TileEntity 下有一系列方法用于同步数据，但实体并没有类似的方法。因为非原版实体的数据同步是 FML 接管的，FML 在这里提供了一个替代方案：`IEntityAdditionalSpawnData`。

```java
public class MyEntity extends Entity implements IEntityAdditionalSpawnData {
    @Override
    public void writeSpawnData(ByteBuf buffer) {
        // 写数据
    }

    @Override
    public void readSpawnData(ByteBuf additionalData) {
        // 读数据
    }
}
```

这样一来，在实体数据从服务器发送到客户端，客户端根据这些数据重新 new 实体并生成在客户端世界的过程中，就会调用这两个方法，以同步额外的数据。

#### 特例

除了 `IEntityAdditionalSpawnData`，FML 还有一个和数据同步有关的接口：`IThrowableEntity`。根据它的 JavaDocs，它是为了提供来自 ModLoaderMP（一个已经不再更新的 Mod 加载器）提供的 `owner` 功能而生的。实际上它的作用是用于同步所有投掷物的来源的（即抛出该投掷物的玩家），实现这个接口后 FML 会识别到并执行特殊的逻辑。
