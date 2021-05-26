# 残酷的事实和救世者：`SimpleNetworkWrapper`

~~说完这么多，但是还是没回答一开始的问题：怎么处理网络 IO？~~  
~~很抱歉你要自己想办法。~~  

开玩笑的。MinecraftForge 有几个已经封装好的解决方案，直接拿去用就可以了。

首先造个轮子：

```java
public enum MySimpleNetworkHandler {
    INSTANCE;

    // 首先我们拿到一个 SimpleNetworkWrapper 的实例，然后对它进行封装
    private final SimpleNetworkWrapper channel = NetworkRegistry.INSTANCE.newSimpleChannel("my_mod");

    // 向某个维度发包（服务器到客户端）
    public void sendMessageToDim(IMessage msg, int dim) {
        channel.sendToDimension(msg, dim);
    }

    // 向某个维度的某个点发包（服务器到客户端）
    public void sendMessageAroundPos(IMessage msg, int dim, BlockPos pos) {
        // TargetPoint的构造器为：
        // 维度id x坐标 y坐标 z坐标 覆盖范围
        // 其中，覆盖范围指接受此更新数据包的坐标的范围
        channel.sendToAllAround(msg, new NetworkRegistry.TargetPoint(dim, pos.getX(), pos.getY(), pos.getZ(), 2.0D);
    }

    // 向某个玩家发包（服务器到客户端）
    public void sendMessageToPlayer(IMessage msg, EntityPlayerMP player) {
        channel.sendToPlayer(msg, player);
    }

    // 向所有人发包（服务器到客户端）
    public void sendMessageToAll(IMessage msg) {
        channel.sendToAll(msg);
    }

    // 向服务器发包（客户端到服务器）
    public void sendMessageToServer(IMessage msg) {
        channel.sendToServer(msg);
    }
}
```

然后是数据包：

```java
import java.util.UUID;

public class MessageFoo implements IMessage {

    UUID bar;

    // 解包用的构造器
    public MessageFoo() {}

    // 封包用的构造器
    public MessageFoo(UUID uuid) {
        this.bar = uuid;
    }

    // 封包
    @Override
    public void toBytes(ByteBuf buf) {
        buf.writeLong(this.bar.getMostSignificantBits());
        buf.writeLong(this.bar.getLeastSignificantBits());
    }

    // 解包
    @Override
    public void fromBytes(ByteBuf buf) {
        final long most = buf.readLong();
        final long least = buf.readLong();
        this.bar = new UUID(most, least);
    }

    // 以及对应的 Handler，通常是静态内部类，如果不是静态的那一定不是内部类，没有第三种情况。
    // 这里的泛参是这样，IMessageHandler<REQ, REPLY>，REQ 代表收到的数据包的类型，而
    // REPLY 代表“回执”包的类型。
    // 理论上，如果不需要发送回执包作为应答，则第二个泛参保留 IMessage 并实际上返回 null，
    ///否则应填入对应的类型。
    // 但实际上，若处理数据的过程需要操作游戏主线程上的对象，这里的回执包是无法正确工作的，
    // 所以，在这里你实际上只能使用 IMessage 并返回 null。
    // 参考：https://github.com/MinecraftForge/MinecraftForge/issues/4231
    public static class MessageFooHandler implements implements IMessageHandler<MessageFoo, IMessage> {
        @Override
        public IMessage onMessage(MessageFoo myFoo , MessageContext ctx) {
            // 此时 MessageFoo 里的数据已经经过 fromBytes 处理并展开，可以直接使用。
            UUID uuid = message.bar;
            return null;
        }
    }
}
```

最后，回到刚才的 `MySimpleNetworkHandler`，给它加个构造器：

```java
private MySimpleNetworkHandler() {
    // 注册该 Message 及处理它的 Handler
    // 第三个参数是识别码 (discriminator)
    // 第四个参数是收包端，是逻辑端。
    // 如果一个 Message 可以同时在两个逻辑端发送，那就要注册两次，使用不同的识别码
    this.instance.registerMessage(MessageFooHandler.class, MessageFoo.class, 0, Side.CLIENT);
}
```

就可以用了。用法如下：

```java
// 第一个参数是要发送的包，第二个参数是收包的玩家。
// 对于 sendMessageToAll，收包的是所有玩家：所有玩家的客户端都会各自收到一样的包并处理。
// 对于 sendMessageToServer，收包的不是玩家，而是服务器。
MySimpleNetworkHandler.INSTANCE.sendMessageToPlayer(new MessageFoo(…), player);
```
