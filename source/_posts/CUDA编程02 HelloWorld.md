title: CUDA编程02 HelloWorld
date: 2021-08-06 02:53:50
categories: cuda
tags: []
---
# CUDA编程02 HelloWorld

## 一、主机和设备

### 1. 主机(Host)

CPU以及系统的内存称为主机

### 2.设备(Device)

GPU以及其内存称为设备

### 3.核函数(Kernel)

在GPU上运行的函数

### 4.标识符

- __ global __命名的函数可以由主机(Host)调用在GPU（Device）上运行的函数

- __ device __ 只能由GPU（Device）上的函数调用的函数

- __ host __ 在主机上运行的函数

### 二、HelloWorld代码

```c
#include<iostream>
__global__ void kernel(void) {
    
}

int main() {
    kernel<<<1,1>>>();
    printf("HelloWorld!");
    return 0;
}
```

在上面的代码中我们看到在主机中调用kernel使用三个尖括号里面的中的数字指的向内核中传递的执行参数，执行参数一共有四个，

-  第一个表示网格大小(grid)
- 第二个声明块的大小(block)
- 第三个声明动态分配的共享存储器大小，默认为0
- 最后一个参数声明执行的流，默认为0。

### 三、加法运算(展示如何传递参数)

```c++
#include<iostream>

__global__ kernel(int a, int b, int *c) {
    *c = a + b;
}

int main() {
    int c;
    int *dev_c;
    cudaMalloc((void**)&dev_c, sizeof(int));
    add<<<1,1>>>(7,2,dev_c);
    cudaMemcpy(&c, dev_c, sizeof(int), cudaMemcpyDeviceToHost);
    printf("7 + 2 = %d\n", c);
    return 0;
}
```

当设备执行任何有用的操作(需要对数据记录)的时候都需要分配内存，例如计算值的返回。

给数据分配内存类似于c的malloc这里使用的关键字是cudaMalloc

- 第一个参数是一个指针，指向用于保存新分配的内存地址的变量
- 第二个参数需要分配的内存空间大小

cudaMemcpyDeviceToHost相当于一个标识，标识数据流动的方向。