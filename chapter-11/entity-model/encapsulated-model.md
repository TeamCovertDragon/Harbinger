## 所谓的“实体模型”

但多数时候，实体的渲染只是一堆不同尺寸的长方体的平移、旋转及缩放操作而已，对于原版生物实体来说这个情况尤为普遍。为此 Minecraft 搞出来了一套不同寻常的“模型”系统。这套系统是完全写死在代码里的，但它包含了大量可以用其他平台无关的数据交换格式表示的数据。  
社区有时候会管这种模型叫“Java 模型”，实际上这个东西只有 Minecraft 在用，而且它也不是严格意义上的模型，因为它还包括了控制某些部件旋转的逻辑，这已经不是一个静态的模型会有的东西了。因此，“Java 模型”的叫法并不严谨。

### `ModelBase`

观察 `RenderLiving` 的构造器，它要求三个参数：

  - `RenderManager`。这个所有的 `Render` 都有。
  - `ModelBase`。
  - `float`，<!-- 根据 MCP 的参数名，-->它用于控制实体阴影的尺寸。

那个 `ModelBase` 便是这个模型系统的起点了。所有 `RenderLiving` 的派生类中，都向这个构造器中传入了一个特定的 `ModelBase` 的实现。

```java
public class MyEntityModel extends ModelBase {
    @Override
    public void render(Entity entity, float limbSwing, float limbSwingAmount, float ageInTicks, float netHeadYaw, float headPitch, float scale) {
        // 具体的渲染逻辑全部在此发生。
    }

    @Override
    public void setRotationAngles(float limbSwing, float limbSwingAmount, float ageInTicks, float netHeadYaw, float headPitch, float scaleFactor, Entity entity) {
        // 你应该注意到了，原版很多实体是有写实的走路动画效果的。
        // 这个方法可以用来更新当前模型的信息，是实现类似走路动画等动画效果的基础。
    }
}
```

### `ModelRenderer`

`ModelRenderer` 实际上代表了一堆要被渲染长方体的集合。这些长方体可以是独立存在的（`ModelBox`），也可以是另一堆长方体（是的，Composite pattern，一个 `ModelRenderer` 下也可以包含一堆 `ModelRenderer`）。这些长方体们受统一的位移、旋转中心与旋转角度控制。  
通常你会在你的 `ModelBase` 的实现中声明一堆 `ModelRenderer` 的字段。这也是为什么会出现 Techne、Tabula 等工具的原因——手写这种东西迟早会累死人。就像下面这样：

```java
public class MyEntityModel extends ModelBase {

    private final ModelRenderer part1;
    private final ModelRenderer part2;
    private final ModelRenderer part3;

    // ...
    // 省略剩下的 ModelRenderer 的声明

    public MyEntityModel() {
        this.part1 = new ModelRenderer(this, ...);
        // 省略 setup
        this.part2 = new ModelRenderer(this, ...);
        // 省略 setup
        this.part3 = new ModelRenderer(this, ...);
        // 省略 setup
    }

    @Override
    public void render(Entity entity, float limbSwing, float limbSwingAmount, float ageInTicks, float netHeadYaw, float headPitch, float scale) {
        // 省略相关 GlStateManager 的调用
        this.part1.render(scale);
        this.part2.render(scale);
        this.part3.render(scale);
        // 省略相关 GlStateManager 的调用
    }

    @Override
    public void setRotationAngles(float limbSwing, float limbSwingAmount, float ageInTicks, float netHeadYaw, float headPitch, float scaleFactor, Entity entity) {
        // 根据实际情况更新数据。
    }
}
```
