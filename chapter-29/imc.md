## 跨 Mod 通信（Inter-Mod Communication，IMC）

<!-- Translated and adapted from https://github.com/MinecraftForge/Documentation/pull/193 -->

FML 提供了一套基于“发信和收信”的跨 Mod 间的通信系统（Inter-Mod Communication），通称 IMC。使用这套系统可以在不硬依赖某个 Mod 的代码的情况下，和其他 Mod 通过这种不与具体代码耦合的形式来实现互相“兼容”。

### 五种类型的消息

  * `String`
  * `ItemStack`
  * `NBTTagCompound`
  * `ResourceLocation`
  * `java.util.function.Function<?, ?>`
    * 发送的信息是实现了 `Function<?, ?>` 的类的 Canonical Name。
    * 因为各种原因，FML 没有办法保存下来这个 `Function<?, ?>` 的真正类型信息，使用时也是使用的 unchecked cast，一切全靠 Modder 之间的约定。

### 发送消息

一般来说消息是在 FML 加载的生命周期中进行的。只要是在 `FMLInterModComms.IMCEvent` 发布之前发送 IMC 消息即可。一般情况下，通常会选择 `FMLPreInitializationEvent` 或 `FMLInitializationEvent` 完成。  
发送消息并不需要额外的 `Loader.isModLoaded` 的检查——因为 IMC 系统自己就会在存储消息前执行这个检查，收信的 Mod 不存在时就不会继续存储消息。所以直接发送消息即可，无用的数据自然会被回收掉的。

```java
// Sending a String message
FMLInterModComms.sendMessage(modid, key, stringPayload);
// Sending an ItemStack message
FMLInterModComms.sendMessage(modid, key, itemStackPayload);
// Sending a NBTTagCompound message
FMLInterModComms.sendMessage(modid, key, tagPayload);
// Sending a ResourceLocation message
FMLInterModComms.sendMessage(modid, key, resourceLocationPayload);
// Sending a function message
FMLInterModComms.sendFunctionMessage(modid, key, functionClassCanonicalName);
```

虽然不常见，但在 `FMLInterModComms.IMCEvent` 发布之后仍然可以发送所谓的“运行时消息”：

```java
// Sending a String message
FMLInterModComms.sendRuntimeMessage(modid, key, stringPayload);
// Sending an ItemStack message
FMLInterModComms.sendRuntimeMessage(modid, key, itemStackPayload);
// Sending a NBTTagCompound message
FMLInterModComms.sendRuntimeMessage(modid, key, tagPayload);
// Sending a ResourceLocation message
FMLInterModComms.sendRuntimeMessage(modid, key, resourceLocationPayload);
// Sending a function message
FMLInterModComms.sendRuntimeFunctionMessage(modid, key, functionClassCanonicalName);
```

### 接收消息

```java
@Mod.EventHandler
public void onIMCMessageDistributed(FMLInterModComms.IMCEvent event) {
    List<FMLInterModComms.IMCMessage> messages = event.getMessages();
    for (FMLInterModComms.IMCMessage message : messages) {
        if (message.isStringMessage()) {
            String stringData = message.getStringValue();
            // Do further process
        }
        if (message.isItemStackMessage()) {
            ItemStack itemStackData = message.getItemStackValue();
            // Do further process
        }
        if (message.isNBTMessage()) {
            NBTTagCompound nbtData = message.getNBTValue();
            // Do further process
        }
        if (message.isResourceLocationMessage()) {
            ResourceLocation resourceLocation = message.getResourceLocationValue();
            // Do further process
        }
        if (message.isFunctionMessage()) {
            Optional<Function<Foo, Bar>> function = message.getFunctionValue(Foo.class, Bar.class);
            if (function.isPresent()) {
                // Do further process after presence check
            }
        }
    }
}
```
