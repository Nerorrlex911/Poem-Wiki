---
description: 介绍如何安全地获取对象
sidebar_position: 1
---
# Acquirable API

### Presentation

`Acquirable<T>`代表一个可获取但线程安全的获取方式不确定的`T`类型对象。

举个例子，想象一下有两个实体，它们之间的距离很远，因此在不同的线程中进行调度。假设实体A想要与实体B交易物品，这是需要同步操作以确保交易成功。从实体A的线程中，您可以检索包含实体B的`Acquirable<Entity>`，然后从那里获取它，以便在不同的线程中安全地访问实体。

这样的API有许多优点:

* 同步且线程安全的代码
* 无论你是每个区块一个线程还是整个服务器一个线程，代码是一样的
* 更好地管理你的数据以找到性能瓶颈

acquirable API的具体代码是这样的:

```java
Acquirable<Entity> acquirableEntity = ...;
System.out.println("Start acquisition...");
acquirableEntity.sync(entity -> {
    // You can use "entity" safely in this consumer!
});
System.out.println("Acquisition happened successfully");
```


`Acquirable#acquire`会在`acquirableEntity`可获取之前阻塞当前线程，获取后在**同一线程**中执行回调。

理解**回调在同一线程中执行**这件事很重要，这是Acquirable API的奥妙所在，你的代码不需要做任何改动以适应多线程。

然而回调中的实体对象**只能**在回调中使用。这意味着如果你需要在回调之外保存实体对象以便后续处理，你应该使用acquirable对象而不是获取的实体对象。

```java
    private Acquirable<Entity> myEntity; // <- GOOD
    // private Entity myEntity <- NO GOOD, DONT DO THAT

    public void randomMethod(Acquirable<Entity> acquirableEntity) {
        this.myEntity = acquirableEntity;
        acquirableEntity.sync(entity -> {
            // "myEntity = entity" 不安全, 永远缓存Acquirable而不是获取的对象
        });
    }
```

现在，如果你不需要同步获取，你可以选择创建请求并稍后处理它（在tick结束时），为此你只需要“调度(schedule)”你的获取操作。

```java
Acquirable<Entity> acquirableEntity = getAcquiredElement();
System.out.println("Hey I am starting the acquisition");
acquirableEntity.async(entity -> {
    System.out.println("Hallo");
});
System.out.println("Hey I scheduled the acquisition");
```

将会输出:

```
Hey I am starting the acquisition
Hey I scheduled the acquisition
Hallo
```

还有一些可用选项:

```java
Acquirable<Player> acquirable = getAcquirable();

// #local() 只在对象存在的情况下获取他
// 当前线程
Optional<Player> local = acquirable.local();
local.ifPresent(player -> {
    // Run code...
});

// #lock() 给获取操作上锁，并在之后由你决定何时解锁
// 如果你用不了 #sync()
Acquired<Player> acquiredPlayer = acquirable.lock();
Player player = acquiredPlayer.get();
// Run code...
acquiredPlayer.unlock();
```

### Acquirable Collections

那么假如你有一个`AcquirableCollection<Player>`，你想要**安全地**访问它包含的所有玩家。你有多种各有优劣的解决方案。

#### Naive loop

你最开始想到的也许是:

```java
// NAIVE ACQUIRABLE LOOP
AcquirableCollection<Player> acquirablePlayers = getOnlinePlayers();
for(Acquirable<Player> acquirablePlayer : acquirablePlayers){
    acquirablePlayer.sync(player -> {
        // Do something...
    });
}
```

这的确可以工作，但是效率很低，因为你需要一个一个地获取每个元素。

#### AcquirableCollection#forEachSync

这是遍历集合最有效的方法，回调会为每个元素执行一次，当且仅当所有元素都被获取后才会停止。

```java
AcquirableCollection<Player> acquirablePlayers = getOnlinePlayers();
acquirablePlayers.acquireSync(player -> {
    // Do something...
});
acquirablePlayers.acquireAsync(player -> {
    // Do something async...
});
```

这是应用最广泛的一种，因为它不会像前面的那些方法那样创建太多的开销。回调中的元素会直接释放，而不必等待其他元素。

### 在不使用acquire的情况下直接获取对象本身

即使你知道你在做什么，到处叠回调也是糟糕的。你也可以直接解开acquirable对象来获取里面的值。

```java
Acquirable<Entity> acquirableEntity = ...;
Entity entity = acquirableEntity.unwrap();
```

集合也有类似的方法:

```java
AcquirableCollection<Player> acquirablePlayers = getOnlinePlayers();
Stream<Player> players = acquirablePlayers.unwrap();
```

:::caution

这些不是线程安全的操作，确保你阅读了[线程安全](../线程安全.md)页面以理解其中的含义。

:::

推荐在使用这些不安全方法的地方添加注释，以说明为什么这个操作不会危及应用的安全性。如果你找不到任何理由，你可能不应该这样做。

### 获取当前线程所有实体

```java
Stream<Entity> entities = Acquirable.currentEntities();
```

### 方法参数类型和返回类型的选择

显然你有权利按你喜欢的来。但是作为一般规则，你应该返回`Acquirable<T>`对象并请求`T`。

这种规则背后的原因是，你可以确定（除非使用不安全的方法）你将安全地访问给定的参数，但是你不知道用户在你返回后会想要做什么。