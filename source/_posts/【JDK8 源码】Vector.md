title: 【JDK8 源码】Vector
date: 2019-10-22 12:39:00
categories: Java
tags: []
---
# 二、Vector

Vector是jdk1.2的类，比较老旧的一个集合类

> Vector底层也是数组，与ArrayList最大的区别就是：**同步(线程安全)**

Vector是同步的![1571558154066.png](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2019/10/3082567298.png)

我们可以看见这几个方法都是synchronized的。

在要求非同步的情况下我们可以用ArrayList来替代Vector

如果想要ArrayList实现同步我们就需要调用collections的方法

> List list = Collections.synchronizedList(new ArrayList(...));

来实现同步



还有一个区别就是

- ArratLIst在不够用的时候实在原来基础上拓展0.5倍，而Vector是一倍。

```java
 private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

## 总结

- 底层是数组，现在很少用，已经被ArrayLIst替代，原因有两个
  - Vector的所有方法都是同步的，有性能损失
  - Vector出事话length是10超过length是会以100%的比率增加，相比ArrayList更加小号内存