## `FluidStack`

与 ItemStack 相仿，`FluidStack` 代表特定数量的特定流体，有时也会持有特定 NBT 数据。和 `ItemStack` 不一样的地方在于，`FluidStack` 持有的 `Fluid` 引用不能是 `null`。`FluidStack` 可以是 `null`，代表空流体。

```java
// null 代表空流体。
FluidStack someFluid = null;

// 1000 mB 的水。
FluidStack anotherFluid = new FluidStack(FluidRegistry.WATER, 1000);

// 会抛出 IllegalArgumentException。
// 使用没有注册的流体也会导致其抛出 IllegalArgumentException。请务必使用注册过的流体，不论注册成功与否。
// FluidStack error = new FluidStack(null, 1000); // ERROR

// 获取流体类型的访问器。
Fluid theType = anotherFluid.getFluid();

// 出于某种原因，流体数量没有访问器，这个字段是公有的。
int size = anotherFluid.amount;
```

### 不寻常的 `equals`

`FluidStack` 覆写了 `Object.equals` 方法，但它只比较 `Fluid` 和 NBT 数据，不比较数量。[但出于某个未知原因][ref-1]，`FluidStack` 覆写的 `Object.hashCode` 方法中，代表流体数量的 `FluidStack.amount` 字段也参与了散列码的计算。


[ref-1]: https://github.com/MinecraftForge/MinecraftForge/pull/5272
