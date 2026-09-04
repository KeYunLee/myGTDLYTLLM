---
tags: [summary, tensorflow, jetson, GPU]
created: 2026-09-04
updated: 2026-09-04
source: "[[40-Raw/Web_Clippings/Tensorflow tutorial]]"
---

# 摘要：Tensorflow tutorial

## 原始素材
- **來源**：[Tensorflow tutorial（HackMD）](https://hackmd.io/VilLT8TuQaSGLxkN2rtoiA)
- **主題**：TensorFlow 在 Jetson 平台的安裝與 GPU 記憶體分配策略

---

## 重點整理

### 1. TensorFlow 在 Jetson 平台安裝

Jetson 系列（Xavier NX、AGX）需要從 NVIDIA 官方 redist 頁面取得對應的 `.whl` 安裝包。

**系統依賴套件**：
```bash
apt-get update
apt-get install -y libhdf5-serial-dev hdf5-tools libhdf5-dev \
  zlib1g-dev zip libjpeg8-dev liblapack-dev libblas-dev gfortran
```

**參考連結**：
- [NVIDIA TF Jetson 版本說明](https://docs.nvidia.com/deeplearning/frameworks/install-tf-jetson-platform-release-notes/)
- [NVIDIA Redist whl 頁面](https://developer.download.nvidia.com/compute/redist/jp/)

---

### 2. GPU 記憶體分配策略

TensorFlow 預設會佔用全部 GPU 記憶體，建議改為**成長型策略（memory growth）**，按需取用。

#### TensorFlow 2.0+（Python API）
```python
import tensorflow as tf
gpus = tf.config.list_physical_devices('GPU')
if gpus:
    try:
        for gpu in gpus:
            tf.config.experimental.set_memory_growth(gpu, True)
        logical_gpus = tf.config.list_logical_devices('GPU')
        print(len(gpus), "Physical GPUs,", len(logical_gpus), "Logical GPUs")
    except RuntimeError as e:
        print(e)
```

#### TensorFlow 2.0+（環境變數）
```bash
export TF_FORCE_GPU_ALLOW_GROWTH=true
```

#### TensorFlow 1.x（tensorflow 模型）
```python
import tensorflow as tf
gpu_options = tf.GPUOptions(allow_growth=True)
sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options))
```

#### TensorFlow 1.x（Keras 模型）
```python
gpu_options = tf.GPUOptions(allow_growth=True)
sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options))
tf.keras.backend.set_session(sess)
```

---

## 相關概念
- [[Concepts/TensorFlow]]
- [[Concepts/PyTorch]]（同為深度學習框架，GPU 管理對比）
- [[Concepts/模型加速與部署]]
