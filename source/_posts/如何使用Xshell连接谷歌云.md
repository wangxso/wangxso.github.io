title: 如何使用Xshell连接谷歌云
date: 2021-11-08 08:50:22
categories: zczb
tags: []
---
# 如何使用Xshell连接谷歌云

### Step1 打开谷歌云的SSH自带的工具(网页端)

![image-20211108164020103](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/img/image-20211108164020103.png)

### Step2 切换到root权限

```bash
sudo -i
```

### Step3 修改Root密码

```bash
passwd root
# enter password....
```

### Step4 修改ssh配置文件(这里特别注意修改的是sshd_config而不是ssh_config)

```bash
vi /etc/ssh/sshd_config
```

修改PermitRootLogin和PasswordAuthentication为yes

```bash
# Authentication:
PermitRootLogin yes //默认为no，需要开启root用户访问改为yes

# Change to no to disable tunnelled clear text passwords
PasswordAuthentication yes //默认为no，改为yes开启密码登陆

```

### Step5 重启ssh服务

```bash
service sshd restart 
# 或 
/etc/init.d/ssh restart
```

### Step6 使用Xshell登录

![image-20211108164808214](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/img/image-20211108164808214.png)

