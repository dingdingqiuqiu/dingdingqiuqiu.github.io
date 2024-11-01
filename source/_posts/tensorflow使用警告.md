---
title: tensorflow使用警告
date: 2024-09-12 21:26:09
tags: English
categories:
---

本文主要对一篇英文回答进行翻译，练练英语能力。
同时尝试解决`tensorflow`使用时的警告问题。

<!--more-->

原回答：https://stackoverflow.com/questions/77338229/tensorflow-2-14-0-with-cuda-not-registering-cuda

### 本机环境

![](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VTNGtwRFlHU3RRZ2dQdUREUUFBQUFBQmFTUnN2NnNRUHhwLWFCTXVnZnRVd1E_ZT16cjlnRzQ.png)

### 问题描述

> 实际上并不能算作严重的问题，警告而已
>
> 本文目的主要是练练英语

<img src="https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VTNGtwRFlHU3RRZ2dQdUVEUUFBQUFBQlMzNFpMWDZySktfanZ2bmlnUE0zWEE_ZT1zaEJjWVo.png" width="958" height="" />

<img src="https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VTNGtwRFlHU3RRZ2dQdUZEUUFBQUFBQnRLVDl3Vnc3aHgwaUg0VUtLMjJSclE_ZT1hNmJjMHI.png" width="958" height="" />

### 警告翻译

开练英语.......

```c
2024-09-13 00:03:04.282338: E external/local_xla/xla/stream_executor/cuda/cuda_fft.cc:485] Unable to register cuFFT factory: Attempting to register factory for plugin cuFFT when one has already been registered
// 
2024-09-13 00:03:04.351321: E external/local_xla/xla/stream_executor/cuda/cuda_dnn.cc:8454] Unable to register cuDNN factory: Attempting to register factory for plugin cuDNN when one has already been registered
2024-09-13 00:03:04.372352: E external/local_xla/xla/stream_executor/cuda/cuda_blas.cc:1452] Unable to register cuBLAS factory: Attempting to register factory for plugin cuBLAS when one has already been registered
2024-09-13 00:03:04.508856: I tensorflow/core/platform/cpu_feature_guard.cc:210] This TensorFlow binary is optimized to use available CPU instructions in performance-critical operations.
To enable the following instructions: AVX2 FMA, in other operations, rebuild TensorFlow with the appropriate compiler flags.
2024-09-13 00:03:05.940357: W tensorflow/compiler/tf2tensorrt/utils/py_utils.cc:38] TF-TRT Warning: Could not find TensorRT
WARNING: All log messages before absl::InitializeLog() is called are written to STDERR
I0000 00:00:1726156987.392321  125977 cuda_executor.cc:1001] could not open file to read NUMA node: /sys/bus/pci/devices/0000:01:00.0/numa_node
Your kernel may have been built without NUMA support.
I0000 00:00:1726156987.582220  125977 cuda_executor.cc:1001] could not open file to read NUMA node: /sys/bus/pci/devices/0000:01:00.0/numa_node
Your kernel may have been built without NUMA support.
I0000 00:00:1726156987.582324  125977 cuda_executor.cc:1001] could not open file to read NUMA node: /sys/bus/pci/devices/0000:01:00.0/numa_node
Your kernel may have been built without NUMA support.
Num GPUs Available:  1
I0000 00:00:1726156987.588599  125977 cuda_executor.cc:1001] could not open file to read NUMA node: /sys/bus/pci/devices/0000:01:00.0/numa_node
Your kernel may have been built without NUMA support.
I0000 00:00:1726156987.588689  125977 cuda_executor.cc:1001] could not open file to read NUMA node: /sys/bus/pci/devices/0000:01:00.0/numa_node
Your kernel may have been built without NUMA support.
I0000 00:00:1726156987.588756  125977 cuda_executor.cc:1001] could not open file to read NUMA node: /sys/bus/pci/devices/0000:01:00.0/numa_node
Your kernel may have been built without NUMA support.
I0000 00:00:1726156988.000957  125977 cuda_executor.cc:1001] could not open file to read NUMA node: /sys/bus/pci/devices/0000:01:00.0/numa_node
Your kernel may have been built without NUMA support.
I0000 00:00:1726156988.001057  125977 cuda_executor.cc:1001] could not open file to read NUMA node: /sys/bus/pci/devices/0000:01:00.0/numa_node
Your kernel may have been built without NUMA support.
2024-09-13 00:03:08.001094: I tensorflow/core/common_runtime/gpu/gpu_device.cc:2112] Could not identify NUMA node of platform GPU id 0, defaulting to 0.  Your kernel may not have been built with NUMA support.
I0000 00:00:1726156988.001174  125977 cuda_executor.cc:1001] could not open file to read NUMA node: /sys/bus/pci/devices/0000:01:00.0/numa_node
Your kernel may have been built without NUMA support.
2024-09-13 00:03:08.001553: I tensorflow/core/common_runtime/gpu/gpu_device.cc:2021] Created device /job:localhost/replica:0/task:0/device:GPU:0 with 1767 MB memory:  -> device: 0, name: NVIDIA GeForce RTX 3050 Laptop GPU, pci bus id: 0000:01:00.0, compute capability: 8.6
tf.Tensor(
[[1. 3.]
 [3. 7.]], shape=(2, 2), dtype=float32)
```

