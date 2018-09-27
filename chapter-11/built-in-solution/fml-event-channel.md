## FMLEventChannel

FML 提供的另一个解决方案，基于 FML 的 EventBus（没错就是第二章里讲的那一个）。有很多 Mod 在用它。下面来做个示范。

````java
// 自行补全需要导入的包

public enum MyNetworkManager {

    INSTANCE;

    //获得一个信道实例。建议使用Modid来命名。
    //当然也可以用别的，保证唯一即可。
    private final FMLEventChannel channel = NetworkRegistry.INSTANCE.newEventDrivenChannel(CHANNEL_NAME);

    private static final String CHANNEL_NAME = "example_mod";

    private MyNetworkManager() {
        //注册listener
        channel.register(this);
    }

    @SubscribeEvent //可加注@SideOnly(Side.CLIENT)
    public void onClientPacketEvent(ClientCustomPacketEvent event) {
        decodeDataClient(event.getPacket().payload(), Minecraft.getMinecraft().thePlayer);
    }

    @SubscribeEvent
    public void onServerPacketEvent(ServerCustomPacketEvent event) {
        decodeDataServer(event.getPacket().payload(), ((NetHandlerPlayServer)event.getHandler()).playerEntity);
    }

    //可加注@SideOnly(Side.CLIENT)
    private void decodeDataClient(ByetBuf input, EntityPlayerSP player) {
        // 服务器->客户端的解包逻辑
        // 解包的第一步逻辑应在这里出现，即识别包的种类
        // 具体实现应将InputStream重新用MyPacket包装后解耦实现
    }
    
    //但这里就不能@SideOnly(Side.SERVER)，因为单机游戏也需要这个。
    //这里的server实质是逻辑服务器端。
    private void decodeDataServer(ByteBuf input, EntityPlayerMP player) {
        // 客户端->服务器的解包逻辑
        // 解包的第一步逻辑应在这里出现，即识别包的种类
        // 具体实现应将InputStream重新用MyPacket包装后解耦实现
    }

    //从这里开始就是一堆封装一样的玩意...

    //向某个维度发包
    public void sendPacketToDim(MyPacket pkt, int dim) {
        channel.sendToDimension(createFMLProxyPacket(pkt), dim);
    }

    //向某个维度的某个点发包
    public void sendPacketAroundPos(MyPacket pkt, int dim, BlockPos pos) {
        // TargetPoint的构造器为：
        // 维度id x坐标 y坐标 z坐标 覆盖范围
        // 其中，覆盖范围指接受此更新数据包的坐标的范围
        // 之所以要强调最后一个参数是double是因为Kotlin并不会帮你把2隐式转换为kotlin.Double....
        channel.sendToAllAround(createFMLProxyPacket(pkt), new NetworkRegistry.TargetPoint(dim, pos.getX(), pos.getY(), pos.getZ(), 2.0D));
    }

    //向某个玩家发包
    public void sendPacketToPlayer(MyPacket pkt, EntityPlayerMP player) {
        channel.sendToPlayer(createFMLProxyPacket(pkt), player);
    }

    //向所有人发包
    public void sendPacketToAll(MyPacket pkt) {
        channel.sendToAll(createFMLProxyPacket(pkt));
    }

    //向服务器发包，这个给客户端用
    public void sendPacketToServer(MyPacket pkt) {
        channel.sendToServer(createFMLProxyPacket(pkt));
    }

    //FMLEventChannel经由这个NetworkHandler暴露出来的方法到此为止

    private FMLProxyPacket createFMLProxyPacket(MyPacket pkt) {
        ByetBuf buffer = Unpooled.buffer();
        packet.writeData(buffer);
        return new FMLProxyPacket(new PacketBuffer(buffer), CHANNEL_NAME);
    }
}

// 简单的数据包实现
public interface MyPacket {

    /**
     * 发包端写数据
     */
    void writeData(ByteBuf out) throws IOException;

    /**
     * 解包端读数据
     */
    void readData(ByteBuf in) throws IOException;

}
````

就是这样。用法和 `SimpleNetworkWrapper` 大同小异。

```java
//待补全
```

### 设计模式

工厂。肯定是工厂啊。

````java
public enum MyDamnitPacketFactory {
    INSTANCE;

    public MyPacket getDummyPacketById(byte id) {
        return PacketType.FACTORY.get(PacketType.VALUES[(int)id]).get();
    }

    private enum PacketType {
        ;
        //按需加枚举类型
        //然后就可以直接按数组进行索引了
        public static final PacketType[] VALUES = PacketType.values()
        public static final Map<PacketType, Supplier<MyPacket>> FACTORY = new EnumMap<>(PacketType.class);
    }
}

````
看这样多好。根据读取到的第一个byte自动生产空Packet然后解包，NetworkHandler里从此看不见任何实现。
（总感觉多此一举了）
