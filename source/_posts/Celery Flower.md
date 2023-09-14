title: Celery Flower
date: 2021-08-07 10:25:08
categories: bxjs
tags: []
---
# Celery Flower
在看教程的时候遇到一个问题，启动flower的时候一直都是amqp也就是rabbitmq是flower的默认配置，仔细阅读了报错发现

```bash
 celery flower --broker=redis://localhost:6379/1
```



> You have incorrectly specified the following celery arguments after flower command: ['--broker']. Please specify them after celery command instead following this templat e: celery [celery args] flower [flower args].

大概的意思就是--broker需要跟在celery命令的构面而不是flower后面。

改成

```bash
celery --broker=redis://localhost:6379/1 flower
```

当前的flower版本是

> flower===1.0.0
>
> python ===3.6

