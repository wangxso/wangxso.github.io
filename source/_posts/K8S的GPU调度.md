title: K8S的GPU调度
date: 2023-07-12 07:08:25
categories: bxjs
tags: []
---
# K8S的GPU调度

环境要求: Kubernetes v1.26 [stable]

Kubernetes可以通过设备插件来稳定的管理你的集群不同节点之间的AMD和Nvidia的图像处理单元。

本章会展示用户如何使用GPU并且概述了其中的一些限制。

## 使用设备插件

Kubernetes 实现了设备插件，允许 Pods(Pods是K8S中可以创建和管理的最小的可部署计算单元)访问专门的硬件特性，如 GPUs。

首先作为管理员，你应该先给GPU装上相关公司提供的驱动程序和运行相应的设备插件程序，下面有几个主流公司的设备插件程序地址:

AMD: 

[Intel GPU device plugin for Kubernetes — Intel® Device Plugins for Kubernetes  documentation](https://intel.github.io/intel-device-plugins-for-kubernetes/cmd/gpu_plugin/README.html)

[GitHub - RadeonOpenCompute/k8s-device-plugin: Kubernetes (k8s) device plugin to enable registration of AMD GPU to a container cluster](https://github.com/RadeonOpenCompute/k8s-device-plugin#deployment)

[GitHub - NVIDIA/k8s-device-plugin: NVIDIA device plugin for Kubernetes](https://github.com/NVIDIA/k8s-device-plugin#quick-start)

如果你安装了相应的插件，你的集群会暴露出一个可定制的调度资源比如`amd.com/gpu` 和

`[nvidia.com/gpu](http://nvidia.com/gpu)` 。

通过自定义的GPU资源，你可以从容器中使用这些GPU，就像你请求CPU和内存资源的方式一样。

然后在如何自定义设备的制定需求的时候有一些限制。

GPU只能在限制下被指定，这意味着：

- 你可以在不指定请求的情况下指定GPU的限制。因为默认情况下k8s会将这个限制作为对请求的默认限制。
- 你可以指定对请求的限制的同时也可以设置对GPU的限制，但是这两个值必须相等。
- 你不能再没有指定GPU限制的情况下也不设置请求的限制。

下面是一个有关Pod请求GPU的清单示例:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-vector-add
spec:
  restartPolicy: OnFailure
  containers:
    - name: example-vector-add
      image: "registry.example/example-vector-add:v42"
      resources:
        limits:
          gpu-vendor.example/example-gpu: 1 # requesting 1 GPU
```

## 包含不同类型 GPU 的集群

如果集群中的不同节点具有不同类型的 GPU，那么可以使用节点标签和节点选择器将 pods 调度到适当的节点。

比如:

```bash
# Label your nodes with the accelerator type they have.
kubectl label nodes node1 accelerator=example-gpu-x100
kubectl label nodes node2 accelerator=other-gpu-k915
```

标签键的accelerator只是一个例子; 如果愿意，可以使用不同的标签键。

## 自动节点标记

如果你使用的是 AMD GPU 设备，则可以部署节点标签器。节点标签器是一个控制器，自动标记你的节点中的GPU 设备属性。

-------------
NVIDIA 的类似功能由 [GPU feature discovery](https://github.com/NVIDIA/gpu-feature-discovery/blob/main/README.md)提供。

reference: https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/