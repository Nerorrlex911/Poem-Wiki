---
description: >-
  使用AnvilLoader加载世界
sidebar_position: 1
---

## 使用AnvilLoader加载世界

使用`InstanceContainer#setChunkLoader(IChunkLoader)`函数加载世界到实例中。

以下是加载世界的示例代码:
```java
InstanceContainer.setChunkLoader(new AnvilLoader("worlds/world"));
```

这会将`worlds/world`目录中的世界加载到`InstanceContainer`中，你仍可以像以前一样使用实例，但是世界已经加载进来了。

世界的加载只需要`/region`文件夹，因为它包含了方块数据。

## 保存世界

当你应用AnvilLoader将世界加载到实例后，保存世界可以使用`InstanceContainer#saveChunksToStorage()`函数。

