## 方块形式存在的流体

因为是以方块形式存在的流体，所以这首先是方块。可以直接使用 `BlockFluidClassic` 这个类，它尽可能还原了原版两种流体方块（水和熔岩）的行为。可以直接 `new BlockFluidClassic(...)`，也可以先继承：

````java
// WIP
import net.minecraftforge.fluids.BlockFluidClassic;

public class MyFluidBlock extends BlockFluidClassic {

}
````

当然你要自己写也可以，因为 `Fluid` 只持有一个 `Block` 字段，所以只要是个方块就可以了。  
除了 `BlockFluidClassic`，还有两个类可以用：

* `BlockFluidBase`，一个抽象类。直接继承这个类会省下不少代码。
* `BlockFluidFinite` <!-- TODO 它的 JavaDoc 声称它的流动行为基于元胞自动机？待调查 -->
