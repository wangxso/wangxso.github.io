title: 关于Carla两三点事
date: 2023-07-11 14:48:00
categories: selfdriving
tags: []
---
# Carla

## 1. 介绍

CARLA 是开源的自动驾驶仿真器。他是从零开始构建的，通过模块化和灵活的API来解决一系列与自动驾驶相关的任务。CARLA基于虚幻引擎构建仿真环境，使用OpenDRIVE规范来定义道路和城市环境。对于仿真的控制是通过Python或C++调用API来控制的，随着项目的开发这样的API会越来越多。

### 仿真器

CARLA由一个可伸缩的主从架构组成。

服务端负责所有与模型相关的东西，包括传感器渲染，物理学的计算，世界状态和行为的更新等。

### 存在的问题

1. Ubuntu 22.04 由于clang11的原因无法安装Carla。
2. https://github.com/carla-simulator/carla/issues/6381该issue提到的解决方法无效，客户端无法运行。
3. Carla很大注意预留好硬盘空间，大概需要100G左右

4. cannot find -lstdc++
今天重新搞carla的时候遇到了这个问题。先说如何解决:
```shell
clang++ --verbose
```
可以看到如下的输出:
```shell
clang version 10.0.0-4ubuntu1 
Target: x86_64-pc-linux-gnu
Thread model: posix
InstalledDir: /usr/bin
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/10
......
Selected GCC installation:  /usr/bin/../lib/gcc/x86_64-linux-gnu/13
Candidate multilib: .;@m64
Selected multilib: .;@m64
```
**Select GCC XXXXXXXXX u/13**

看到这个数字13了把，你就安装对应版本的libstdc++，如下所示
```shell
sudo apt install libstdc++-13-dev
```

----------------------------
分割线，等我想更新了再写