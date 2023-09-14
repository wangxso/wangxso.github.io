title: 【JVM系列】02-类加载子系统
date: 2020-04-08 14:24:12
categories: JVM
tags: []
---
#类加载子系统
## 一、类加载器和类加载过程
1. 从文件或者网络中加载Class文件，Class文件开头具有特性的标识。
![Screenshot_20200408_214835.jpg](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2020/04/2493225977.jpg)

2. 类加载的过程
1）. 加载
 - 通过一个类的全限定名获取定义此类的二进制字节流
 - 将这个字节流所代表的静态存储结构转化为方法去的运行时数据结构
 - <font color=red>在内存中生成一个代表这个类的java.lang.class对象</font>作为方法去这个类的各种数据的访问入口


2）.链接
 - 验证（verify）：保证Class的正确性，均由CAFEBABE开头
 - 准备：为类变量分配内存，并且设置变量默认初始值
ps：这里不包含final修饰的static变量。因为他们在编译的时候就被分配了内存，准备阶段会显式初始化。
ps2:这里不会为实际变量初始化，实际变量和对象一起在堆中进行内存分配

 - 解析 将常量池内的符号引用转换为直接引用
 
 3）.初始化
 - 初始化阶段就是执行类构造器方法<clinit>()的过程
 - 构造指令由出现顺序执行
 
 
 <font color=red>非法的前项引用，无static不会生成<clinit>方法</font>
 <init>为构造器，任何一个类至少有一个构造器，虚拟机必须保证一个类的<clinit>()方法在多线程下是同步加锁的。
 
 ## 二、类加载器的分类
 1.启动类加载器(BootstrapClassLoader)
   - 这个类加载器使用<font color=red>C/C++</font>编写，嵌套在JVM内部，用来加载Java核心库，并不继承自java.lang.ClassLoader,无父加载器
 2. 拓展类加载器(ExtClassLoader)
  - Java编写，派生自ClassLoader类，从java.ext.dirs或jre/lib/ext中加载
 3. 系统类加载器(AppClassLoader)
 - 默认的类加载器，父类为拓展类加载器，从CLASSPATH中加载
 4. 用户自定义类加载器
 在必要时才需要比如:①隔离加载类②修改加载方式③拓展加载源④防止源码泄露（加密，解密）
 写一个用户自定义加载类的方法：
 step1 继承ClassLoader类
 step2 重写findClass方法
 ps若无特殊情况可以继承URLClassLoader
 
 ## 三、双亲委派机制
 作用：①避免类重复加载②保护程序安全，防止核心api被篡改。（引导类加载器会优先加载jdk的api）
 过程：
 ![Screenshot_20200408_222135.jpg](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2020/04/2447644365.jpg)
 类加载器收到加载请求会向上委派知道引导类加载器若父加载器能够完成任务则返回结束，若无法完成任务加载，子加载器才会进行加载