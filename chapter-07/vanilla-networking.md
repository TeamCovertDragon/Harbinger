## 原版的网络通信

每一个 `EntityPlayerMP`（注意那个 MP）下都有一个名为 `connection` 的字段，这个字段代表了这个玩家所在的客户端和服务器之间的网络通信频道。原版的所有数据包，也就是那些名字以 `SPacket` 和 `CPacket` 开头的类，都是通过这里传输的。  

### 从逻辑服务器向逻辑客户端发包

```java
EntityPlayerMP player = ...;
player.connection.sendPacket(...);
```

至于数据包的构造，请参考各个 `SPacket` 开头的类的具体细节，这里不作赘述。

### 从逻辑客户端向逻辑服务器发包

WIP
