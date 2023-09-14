title: 如何让mininet在M1系列的Macbook上运行？
date: 2022-04-23 05:43:00
categories: zczb
tags: []
---
## 一、你可以尝试在Docker上运行或者自己构建镜像，已知的是mininet在Ubuntu 20.04 LTS arm64 版本上是由相关的包存在的，你也可以选择在github上面下载。

## 二、使用虚拟机安装

你有两种选择：

- Parallels Desktop for ARM 收费
- [Vmware Fusion Player](https://customerconnect.vmware.com/downloads/get-download?downloadGroup=FUS-PUBTP-2021H1) 个人免费

1. 下载安装虚拟机，具体步骤按不同而定吧

2. 安装完成后下载[Ubuntu 20.04 LTS arm64]([Ubuntu for ARM | Download | Ubuntu](https://ubuntu.com/download/server/arm))的镜像，如果你用的PD的话就可以直接选择下载默认的镜像。

3. 安装Ubuntu，这个没什么其他的操作了。

4. 安装完成后我们修改ubuntu的镜像源

   ```bash
   sudo mv /etc/apt/sources.list /etc/apt/sources.list.bak
   sudo nano /etc/apt/sources.list
   
   # 然后加入一下内容
   # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal main restricted universe multiverse
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-updates main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-updates main restricted universe multiverse
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-backports main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-backports main restricted universe multiverse
   deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-security main restricted universe multiverse
   # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-security main restricted universe multiverse
   
   ```

5. 安装mininet

   ```bash
   sudo apt install mininet
   sudo mn --version
   2.2.2
   ```

6. 安装其他软件

   ```bash
   sudo apt install iperf3 wireshark hping3 -y
   ```

7. 配置X11转发

   ```bash
   sudo nano /etc/ssh/sshd_config
   
   CTRL + W 搜索 X11
   X11Forwarding yes
   X11DisplayOffset 10
   
   搜索ROOT
   改成
   # 这是为了等下登录可以使用root账号登录
   PermitRootLogin yes
   
   CTRL + o 保存
   CTRL + x 退出
   
   sudo service sshd restart
   
   sudo su
   
   passwd
   
   ```

8. Mac端

   ```bash
   brew install XQuartz
   sudo vim /etc/ssh/ssh_config
   ForwardX11 yes
   # 重启下mac
   sudo reboot
   ```

9. 然后就可以连接了

   ```bas
   ssh -AY root@192.168.100.1
   sudo mn
   xterm h1
   ```

   

