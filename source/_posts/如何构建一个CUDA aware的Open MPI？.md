title: 如何构建一个CUDA aware的Open MPI？
date: 2022-03-22 07:11:00
categories: cuda
tags: []
---
> 编译后出现了错误
> mpicc: error while loading shared libraries: > libopen-pal.so.0: cannot open shared object > > file: No such file or directory
> 我们可以使用
 ```shell
  sudo ldconfig
 ```
> 来解决




## 1. 如何让Open Mpi支持 CUDA-aware

CUDA-aware 可以让你的MPI能够直接的发送和接收GPU缓存

Open MPI 提供了两种不同的CUDA支持

### (1). 使用 [UCX](https://openucx.org/).
这是你首选的一种机制，同时要确保你的ucx是使用了CUDA支持而构建的。你可以检查你的UCX是否支持CUDA。

```shell
# Check if ucx was built with CUDA support
ucx_info -v
# configured with: --build=powerpc64le-redhat-linux-gnu --host=powerpc64le-redhat-linux-gnu --program-prefix= --disable-dependency-tracking --prefix=/usr --exec-prefix=/usr --bindir=/usr/bin --sbindir=/usr/sbin --sysconfdir=/etc --datadir=/usr/share --includedir=/usr/include --libdir=/usr/lib64 --libexecdir=/usr/libexec --localstatedir=/var --sharedstatedir=/var/lib --mandir=/usr/share/man --infodir=/usr/share/info --disable-optimizations --disable-logging --disable-debug --disable-assertions --enable-mt --disable-params-check --enable-cma --without-cuda --without-gdrcopy --with-verbs --with-cm --with-knem --with-rdmacm --without-rocm --without-xpmem --without-ugni --without-java
```
然后就是构建Open MPI同时使用UCX

- 获取最新的MPI版本
  ```shell
  $ git clone https://github.com/open-mpi/ompi.git
  $ cd ompi
  $ ./autogen.pl
  ```
  
- 编译UCX
  ```shell
  $ mkdir build-ucx
  $ cd build-ucx
  $ ../configure --prefix=<ompi-install-path> --with-ucx=<ucx-install-path>
  ```
  **注意：** 在Open MPI 4.0 + ，可能会报"btl_uct"组件编译失败的错误，这个组件并不重要，我们可以通过以下方式禁用：
  ```shell
  $ ./configure ... --enable-mca-no-build=btl-uct ...
  ```
- 编译
  ```shell
  $ make
  $ make install
  ```
  
- 运行MPI 
  ```shell
  $ mpirun -np 2 -mca pml ucx -x UCX_NET_DEVICES=mlx5_0:1 ./app
  ```
  
  > 最近的OpenMPI版本包含一个名为“uct”的BTL组件，它可能导致在OPAL和UCM之间的Malloc钩子冲突时启用数据损坏。为了解决这个问题，使用以下替代方案之一:
  - 方法1 禁止将该组件编译进来
  ```shell
  $ ./configure ... --enable-mca-no-build=btl-uct ...
  ```
  - 方法2 禁止组件运行
  ```shell
  $ mpirun -np 2 -mca pml ucx -mca btl ^uct -x UCX_NET_DEVICES=mlx5_0:1 ./app
  ```
  
  
- 运行调试
  默认情况下，OpenMPI允许构建传输（BTL），这可能导致OpenMPI进度函数中的其他软件开销。为了解决此问题，您可能会尝试禁用某些BTL。
  ```shell
  $ mpirun -np 2 -mca pml ucx --mca btl ^vader,tcp,openib,uct -x UCX_NET_DEVICES=mlx5_0:1 ./app
  ```
  
  
### (2). 使用内部的Open MPI CUDA支持
你可以使用```--with-cuda=<path-to-cuda>```来构建你的MPI支持CUDA


## 2. 验证安装

```shell
# Use ompi_info to verify cuda support in Open MPI
shell$ ./ompi_info |grep "MPI extensions
```

显示
```shell
MPI extensions: affinity, cuda, pcollreq
```

## 3. CUDA-aware支持的MPI API
- MPI_Allgather

- MPI_Allgatherv

- MPI_Allreduce

- MPI_Alltoall

- MPI_Alltoallv

- MPI_Alltoallw

- MPI_Bcast

- MPI_Bsend

- MPI_Bsend_init

- MPI_Exscan

- MPI_Ibsend

- MPI_Irecv

- MPI_Isend

- MPI_Irsend

- MPI_Issend

- MPI_Gather

- MPI_Gatherv

- MPI_Get

- MPI_Put

- MPI_Rsend

- MPI_Rsend_init

- MPI_Recv

- MPI_Recv_init

- MPI_Reduce

- MPI_Reduce_scatter

- MPI_Reduce_scatter_block

- MPI_Scan

- MPI_Scatter

- MPI_Scatterv

- MPI_Send

- MPI_Send_init

- MPI_Sendrecv

- MPI_Ssend

- MPI_Ssend_init

- MPI_Win_create


## 5. CUDA aware 不支持的API
- MPI_Accumulate

- MPI_Compare_and_swap

- MPI_Fetch_and_op

- MPI_Get_Accumulate

- MPI_Iallgather

- MPI_Iallgatherv

- MPI_Iallreduce

- MPI_Ialltoall

- MPI_Ialltoallv

- MPI_Ialltoallw

- MPI_Ibcast

- MPI_Iexscan

- MPI_Rget

- MPI_Rput


> 参考资料 ：
>[1]. [The Open MPI documentation CUDA Section](https://docs.open-mpi.org/en/master/networking/cuda.html?highlight=cuda)