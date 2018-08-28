### 事件概论

#### 什么是事件？

事件是什么？某个时间点发生的有一定关注度的“事情”。

考虑下面的代码：

````java
Teacher teacher = aCertainClass.getInstructor();
teacher.post(homework);
````

教师发布了作业。一个包含有若干“作业”对象的 `List` 分派到了学生手中。  
表面上，到此为止了。事实上，果真只能有这点逻辑吗？发布作业的过程中难道不会发生什么事情吗？比方说，教师是有课代表的：

````java
studentRepresentative = teacher.getRepresentative();
homeworkList = homework.content();
for (entry : homeworkList) {
    if (!studentRepresentative.validate(singleHomework)) {
        studentRepresentative.boardcast("This homework has problem, please notify {} to fix it!", teacher);
    }
}
````

再比如说，有别的老师会关心别的老师的作业，然后忽然决定“今天就少布置一点作业吧”。

````java
if (homeworkList.content().size() > THRESHOLD) {
    myProposedHomework = myProposedHomework.subList(1, 3);
}
````

#### Minecraft 没有事件系统

回到 Minecraft 上来——Modder 有时候希望能干涉原版的机制。  
问题来了，怎么干涉？原版的代码很多地方都是写死的，对于这些地方来说没什么办法。  
举个例子，我想在玩家进入游戏时发出欢迎词，怎么办？原版是有那么一句话但那显然是写死的。

Forge 提供了一个完善的事件系统来解决这个问题。

目前，Forge 有三条事件总线：

  * 一般事件总线
  * 矿物生成总线
  * 地形修饰总线

注意，FML 的那几个用于 Mod 加载的事件不算。那些是 Mod 加载周期的事件，在那个时候 FML 需要使用别的事件总线（具体来说，是 Guava 的事件总线）。

#### 事件订阅
按如下步骤操作：

1. 确定需求，然后找事件
2. 如果找到了对应的事件：

````java
//本方法返回值必须是void
//本方法的参数必须是一个事件，并且作为唯一的参数存在
//本方法可以是静态的，具体差别一会就会出现
@SubscribeEvent
public void onEventFired(Event event) {
    //当然这个Event一定要换成对应的事件。
    //这里之所以写Event，是因为在MinecraftForge事件总线中
    //Event这个类便是所有的事件的父类
    //方法名无所谓，最后都会被ASM成全新的Callback实例
}

//然后注册
MinecraftForge.EVENT_BUS.register(new EventListener());
//如果是静态方法监听，则应注册class
MinecraftForge.EVENT_BUS.register(EventListener.class);

````

以及注意一点，如果你订阅的事件属于矿物生成类的，或是地形生成类的（比如什么`InitNoise`这样的事件，那么请往`ORE_GEN_BUS`或者`TERRAIN_GEN_BUS`注册。

然后——如果你能看到这里：很好，你大概没找到你需要的事件。

  1. 如果你的事件是自己用的，请看下面一小节。
  2. 请仔细思考你的需求。你的需求是不是和原版的什么东西比较像？原版的东西是怎么做的？
  3. 再仔细思考你的需求。你的需求是不是已经有 Mod 实现过了？他们都是利用了什么事件？
  3. 否则你只能给 Forge 发 Pull request 了。

#### 但是我需要给自己的【某种机制】弄一个事件！

````java
// Cancable注解声明此事件可以撤销。不可撤销的事件不要加Cancalable注解。
// 撤销后的事件会直接退出其生命周期，并且不会被后续的Listener影响。
// HasResult注解声明此事件会有“结果”。没有结果的事件不要加HasResult注解。
// Result类位于Event类内部，为枚举类，有三种可能：SUCCESS, FAIL, PASS
//（等等我好像记错了，这好像是控制物品使用的EnumActionResult，不是Result）
@Cancalable
@HasResult
public class MyEvent extends Event {
    public final Object foo;
    //按照惯例，事件中的字段都应该是Final的
    public MyEvent(Object foo) {
        this.foo = foo;
    }
}

// 发布事件。
// 这个post是有返回值，含义如下：
// true 代表事件被取消。事件必须有 @Cancalable 注解才可以被取消，并有可能令 EventBus.post 返回 true。
// false 代表事件成功发布。有 @Cancalable 的事件，且没有被取消时会返回 false。
// 若这个事件无法取消，一定会返回 false。
boolean result = MinecraftForge.EVENT_BUS.post(new MyEvent(aFoo));
````
