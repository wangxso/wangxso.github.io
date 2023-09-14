title: 【读书笔记】【深入理解Java虚拟机】垃圾搜集器与内存分配策略
date: 2021-10-19 08:54:00
categories: jvmbook
tags: []
---
# 二、垃圾搜集器与内存分配策略

## 1、如何判断对象是否死亡？

### 1.1 引用计数法

#### 1.1.1 基本算法

​	给对象添加一个引用计数器，每当有一个地方引用它时，计数器值就加1；当引用失效时，计数器就减1；任何时刻计数器为0的对象就是不可能再被使用的对象也就是**“对象已经死亡”**（一般人对于引用计数法的理解）

#### 1.1.2 缺陷

最大的缺陷就是很难解决对象之间的相互循环引用的问题。举个例子下面代码中的testGC()方法：对象

```java
package wang.wangx.jvm.gc;

/**
 * testGC() 方法执行完后，objA和objB会不会被GC呢？
 * @author wangxs
 */
public class ReferenceCountingGC {
    public Object instance = null;
    private static final int _1MB = 1024 * 1024;
    /**
     * 这个成员的唯一作用就是占内存，以便于GC日志中能清楚看到是否被回收
     */
    private byte[] bigSize = new byte[2 * _1MB];

    public static void testGC() {
        ReferenceCountingGC objA = new ReferenceCountingGC();
        ReferenceCountingGC objB = new ReferenceCountingGC();
        objA.instance = objB;
        objB.instance = objA;

        objA = null;
        objB = null;

        System.gc();
    }
}

```

运行结果如下

> [GC (System.gc()) [PSYoungGen: 8002K->888K(75776K)] 8002K->896K(249344K), 0.0010171 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
> [Full GC (System.gc()) [**PSYoungGen: 888K->0K(75776K)**] [ParOldGen: 8K->611K(173568K)] 896K->611K(249344K), [Metaspace: 3262K->3262K(1056768K)], 0.0038091 secs] [Times: user=0.01 sys=0.00, real=0.00 secs] 
> Heap
>  PSYoungGen      total 75776K, used 1951K [0x000000076be00000, 0x0000000771280000, 0x00000007c0000000)
>   eden space 65024K, 3% used [0x000000076be00000,0x000000076bfe7c68,0x000000076fd80000)
>   from space 10752K, 0% used [0x000000076fd80000,0x000000076fd80000,0x0000000770800000)
>   to   space 10752K, 0% used [0x0000000770800000,0x0000000770800000,0x0000000771280000)
>  ParOldGen       total 173568K, used 611K [0x00000006c3a00000, 0x00000006ce380000, 0x000000076be00000)
>   object space 173568K, 0% used [0x00000006c3a00000,0x00000006c3a98de0,0x00000006ce380000)
>  Metaspace       used 3279K, capacity 4496K, committed 4864K, reserved 1056768K
>   class space    used 355K, capacity 388K, committed 512K, reserved 1048576K

可以看到一次full gc后从888k->0k，意味着Java虚拟机并没有因为两个对象互相引用而就不回收它们，这也就侧面的反映了虚拟机不是采用引用计数法来判断对象是否存活的。

### 1.2 可达性分析算法

#### 1.2.1 基本算法

​		通过一系列的称为“GC Roots”的对象作为起始点，从这些结点往下搜索，搜索所走过的路径称为引用链(Reference Chain)，当一个对象到GC Roots没有任何引用链相连，也就是GC Roots到这个对象不可达，则证明这个对象不可用("已经死亡")。

![可达性分析示意图](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/img/image-20211019160640626.png)

#### 1.2.2 可作为GC Roots的对象

- 虚拟机栈(栈帧中的本地变量表)中引用的对象
- 方法区中静态属性引用的对象
- 方法区中常量引用的对象
- 本地方法栈中JNI（即一般说的Native方法）引用的对象

一般来说这些对象都具有一定的稳定性，一般不会被丢弃使用

### 1.3 为什么会出现强引用、软引用等

在JDK1.2以前，Java对于引用的定义很传统：如果reference类型的数据中存储的数值代表的是另一块内存的起始地址，就称为这块内存代表了一个引用，这种定义很纯粹但是很狭隘，一种对象在这种定义下只有引用和没有被引用两种状态，对于那种“食之无味，弃之可惜“的对象就显得有点无能为力了。在内存很紧张的时候可以适当的抛弃一些不太重要的对象。

自JDK1.2以后Java就对引用的概念进行了扩充，将引用分为

- 强引用(Strong Reference)：就是指在程序代码之中普遍存在的，类似“Object obj = new Object()”这类引用，**只要强引用在，垃圾回收器永远不会回收掉被引用对象**
- 软引用(Soft Reference)：用来描述一些还有用，但是非必须的对象。软引用对象在系统将要发生内存溢出异常之前，将会把这些对象列入回收范围之中进行第二次回收。
- 弱引用(Weak Reference)：描述非必须对象，但是他的强度比软引用还要更弱一些，被弱引用关联的对象只能生存到下一次垃圾收集发生之前。无论内存是否不足都会被回收 
- 虚引用(Phantom Reference)：又称幽灵引用或幻影引用，它是最弱的一种引用关系。一个对象是否有虚引用的存在，完全不会对其生存时间构成影响，也无法通过虚引用来取得一个对象实例。为一个对象设置虚引用只有一个目的那就是能在对象被回收的时候收到一个通知。



**下一次我们谈谈垃圾回收算法**