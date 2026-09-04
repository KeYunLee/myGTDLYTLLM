---
tags: [concept, tensorflow, 深度學習, 框架, GPU, jetson]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# TensorFlow

## 核心定義
TensorFlow 是 Google 開發的開源深度學習框架，以靜態計算圖（graph execution）為原始設計，2.0 版後引入 eager execution 兼容動態圖。廣泛用於工業部署、研究與嵌入式裝置（Jetson、Coral）。

---

## 運作原理

### Jetson 平台安裝

Jetson（ARM 架構）需從 NVIDIA 官方 redist 頁面取得對應 `.whl` 安裝包，並預先安裝系統依賴。

**系統依賴**：
```bash
apt-get update
apt-get install -y libhdf5-serial-dev hdf5-tools libhdf5-dev \
  zlib1g-dev zip libjpeg8-dev liblapack-dev libblas-dev gfortran
```

**參考**：
- [NVIDIA TF Jetson 版本說明](https://docs.nvidia.com/deeplearning/frameworks/install-tf-jetson-platform-release-notes/)
- [NVIDIA Redist whl 頁面](https://developer.download.nvidia.com/compute/redist/jp/)

---

### GPU 記憶體分配策略

TensorFlow 預設啟動時佔用全部 GPU 記憶體，在多程式環境（如 Jetson 同時跑多個推論服務）下容易造成 OOM，建議改為**成長型策略（memory growth）**。

#### TensorFlow 2.0+（推薦）
```python
import tensorflow as tf
gpus = tf.config.list_physical_devices('GPU')
if gpus:
    try:
        for gpu in gpus:
            tf.config.experimental.set_memory_growth(gpu, True)
    except RuntimeError as e:
        print(e)
```

**或使用環境變數**（無需改動程式碼）：
```bash
export TF_FORCE_GPU_ALLOW_GROWTH=true
```

#### TensorFlow 1.x（舊版相容）
```python
# tensorflow 原生模型
gpu_options = tf.GPUOptions(allow_growth=True)
sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options))

# Keras 模型
gpu_options = tf.GPUOptions(allow_growth=True)
sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options))
tf.keras.backend.set_session(sess)
```

---

## 優點與限制

| | TensorFlow | PyTorch |
|--|--|--|
| 計算圖 | 靜態（graph）為主，2.0 支援 eager | 動態（eager）為主 |
| 部署 | TFLite / TF Serving 生態成熟 | 需轉 ONNX / TorchScript |
| Jetson 支援 | NVIDIA 官方提供 `.whl` | 需手動從原始碼編譯 |
| GPU 記憶體 | 預設全占，需主動設定成長型 | 動態管理，較彈性 |

**優點**：
- NVIDIA 對 Jetson 提供官方 TF 預編譯包，安裝較 PyTorch 簡單
- TFLite 可直接部署至行動裝置，生態完整
- TF Serving 提供生產級 model serving 方案

**限制**：
- 1.x → 2.x 版本 API 差異大，舊版程式碼需遷移
- eager mode 效能在 graph mode 優化情境下略遜
- GPU 記憶體預設策略需手動調整

---

## 與相關概念的關係
- 相似：[[Concepts/PyTorch]]（同為深度學習框架，API 風格不同）
- 對比：[[Concepts/TensorRT]]（TRT 為推論優化引擎，可消費 TF SavedModel）
- 上位：[[Concepts/模型加速與部署]]
- 相關：[[Concepts/ONNX]]（TF → ONNX 轉換橋樑）、[[Concepts/AI模型部署]]

---

## 來源
- [[Summaries/Tensorflow-tutorial]]
