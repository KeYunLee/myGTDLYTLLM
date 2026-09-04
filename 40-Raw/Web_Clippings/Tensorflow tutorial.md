---
title: "Tensorflow tutorial"
source: "https://hackmd.io/VilLT8TuQaSGLxkN2rtoiA"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
###### tags: 基礎 課程

## Tensorflow in Jetson platform

[TensorFlow For Jetson Platform](https://docs.nvidia.com/deeplearning/frameworks/install-tf-jetson-platform-release-notes/tf-jetson-rel.html#tf-jetson-rel)  
[Tensorflow whl for nvidia device](https://developer.download.nvidia.com/compute/redist/jp/)  
[NVIDIA JETSON XAVIER NX DEVELOPER KIT──安裝TensorFlow](https://14shan.medium.com/%E5%9C%A8nvidia-jetson-xavier-nx-developer-kit%E4%B8%8A%E5%AE%89%E8%A3%9Dtensorflow-2dead2e16c3c)

- 系統套件需求

```bash
apt-get update
apt-get install -y libhdf5-serial-dev hdf5-tools libhdf5-dev zlib1g-dev zip libjpeg8-dev liblapack-dev libblas-dev gfortran
```

## GPU分配策略

[Tensorflow2對GPU記憶體的分配策略](https://iter01.com/611244.html)

### 設為成長型策略(Tensorflow 2.0以上版本): 要用時再拿

- 使用Python API

```python
# Tensorflow 2.0以上版本
import tensorflow as tf
gpus = tf.config.list_physical_devices('GPU')
if gpus:
  try:
    # Currently, memory growth needs to be the same across GPUs
    for gpu in gpus:
      tf.config.experimental.set_memory_growth(gpu, True)
    logical_gpus = tf.config.list_logical_devices('GPU')
    print(len(gpus), "Physical GPUs,", len(logical_gpus), "Logical GPUs")
  except RuntimeError as e:
    # Memory growth must be set before GPUs have been initialized
    print(e)
```

- 使用環境變數設定

```bash
export TF_FORCE_GPU_ALLOW_GROWTH=true
```

### 設為成長型策略(Tensorflow 1.x版本): 要用時再拿

- 使用Python API

```python
# Tensorflow 1.x版本 tensorflow模型
import tensorflow as tf
gpu_options = tf.GPUOptions(allow_growth=True)
sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options))
print(sess.run(y))

# Tensorflow 1.x版本 keras模型
gpu_options = tf.GPUOptions(allow_growth=True)
sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options))
tf.keras.backend.set_session(sess)
# 使用 Keras 建立模型
# ...
```

選擇 Repo