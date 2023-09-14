title: zlib 404
date: 2023-08-01 08:45:31
categories: zczb
tags: []
---
```shell
/apollo/.cache/bazel/540135163923dd7d5820f3ee4b306b32/external/rules_proto/proto/private/dependencies.bzl
```

查看输出，发现所需文件“zlib-1.2.11.tar.gz” 的预期的哈希值与实际的哈希值不匹配，这可能表示下载的文件与预期的文件不同或文件可能已被篡改。

新的下载链接
https://zlib.net/fossils/zlib-1.2.11.tar.gz

/apollo/.cache/bazel/540135163923dd7d5820f3ee4b306b32/external/rules_proto/proto/private

目录下面的
dependencies.bzl

![](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/usr/uploads/2023/08/4096377460.png)