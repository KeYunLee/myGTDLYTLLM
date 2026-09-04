---
tags: [concept, pytorch, 深度學習, 框架]
created: 2026-09-04
updated: 2026-09-04
source_count: 2
---

# PyTorch

## 核心定義
PyTorch 是 Meta AI 開發的開源深度學習框架，以動態計算圖（eager execution）為核心，是研究和工業部署中最主流的框架之一。

## 運作原理

### 裝置管理
```python
device = torch.device('cuda') if torch.cuda.is_available() else torch.device('cpu')
model.to(device)
# 查詢 Compute Capability
torch.cuda.get_device_capability()  # e.g. (8, 6) for RTX 30xx
```

### 記憶體管理
```python
del model
import gc; gc.collect()
# 確認 GPU 記憶體已釋放
torch.cuda.memory_allocated()  # 應為 0
```

### 模型儲存與載入
```python
torch.save(model, 'model.pt')                    # 完整模型
torch.save({'model': model}, 'checkpoint.pkl')   # 含 key 的字典
model = torch.load('model.pt')
model = torch.load('checkpoint.pkl')['model']
```

### Jetson（ARM）安裝要點
- 需手動安裝：`libopenblas-base libopenmpi-dev libomp-dev`
- torchvision 需從原始碼編譯（設定 `FORCE_CUDA=1`、`CUDA_HOME`）
- Jetpack 4.x → Python 3.6 → Torch 1.6~1.10；Jetpack 5.x → Python 3.8 → Torch 1.11+

### 轉 ONNX（部署橋樑）
```python
torch.onnx.export(
    model, dummy_input, 'model.onnx',
    input_names=['input'], output_names=['output'],
    opset_version=11,
    dynamic_axes={'input': {0: 'dynamic'}}  # 動態 batch
)
```

## 優點與限制
- **優點**：Python 友善、動態圖易於 debug、生態豐富（torchvision/torchtext）
- **限制**：相較 TensorFlow/TensorRT，原生部署工具較少；Jetson 上需手動編譯

## 與相關概念的關係
- 相似：[[Concepts/TensorFlow]]（同為深度學習框架，動態圖 vs 靜態圖）
- 對比：[[Concepts/TensorRT]]（部署優化，非訓練框架）
- 上位：[[Concepts/模型加速與部署]]
- 相關：[[Concepts/TensorRT]]、[[Concepts/ONNX]]

## 來源
- [[Summaries/Torch-tutorial]]
- [[Summaries/TensorRT-tutorial]]
- [[Summaries/圖片分類與物件偵測]]
