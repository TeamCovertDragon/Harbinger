### 方块概论

#### 你的第一个方块
````java
public class ExampleBlock extends Block {
    public ExampleBlock(Material material) {
        super(material);
        //同样是不能有大写字母，即 snake_case
        //此方法的用途很快会提到。
        //此方法返回 Block，可组成调用链。
        this.setRegistryName("example_block");
    }
}
````

和物品一样，需要注册：

````java
@Mod.EventBusSubsriber(modid = "mymodid")
public final class BlockLoader {

    // 你不需要手动调用此方法！Forge 会自动调用它的。
    @SubscribeEvent
    public static void registerBlock(RegistryEvent.Register<Block> event) {
        event.getRegistry().register(myItem);
    }

}
````

完。


#### `ItemBlock`

如上一章所述，玩家背包里的都是 `ItemStack`。既然如此，方块也不会例外。它们基本都在使用 `ItemBlock` 这个类。
