title: 【转载】ClassNotFoundException和NoClassDefFoundError的区别
date: 2019-10-22 13:08:00
categories: Java
tags: []
---
作者：Duke2016
　　　　
出处：http://www.cnblogs.com/duke2016/
　　　　
本文版权归作者和博客园共有，欢迎转载，但未经作者同意必须保留此段声明，且在文章页面明显位置给出原文连接，否则保留追究法律责任的权利。

|  ClassNotFoundException |  NoClassDefFoundError |
| ------------ | ------------ |
|  从java.lang.Exception继承，是一个Exception类型 |  从java.lang.Error继承，是一个Error类型 |
| 当动态加载Class的时候找不到类会抛出该异常  |  当编译成功以后执行过程中Class找不到导致抛出该错误 |
| 一般在执行Class.forName()、ClassLoader.loadClass()或ClassLoader.findSystemClass()的时候抛出| 由JVM的运行时系统抛出|


