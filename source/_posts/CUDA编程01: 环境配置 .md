title: CUDA编程01: 环境配置 
date: 2021-08-04 09:30:00
categories: cuda
tags: []
---
# CUDA编程01: 环境配置

## Windows

这里可以先看官方文档

[Installation Guide Windows :: CUDA Toolkit Documentation (nvidia.com)](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html)

### 一、确认你的GPU支持CUDA

你可以在[CUDA GPU | NVIDIA Developer](https://developer.nvidia.com/zh-cn/cuda-gpus)查询

其中有些移动端GPU也支持CUDA但是没有列出如MX系列

### 二、安装CUDA Toolkit 

点击地址[CUDA Toolkit 11.4 Update 1 Downloads | NVIDIA Developer](https://developer.nvidia.com/cuda-downloads)

![image-20210804172115430](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/image-20210804172115430.png)

然后一路Next即可。

打开CMD或者powershell输入

```bash
nvcc -V
```

会显示

![image-20210804172856486](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/image-20210804172856486.png)

即为成功安装。

### 三、安装Visual Studio 2019(其他版本类似)

安装这个主要是为了VC编译器。

如果你在为安装这个的时候使用nvcc命令进行编译会提示缺少cl.exe。



下载VS 2019

[Visual Studio 2019 IDE - 适用于 Windows 的编程软件 (microsoft.com)](https://visualstudio.microsoft.com/zh-hans/vs/)

然后安装

![image-20210804172619892](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/image-20210804172619892.png)

其实我觉得只要我箭头指的那一项就行，但是为了保险起见我全都安装了。

将el.exe的目录加入Path

![image-20210804172535293](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/image-20210804172535293.png)

### 四、大功告成

大功告成，你可以开始进入CUDA编程的世界了。