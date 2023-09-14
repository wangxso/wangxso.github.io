title: 【JVM系列】01-JVM简介
date: 2020-04-07 14:45:00
categories: JVM
tags: []
---
#JVM简介
##一、什么是JVM？
   JVM(Java Virtula Machine)，Java虚拟机的缩写，是执行字节码文件的一个虚拟的计算机设备。
   JVM的出现早于Java语言的出现，并且在JDK7之后JVM可以运行非Java语言编写的字节码文件。
##二、JVM的结构
![Screenshot_20200407_221637.jpg](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2020/04/1375290828.jpg)

#### JVM的整体结构包括：
**上层：类文件、类加载子系统**

**中层：方法区、堆、程序计数器、本地方法栈、JVM虚拟机**

**下层：执行引擎、本地方法接口**
###三、Java代码的执行过程
 1.Java源码(xxx.java)导入
 2.通过Java编译器编译为字节码文件(xxx.class)
 3.进入JVM虚拟机执行
 4.通过类加载器加载
 5.通过字节码校验器校验是否正确
 6.通过JIT编译器翻译字节码
 
 
## 四、JVM的架构模型
通常的架构分为**基于栈的指令集**架构和**基于寄存器的指令集**架构
1.基于栈的指令集合：
 - 优点：跨平台，指令集小，指令多
 - 缺点：执行性能比寄存器差

2.基于寄存器的指令集合：
 - 优点：执行性能高，运行更快
 - 缺点：可移植性差
 
 
Hotspot是基于栈的指令集的架构
 - 0地址指令：无地址位
 - 1地址指令：有一个地址位
 
## 五、JVM的生命周期
1. **虚拟机的启动**
Java虚拟机的启动是通过**类加载器**(Bootstrap Class Loader)创建一个**初始类**(Initial Class)来完成的，这个类有虚拟机的具体情况指定。
2. **虚拟机的执行**
一个运行中的Java虚拟机有一个明确的目的就是执行Java程序
3. **虚拟机的退出**
1）程序执行结束
2）在执行的过程中出现未处理的异常或错误而终止
3）操作系统出现错误使Java虚拟机进程终止
4）某线程调用Runtime类或System类的exit方法，或Runtime类的halt方法，并且Java安全管理器也允许这次操作

## 六、Java的发展过程
1.Sum Classic VM
&nbsp;&nbsp;&nbsp;只提供了解释器，外挂JITJDK4后被淘汰
2.Exact VM
&nbsp;&nbsp;&nbsp;准确的内存管理和他的名字一样，可知内存中某个位置的数据类型
3.Hotspot VM
通过计数器找到最具有价值的代码，触发即时编译器或者栈上替换，Hotspot就代表热点代码探测技术，编译器和解释器协调工作，可以在响应速度和执行时间之间取得一个平衡。
4. BEA JRockit
专注于服务器端，号称世界上最快的JVM
5.IBM J9
有影响力的三大商用虚拟机，多用途JVM
6.KVM和CDC/CLDC Hotspot
KVM简单轻量，高度移植性
7.Azul VM
软硬件结合，性能更加优秀
。。。。