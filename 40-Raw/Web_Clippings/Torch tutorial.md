---
title: "Torch tutorial"
source: "https://hackmd.io/GKHsyDNHRVql7C9hSw-fiA"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
###### tags: 基礎

[torch stable pypi](https://download.pytorch.org/whl/torch_stable.html)  
[Trouble with CUDA capability sm\_86](https://discuss.pytorch.org/t/need-help-trouble-with-cuda-capability-sm-86/120235)  
[报错解决之GeForce RTX \*\*\* with CUDA capability sm\_86 is not compatible with the current PyTorch…](https://blog.csdn.net/weixin_43051346/article/details/123003905)  
[PyTorch for Jetson - version 1.11 now available](https://forums.developer.nvidia.com/t/pytorch-for-jetson-version-1-11-now-available/72048)

## 使用CPU/GPU

```python
import torch
device = torch.device('cuda') if torch.cuda.is_available() else torch.device('cpu')
model.to(device)
```

## 查詢torch支援的Compute Capability

```python
import torch
torch.cuda.get_device_capability()
```

## torch in jetson

- system package

```bash
apt-get update
apt-get install -y libopenblas-base libopenmpi-dev libomp-dev libopenmpi2_2.1.1-8
```

- version

| Jetpack version | Python version | Torch version |
| --- | --- | --- |
| 4.4 | 3.6 | 1.2.0、1.3.0、1.4.0、1.5.0 |
| 4.4、4.4.1、4.5、4.5.1、4.6 | 3.6 | 1.6.0、1.7.0、1.8.0、1.9.0、1.10.0 |
| 5.0、5.0.1 | 3.8 | 1.11.0、1.12.0 |

## torchvision in jetson

[torchvision github](https://github.com/pytorch/vision/tree/v0.10.0)

- Note

> **By default, GPU support is built if CUDA is found and torch.cuda.is\_available() is true. It's possible to force building GPU support by setting FORCE\_CUDA=1 environment variable, which is useful when building a docker image.**

- install

```bash
sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev gcc
cd torchvision
export BUILD_VERSION=0.10.0
export CUDA_HOME=/usr/local/cuda-10.2
export FORCE_CUDA=1
python3 setup.py install --user
```

- create whl

```bash
cd torchvision
pip wheel --no-index --no-deps ./
```

## release cuda memory

[Delete model from GPU/CPU](https://discuss.pytorch.org/t/delete-model-from-gpu-cpu/123287)  
[Python使用gc模块进行垃圾回收](https://www.jianshu.com/p/b6a20c812ce4)  
[【Python】 垃圾回收机制和gc模块](https://www.cnblogs.com/franknihao/p/7326849.html)  
[Python：gc模块使用和垃圾回收机制](https://www.modb.pro/db/241904)

```python
model = models.resnet18()
print(torch.cuda.memory_allocated())
# 0

model.cuda()
print(torch.cuda.memory_allocated())
# 46861312

model = model.to("cuda")

del model
print(torch.cuda.memory_allocated())
# 0

import gc
gc.collect()
```

選擇 Repo