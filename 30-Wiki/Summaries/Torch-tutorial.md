---
tags: [summary, pytorch, torch, 基礎]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Torch Tutorial 摘要

## 原始素材
- 來源：[[40-Raw/Web_Clippings/Torch tutorial]]
- Source URL：https://hackmd.io/GKHsyDNHRVql7C9hSw-fiA

## 摘要

本篇整理 PyTorch 在不同硬體環境（x86 / Jetson）的安裝與基礎使用技巧。

### CPU/GPU 切換
```python
device = torch.device('cuda') if torch.cuda.is_available() else torch.device('cpu')
model.to(device)
```

### Compute Capability 查詢
```python
torch.cuda.get_device_capability()  # e.g. (8, 6) for RTX 3xxx
```

### Jetson（ARM）上的 Torch 安裝

**系統套件前置需求**：
```bash
apt-get install -y libopenblas-base libopenmpi-dev libomp-dev libopenmpi2_2.1.1-8
```

**版本對應**：
| Jetpack | Python | Torch |
|---------|--------|-------|
| 4.4 | 3.6 | 1.2.0 ~ 1.5.0 |
| 4.4.1 ~ 4.6 | 3.6 | 1.6.0 ~ 1.10.0 |
| 5.0 ~ 5.0.1 | 3.8 | 1.11.0 ~ 1.12.0 |

### torchvision（Jetson 編譯安裝）
```bash
export BUILD_VERSION=0.10.0
export CUDA_HOME=/usr/local/cuda-10.2
export FORCE_CUDA=1
python3 setup.py install --user
# 或打包成 whl：pip wheel --no-index --no-deps ./
```

### 釋放 CUDA 記憶體
```python
del model
import gc; gc.collect()
# torch.cuda.memory_allocated() 確認歸零
```

## 關聯概念
- [[Concepts/PyTorch]]
- [[Concepts/TensorRT]]
- [[Concepts/模型加速與部署]]
