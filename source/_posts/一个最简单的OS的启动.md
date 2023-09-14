title: 一个最简单的OS的启动
date: 2021-11-19 09:28:09
categories: dsbj
tags: []
---
https://time.geekbang.org/column/article/369502
https://gitee.com/lmos/cosmos
### 一、下载Virtual Box

[Oracle VM VirtualBox](https://www.virtualbox.org/)

### 二、下载Ubuntu 镜像

[Index of /ubuntu-releases/20.04/ (aliyun.com)](http://mirrors.aliyun.com/ubuntu-releases/20.04/)

### 三、创建虚拟机

#### 1. 点击新建

![image-20211119164837210](https://i.loli.net/2021/11/19/7iF28QaAJbDPu59.png)

#### 2. 填写信息

![image-20211119164955108](https://i.loli.net/2021/11/19/2wXNtbx3K9WP4cM.png)

#### 3.根据你的内存大小选择，我们运行GUI所以给大点

![image-20211119165046447](https://i.loli.net/2021/11/19/s7umRivLS2gZ9lG.png)

#### 4. 创建硬盘(之后默认即可)

![image-20211119165116449](https://i.loli.net/2021/11/19/CYvomt9wDaqWQZd.png)

#### 5. 启动安装Ubuntu

![image-20211119165207555](https://i.loli.net/2021/11/19/vTw7JndZa1IXzRF.png)

![image-20211119165227214](https://i.loli.net/2021/11/19/3YocRi61dLDPAq7.png)

![image-20211119165236028](https://i.loli.net/2021/11/19/czfCO7oesY9FBJS.png)

![image-20211119165251009](https://i.loli.net/2021/11/19/ycPDUvAmsdwt4Xh.png)

#### 6. 安装Ubuntu

![image-20211119165527007](https://i.loli.net/2021/11/19/HrgxvVBmjqXhzcb.png)

你可以选择中文，这样你的apt源就会是中国的，或者选英文之后再改。一路默认即可。

### 三、配置环境

#### 1. 安装nasm

```bash
sudo apt install nasm
```



#### 2. 安装gcc和make

```bash
sudo apt install gcc make -y
```



### 四、编译和配置grub

```bash
make
```

成功后会出现

![image-20211119170055316](https://i.loli.net/2021/11/19/sjNCkKwRzxQ95Jv.png)

```bash
sudo cp HelloOS.bin /boot/
cd /boot/
cd /grub/
```

你会看到**grub.cfg**

```bash
sudo nano grub.cfg
```

添加这个

![image-20211119170523989](https://i.loli.net/2021/11/19/6dOqT5HnvaFeuWA.png)

这里是/dev/sda5 所以要把下面的"hd0, msdos4" -> "hd0, msdos5"

```c
menuentry 'HelloOS' { 
    insmod part_msdos #GRUB加载分区模块识别分区
    insmod ext2 #GRUB加载ext文件系统模块识别ext文件系统
    set root='hd0,msdos4' #注意boot目录挂载的分区，这是我机器上的情况
    multiboot2 /boot/HelloOS.bin #GRUB以multiboot2协议加载HelloOS.bin 
    boot #GRUB启动HelloOS.bin
}
```

![image-20211119171014509](https://i.loli.net/2021/11/19/qlsre4juTWXhQDA.png)

```bash
sudo reboot
```



### 五、错误

发现重启后没有按照grub启动，因为默认启动被隐藏了

```bash
sudo su
cd /etc/default
nano grub
```

#### 1.找到hidden，注释掉

![image-20211119171449596](https://i.loli.net/2021/11/19/7rGdgzKwpWq51Mt.png)

#### 2. 把GRUB_TIMEOUT=0改成30，GRUB_CMDLINE_LINUX_DEFAULT改成text，如下图

![image-20211119171642806](https://i.loli.net/2021/11/19/NiacjxbtJLvSRes.png)

#### 3. 保存，重启

Ctrl + O 保存

Ctrl + X 退出

```bash
sudo update-grub
```

<font color="red">**这里我重启之后发现HelloOS没有，然后发现之前的/boot/grub/grub.cfg里面写的内容消失了，重新写一次就行。**</font>

### 六、成功

![image-20211119172421389](https://i.loli.net/2021/11/19/wUExZvTdLuSq6o2.png)

![image-20211119172458263](https://i.loli.net/2021/11/19/M6qPmFYs7oKbki9.png)