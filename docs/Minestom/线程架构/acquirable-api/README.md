---
description: ������ΰ�ȫ�ػ�ȡ����
sidebar_position: 1
---
# Acquirable API

### Presentation

`Acquirable<T>`����һ���ɻ�ȡ���̰߳�ȫ�Ļ�ȡ��ʽ��ȷ����`T`���Ͷ���

�ٸ����ӣ�����һ��������ʵ�壬����֮��ľ����Զ������ڲ�ͬ���߳��н��е��ȡ�����ʵ��A��Ҫ��ʵ��B������Ʒ��������Ҫͬ��������ȷ�����׳ɹ�����ʵ��A���߳��У������Լ�������ʵ��B��`Acquirable<Entity>`��Ȼ��������ȡ�����Ա��ڲ�ͬ���߳��а�ȫ�ط���ʵ�塣

������API������ŵ�:

* ͬ�����̰߳�ȫ�Ĵ���
* ��������ÿ������һ���̻߳�������������һ���̣߳�������һ����
* ���õع�������������ҵ�����ƿ��

acquirable API�ľ��������������:

```java
Acquirable<Entity> acquirableEntity = ...;
System.out.println("Start acquisition...");
acquirableEntity.sync(entity -> {
    // You can use "entity" safely in this consumer!
});
System.out.println("Acquisition happened successfully");
```


`Acquirable#acquire`����`acquirableEntity`�ɻ�ȡ֮ǰ������ǰ�̣߳���ȡ����**ͬһ�߳�**��ִ�лص���

���**�ص���ͬһ�߳���ִ��**����º���Ҫ������Acquirable API�İ������ڣ���Ĵ��벻��Ҫ���κθĶ�����Ӧ���̡߳�

Ȼ���ص��е�ʵ�����**ֻ��**�ڻص���ʹ�á�����ζ���������Ҫ�ڻص�֮�Ᵽ��ʵ������Ա����������Ӧ��ʹ��acquirable��������ǻ�ȡ��ʵ�����

```java
    private Acquirable<Entity> myEntity; // <- GOOD
    // private Entity myEntity <- NO GOOD, DONT DO THAT

    public void randomMethod(Acquirable<Entity> acquirableEntity) {
        this.myEntity = acquirableEntity;
        acquirableEntity.sync(entity -> {
            // "myEntity = entity" ����ȫ, ��Զ����Acquirable�����ǻ�ȡ�Ķ���
        });
    }
```

���ڣ�����㲻��Ҫͬ����ȡ�������ѡ�񴴽������Ժ���������tick����ʱ����Ϊ����ֻ��Ҫ������(schedule)����Ļ�ȡ������

```java
Acquirable<Entity> acquirableEntity = getAcquiredElement();
System.out.println("Hey I am starting the acquisition");
acquirableEntity.async(entity -> {
    System.out.println("Hallo");
});
System.out.println("Hey I scheduled the acquisition");
```

�������:

```
Hey I am starting the acquisition
Hey I scheduled the acquisition
Hallo
```

����һЩ����ѡ��:

```java
Acquirable<Player> acquirable = getAcquirable();

// #local() ֻ�ڶ�����ڵ�����»�ȡ��
// ��ǰ�߳�
Optional<Player> local = acquirable.local();
local.ifPresent(player -> {
    // Run code...
});

// #lock() ����ȡ��������������֮�����������ʱ����
// ������ò��� #sync()
Acquired<Player> acquiredPlayer = acquirable.lock();
Player player = acquiredPlayer.get();
// Run code...
acquiredPlayer.unlock();
```

### Acquirable Collections

��ô��������һ��`AcquirableCollection<Player>`������Ҫ**��ȫ��**������������������ҡ����ж��ָ������ӵĽ��������

#### Naive loop

���ʼ�뵽��Ҳ����:

```java
// NAIVE ACQUIRABLE LOOP
AcquirableCollection<Player> acquirablePlayers = getOnlinePlayers();
for(Acquirable<Player> acquirablePlayer : acquirablePlayers){
    acquirablePlayer.sync(player -> {
        // Do something...
    });
}
```

���ȷ���Թ���������Ч�ʺܵͣ���Ϊ����Ҫһ��һ���ػ�ȡÿ��Ԫ�ء�

#### AcquirableCollection#forEachSync

���Ǳ�����������Ч�ķ������ص���Ϊÿ��Ԫ��ִ��һ�Σ����ҽ�������Ԫ�ض�����ȡ��Ż�ֹͣ��

```java
AcquirableCollection<Player> acquirablePlayers = getOnlinePlayers();
acquirablePlayers.acquireSync(player -> {
    // Do something...
});
acquirablePlayers.acquireAsync(player -> {
    // Do something async...
});
```

����Ӧ����㷺��һ�֣���Ϊ��������ǰ�����Щ������������̫��Ŀ������ص��е�Ԫ�ػ�ֱ���ͷţ������صȴ�����Ԫ�ء�

### �ڲ�ʹ��acquire�������ֱ�ӻ�ȡ������

��ʹ��֪��������ʲô���������ص�Ҳ�����ġ���Ҳ����ֱ�ӽ⿪acquirable��������ȡ�����ֵ��

```java
Acquirable<Entity> acquirableEntity = ...;
Entity entity = acquirableEntity.unwrap();
```

����Ҳ�����Ƶķ���:

```java
AcquirableCollection<Player> acquirablePlayers = getOnlinePlayers();
Stream<Player> players = acquirablePlayers.unwrap();
```

:::caution

��Щ�����̰߳�ȫ�Ĳ�����ȷ�����Ķ���[�̰߳�ȫ](../�̰߳�ȫ.md)ҳ����������еĺ��塣

:::

�Ƽ���ʹ����Щ����ȫ�����ĵط����ע�ͣ���˵��Ϊʲô�����������Σ��Ӧ�õİ�ȫ�ԡ�������Ҳ����κ����ɣ�����ܲ�Ӧ����������

### ��ȡ��ǰ�߳�����ʵ��

```java
Stream<Entity> entities = Acquirable.currentEntities();
```

### �����������ͺͷ������͵�ѡ��

��Ȼ����Ȩ������ϲ��������������Ϊһ�������Ӧ�÷���`Acquirable<T>`��������`T`��

���ֹ��򱳺��ԭ���ǣ������ȷ��������ʹ�ò���ȫ�ķ������㽫��ȫ�ط��ʸ����Ĳ����������㲻֪���û����㷵�غ����Ҫ��ʲô��