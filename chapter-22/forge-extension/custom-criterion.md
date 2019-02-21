## 自定义进度触发条件

进度触发条件（Criterion Trigger）代表了解锁进度的某一个具体条件。一个进度的解锁可能会需要满足一个触发条件，或是多个触发条件的组合。虽然原版提供了相当丰富的触发条件，但我们仍然会不可避免地遇到需要自定义触发条件的情况。不过，写一个新的触发条件其实并不简单。一个完整的 `ICriterionTrigger<? extends ICriterionInstance>` 的实现大约长这样：

```java
import net.minecraft.advancements.ICriterionTrigger;
import net.minecraft.advancements.PlayerAdvancements;
import net.minecraft.advancements.critereon.AbstractCriterionInstance;

public final class CustomTrigger implements ICriterionTrigger<CustomTrigger.Instance> {

    // 用作该 trigger 的唯一 ID
    private static final ResourceLocation ID = new ResourceLocation("my_mod", "custom");

    // 集中管理所有进度用到的该触发条件的具体实例
    private final Map<PlayerAdvancements, Set<Listener<CustomTrigger.Instance>>> listeners = new HashMap<>();

    /*
     * 该 Criterion 唯一 ID 的访问器
     */
    @Nonnull
    @Override
    public ResourceLocation getId() {
        return ID;
    }

    /*
     * 加入新的监听器
     */
    @Override
    public void addListener(PlayerAdvancements playerAdvancements, Listener<CustomTrigger.Instance> listener) {
        this.listeners.computeIfAbsent(playerAdvancements, advancements -> new HashSet<>()).add(listener);
    }

    /*
     * 清除不用的监听器，避免占用资源。由 Minecraft 内部统一管理。
     */
    @Override
    public void removeListener(PlayerAdvancements playerAdvancements, Listener<CustomTrigger.Instance> listener) {
        Set<Listener<CustomTrigger.Instance>> listeners = this.listeners.get(playerAdvancements);
        if (listeners != null) {
            listeners.remove(listener);
            if (listeners.isEmpty()) {
                this.listeners.remove(playerAdvancements);
            }
        }
    }

    /*
     * 直接取消对某个玩家进度的跟踪
     */
    @Override
    public void removeAllListeners(PlayerAdvancements playerAdvancements) {
        this.listeners.remove(playerAdvancements);
    }

    /*
     * 抽象工厂方法，实际上是反序列化器
     */
    @Override
    public CustomTrigger.Instance deserializeInstance(JsonObject json, JsonDeserializationContext context) {
        return new Instance(...);
    }

    /*
     * 该触发器的入口，需要在合适的时间与位置调用
     */
    public void trigger(EntityPlayerMP player, Context context) {
        PlayerAdvancements advancements = player.getAdvancements()
        HashSet<Listener<Instance>> listeners = this.listeners.get(advancements);
        if (listeners != null) {
            List<Listener<Instance>> triggered = new ArrayList<>();
            for (Listener<CustomTrigger.Instance> listener : listeners) {
                if (listener.getCriterionInstance().test(context)) {
                    triggered.add(listener);
                }
            }
            for (Listener<CustomTrigger.Instance> listener : triggered) {
                listener.grantCriterion(advancements);
            }
        }
    }

    /*
     * 抽象工厂方法生产出的触发器实例。不能为 private，否则 outer class 类声明中接口的
     * 泛型参数看不到这个类型。
     * 此外，ICriterionTrigger 的类型参数的边界实际上是 extends ICriterionInstance，
     * 这里使用 AbstractCriterionInstance 是为了复用一些 boiler plate code（toString
     * 啦这样的）。
     */
    static final class Instance extends AbstractCriterionInstance {
        Instance(...) {
            // 进行必要的初始化操作
            // AbstractCriterionInstance 的构造器要求传入的是这个进度唯一的
            // 识别 ID，即 ICriterionTrigger.getId() 的返回值。
            super(CustomTrigger.ID)
        }

        /*
         * 每一个触发器实例都有其独立的判据。
         */
        boolean test(Context context) {
            return false;
        }
    }
}
```

> 天知道 Dinnerbone 写出这个东西的时候在想什么。

简单来说，一个新的 Criterion 分成这两个部分：

  1. `ICriterionTrigger<? extends AbstractCriterionInstance>` 的实现。每一个它的实现的实例代表了一大类具体的条件，比如说，对于 `minecraft:consume_item`，你可以通过指定不同的物品来获得不同的条件，但这些不同的条件都属于 `minecraft:consume_item` 这个类型。换言之，这是享元模式（Flyweight pattern）中的共用对象（Flyweight），主要负责管理所有具体的触发条件实例，同时兼职抽象工厂（见 2.）。
  2. `ICriterionInstance` 的实现。它代表了具体的一个条件，即便识别 ID 和别的条件一致，其具体判断细节也未必相同。它的实例由一个抽象工厂产出，而这个抽象工厂正是由对应的 `ICriterionTrigger` 兼职的（即 `deserializeInstance` 方法）。

而使用这个新的 Criterion 的方式是这样的：

  1. 首先注册 `ICriterionTrigger` 的实例。通过原版的 `CriteriaTriggers.register` 完成注册。这个方法实际上是被 Forge patch 过的，在 patch 之前它不能正确处理 ID 中的 namespace 不是 `minecraft` 的 criterion。这个方法返回被注册的 criterion，所以你可以写：
  ```java
  CustomTrigger TRIGGER = CriteriaTriggers.register(new CustomTrigger());
  ```
  2. 在适当的时候调用 `TRIGGER.trigger(player, ...)`，其中 `player` 是玩家实体，`...` 代表你需要的上下文信息。玩家实体的作用是提供 `PlayerAdvancements` 对象。上文中的示例代码使用了 `Context`——这是一个假想的 parameter object，在这里充当上下文的容器。
  3. `TRIGGER.trigger(player, ...)` 查表获得对应的 `Set<Listener<? extends ICriterionInstance>>`，若存在则遍历所有已知的 `ICriterionInstance`，并根据上下文过滤出所有满足触发条件的 `Listener<? extends ICriterionInstance>`。然后对所有满足条件的 `Listener<? extends ICriterionInstance>` 对象调用 `grantCriterion(PlayerAdvancements)` 方法。上文中示例代码之所以使用了单独的 `List` 是因为 Minecraft 会在 `grantCriterion` 被调用时清理不再使用的 `ICriterionInstance`，若处理不当，极易导致 `ConcurrentModificationException` 出现。<!-- TODO：照这么说那个 HashMap 可以改用 IdentityHashMap，直接使用 == 比较，绕开 Object.equals？-->

### 真实案例：右击方块时触发的 criterion

【施工中】
