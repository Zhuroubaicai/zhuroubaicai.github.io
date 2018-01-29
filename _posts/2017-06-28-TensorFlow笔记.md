---
layout: post
title: TensorFlow笔记
---

## 调整TensorFlow的log级别

从源里装的TensorFlow执行的时候回有许多warning，类似这样的`The TensorFlow library wasn't compiled to use SSE instructions`，使需要的log信息难以找到。这是因为源里面安装的tensorflow在编译时没有打开一些[SMID][SIMD]优化选项，但这并不影响我们的正常使用。可以通过输出环境变量控制tensorflow输出的log。

```
 export TF_CPP_MIN_LOG_LEVEL=1
```

| level | 描述           |
| :---- | :----------- |
| 0     | 不打印info      |
| 1     | 不打印warning   |
| 2     | 不打印任何信息(不建议) |

## 调整显存使用比例

默认策略是全部占满显存，可能造成显存的浪费。可以选择按比例加载，也可以选择按需分配。

```python
# 设置使用 0.4 显存
config = tf.ConfigProto()
config.gpu_options.per_process_gpu_memory_fraction = 0.4
session = tf.Session(config=config, ...)

#按需分配
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
session = tf.Session(config=config, ...) 
```
[SIMD]:https://zh.wikipedia.org/wiki/%E5%8D%95%E6%8C%87%E4%BB%A4%E6%B5%81%E5%A4%9A%E6%95%B0%E6%8D%AE%E6%B5%81

## 安装 CUDA 驱动

1. 屏蔽 nouveau 模块, 修改/etc/modprobe.d/blacklist.conf，在末尾加入
```
blacklist nouveau
options nouveau modeset=0
```

2. 更新 initramfs
```bash
update-initramfs -u
```