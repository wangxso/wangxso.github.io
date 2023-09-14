title: 【Go】Import cycle not allowed in test
date: 2021-11-12 07:34:57
categories: zczb
tags: []
---
  今天写测试文件的时候发现的一个错误，，当我在wangxsrpc/codec 中引用wangxsrpc包的时候出现了该错误
项目结构图如下
![项目结构图](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2021/11/2499754333.png)

#### 解决方法
将测试用例的包从codec改为codec_test
```go
package codec ----> package codec_test
```
