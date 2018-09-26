### 实体数据同步

实体的数据在客户端与服务器之间同步时，难免会遇到需要重新生成实体的情况。  
TileEntity 下有一系列方法用于同步数据，但实体并没有类似的方法。因为非原版实体的数据同步是 FML 接管的，FML 在这里提供了一个替代方案：IEntityAdditionalSpawnData。

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
