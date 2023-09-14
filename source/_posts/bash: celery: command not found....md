title: bash: celery: command not found...
date: 2021-09-26 01:41:14
categories: cuda
tags: []
---
# bash: celery: command not found...

今天登陆到远程的超算，不知道咋了就不能用celery了，可能管理员改了什么乱七八糟的东西。百度搜索一堆都不知道在讲什么，最后在StackOverflow上找到了相关的解决方案。

[can't find the command ’celery‘ - Stack Overflow](https://stackoverflow.com/questions/50113795/cant-find-the-command-celery)

我简单的描述下修复的过程。

原因是由于安装的celery是在

```bash
~/.local/bin
```

目录下，但是当前目录不在path中，所以会出现这样的情况。如果你和我一样没有权限更改系统文件，可以采用以下方法。

```bash
export $PATH:~/.local/bin
```

这个方法是暂时的，重启会失效。

```bash
nano ~/.bashrc
~/.local/bin
```

修改bashrc把这个目录加入即可

第三种是有权限的话可以修改/etc/profile文件