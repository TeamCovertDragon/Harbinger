### 方块概论

#### 你的第一个方块

````java
public class ExampleBlock extends Block {
    public ExampleBlock(Material material) {
        super(material);
    }
}
````

和物品一样，需要注册：

````java
@Mod.EventBusSubsriber(modid = "mymodid")
public final class BlockLoader {

    @SubscribeEvent
    public static void registerBlock(RegistryEvent.Register<Block> event) {
        // 一定要为每一个方块提供一个注册名。注册名要求符合 Resource Location 的一般约定，
        // 即不能使用大写字母。
        event.getRegistry().register(
            new ExampleBlock(Material.ROCK)
            .setRegistryName("my_mod:example_block")
        );
    }

}
````

完。

#### `ItemBlock`

如上一章所述，玩家背包里的都是 `ItemStack`。既然如此，方块也不会例外。只不过方块对应物品的 `Item` 类基本都继承了 `ItemBlock` 这个类。
