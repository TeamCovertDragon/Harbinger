## 残酷的事实和救世者：`SimpleNetworkWrapper`

说完这么多，但是还是没回答一开始的问题：怎么处理网络 IO？  
很抱歉你要自己想办法。  

开玩笑的。MinecraftForge 有几个已经封装好的解决方案，直接拿去用就可以了。

首先造个轮子：

````java
public enum MySimpleNetworkHandler {
    INSTANCE;

    private final SimpleNetworkWrapper channel = NetworkRegistry.INSTANCE.newSimpleChannel("example_mod");

    //从这里开始就是一堆封装...

    //向某个维度发包
    public void sendMessageToDim(IMessage msg, int dim) {
        channel.sendToDimension(msg, dim);
    }

    //向某个维度的某个点发包
    public void sendMessageAroundPos(IMessage msg, int dim, BlockPos pos) {
        // TargetPoint的构造器为：
        // 维度id x坐标 y坐标 z坐标 覆盖范围
        // 其中，覆盖范围指接受此更新数据包的坐标的范围
        channel.sendToAllAround(msg, new NetworkRegistry.TargetPoint(dim, pos.getX(), pos.getY(), pos.getZ(), 2.0D);
    }

    //向某个玩家发包
    public void sendMessageToPlayer(IMessage msg, EntityPlayerMP player) {
        channel.sendToPlayer(msg, player);
    }

    //向所有人发包
    public void sendMessageToAll(IMessage msg) {
        channel.sendToAll(msg);
    }

    //向服务器发包
    public void sendMessageToServer(IMessage msg) {
        channel.sendToServer(msg);
    }
}
````

然后是数据包：

````java
public class MessageFoo implements IMessage {
    //解包用的构造器
    public MessageFoo() {}

    //别问我为毛是这个
    java.util.UUID bar;

    //封包用的构造器
    public MessageFoo(UUID uuid) {
        this.bar = uuid;
    }

    @Override
    public void toBytes(ByteBuf buf) {
        buf.writeBytes(bar.toString().getBytes())
    }

    @Override
    public void fromBytes(ByteBuf buf) {
        byte[] bytes = new byte[36]; //如果我没记错的话，这就是UUID的长度。
        buf.readBytes(bytes);
        this.bar = new UUID(new String(bytes));
    }

    //以及对应的Handler，通常是静态内部类
    //对于这个IMessageHandler，如果是内部类那一定是静态的，如果不是静态的那一定不是内部类，没有第三种情况
    //这里的泛参是这样，IMessageHandler<REQ, REPLY>
    //如果不需要在收到包后发回复包，则第二个泛参保留IMessage，否则请填入对应的类型
    public static class MessageFooHandler implements implements IMessageHandler<MessageFoo, IMessage> {
        @Override
        public IMessage onMessage(MessageFoo myFoo , MessageContext ctx) {
            UUID uuid = message.bar;
            // 该干啥干啥
            // 返回null表示没有回复包
            return null;
        }
    }
}
````

最后，回到刚才的`MySimpleNetworkHandler`，给它加个构造器：

````java
private MySimpleNetworkHandler {
    //注册该Message及Handler
    //第三个参数是识别码 (discriminator)
    //第四个参数是收包端，是逻辑端。
    //如果一个Message可以同时在两个逻辑端发送，那就要注册两次，使用不同的识别码
    //^ForgeDoc如是说。
    this.instance.registerMessage(MessageFooHandler.class, MessageFoo.class, 0, Side.CLIENT);
}
````

就可以用了。用法如下：

```java
// 待补全
```
