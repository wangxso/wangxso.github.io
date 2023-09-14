title: 【Java多线程】Volatile
date: 2021-05-22 08:38:00
categories: JUC
tags: []
---
## Volatile

### 一. Volatile的作用

1. 实现内存的可见性
2. 禁止指令重排（内存屏障）

### 二. 如何实现内存的可见性

<img src="https://wangxblog.oss-cn-hangzhou.aliyuncs.com/20210522153930.png" style="zoom:67%;" />

上图显示了计算机中的内存结构，现代计算机采用多级缓存机制。Volatile为了是的保证内存的可见性采用了两种原则：

1. 引起处理器的缓存写回内存。

   - 使得处理器独占(Exclusive)共享内存
   - 一般独占的方式不采用锁总线而采用锁缓存的机制。

2. 一个处理器的缓存回写使得其他处理器的缓存失效。采用MESI（M=Modify，E=Exclusive，S=Shared，I=Invalid）协议来保持处理器的缓存一致性。

   - 处理器会嗅探它的内部缓存和其他处理器的缓存是否一致（通过总线）
   - 如果检测到有处理器写回内存(Modify)并且这个地址处于共享(Shared)状态
   - 那么正在嗅探的处理器会使得这个缓存行无效，在下次要访问这个地址的时候重新从内存中填充到缓存行中。

   

### 三. Volatile的使用优化

在我看来这个使用优化就是因为处理器强制加载缓存行会一次加载一行，而每一行的大小都等于当前处理器的位数(地址总线的数量)，而通过追加字节的方式能够是的一个数据能够在同一个数据行，减少访问缓存的次数从而减少访问带来的开销。

### 四. 如何实现禁止指令重排（内存屏障）

1. 为什么会出现指令重排？

   由于你写的代码并不是一定适合CPU的运行(也就是说你写的代码知识符合了人类的逻辑而不一定符合机器执行的逻辑)，所以在编译的优化阶段会对你的代码进行优化(指令重排)(编译分为词法分析，语法分析，语义分析，优化 ，目标代码生成五个阶段)。编译器和处理器都会生成的中间代码进行优化。

2. 如何实现禁止指令重排？

   首先我们要清除内存屏障的概念，内存屏障又称为内存栅栏，它是一个CPU指令作用是保持特定操作的顺序性和保证某些变量的内存可见性（上面的内存可见性也是通过这个指令来实现的），添加了内存栅栏可以使得在栅栏内的指令不会被CPU或编译器进行重排指令。

   

### 五、Volatile保证原子性吗？

实际上Volatile是无法保证数据的原子性的，比如如下代码

```java
public class TestVolatile {
    public volatile int inc = 0;

    public void increase() {
        inc++;
    }

    public static void main(String[] args) {
        final TestVolatile test = new TestVolatile();
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    test.increase();
                }
            }).start();
        }
	    // 当当前活动线程数小于1的时候结束打印inc的值
        while (Thread.activeCount()>1) {
            Thread.yield();
        }

        System.out.println(test.inc);

    }
}
```

我们会发现这个inc会小于10000，而并不是我们所设想的是10000（也可能是但是一次不是10000就可以反证他不拥有原子性）。虽然volatile会使得进程获得最新的数据但是我们假设以下情景。

当前的inc的值为10，线程1先读取了变量inc的初始值(10),线程1进行自增操作然后线程1被阻塞了；线程2进行自增操作然后线程2也去读取变量inc的原始值，由于线程1只是对原始值进行了读取，未进行修改所以线程2读取到的还是10，自增后还是11。

我们知道缓存行只有发生修改写回才会失效，但是线程1已经被阻塞了所以未写回信息，导致缓存行仍然是10，导致两个线程计算的写回是都是11。

![image-20210522162649111](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/image-20210522162649111.png)

   

   导致这个产生是由于自增本身就不带有原子性，可以采用atomic包里面的进行比如AtomicIntenger等

```java
import java.util.concurrent.atomic.AtomicInteger;

public class TestVolatile {
    public volatile AtomicInteger inc = new AtomicInteger(0);

    public void increase() {
        inc.incrementAndGet();
    }

    public static void main(String[] args) {
        final TestVolatile test = new TestVolatile();
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    test.increase();
                }
            }).start();
        }

        while (Thread.activeCount()>2) {
            Thread.yield();
        }

        System.out.println(test.inc);

    }
}

```





