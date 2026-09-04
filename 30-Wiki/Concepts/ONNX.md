---
tags: [concept, onnx, 模型部署, 格式轉換]
created: 2026-09-04
updated: 2026-09-04
source_count: 2
---

# ONNX（Open Neural Network Exchange）

## 核心定義
ONNX 是由 Microsoft 與 Meta 共同推動的開放神經網路交換格式，作為不同深度學習框架（PyTorch、TensorFlow、Keras 等）與部署後端（TensorRT、ONNX Runtime）之間的**中間表示（IR）**橋樑。

## 運作原理

### 角色定位
```
PyTorch / TensorFlow / Keras
         ↓ 匯出 .onnx
      ONNX（中間格式）
         ↓ 轉換
 TensorRT / ONNX Runtime / CoreML
```

### 匯出（PyTorch → ONNX）
```python
torch.onnx.export(
    model, dummy_input, "model.onnx",
    input_names=["input"],
    output_names=["output"],
    opset_version=11,                       # 推薦 opset 11
    dynamic_axes={"input": {0: "batch"}}   # 動態 batch
)
```

### 驗證與視覺化
```bash
# 驗證模型結構
python -c "import onnx; onnx.checker.check_model('model.onnx')"
# 使用 Netron 視覺化（https://netron.app）
```

### Opset 版本選擇
| opset | 建議場景 |
|-------|---------|
| 9 | Caffe → ONNX |
| 11 | PyTorch / TensorFlow → TRT（穩定首選）|
| 13+ | 新算子支援更完整，但 TRT 轉換可能失敗 |

## 優點與限制
- **優點**：框架無關；生態豐富；廣泛被各推論引擎支援
- **限制**：自訂算子（Custom Op）需額外實作；opset 版本相容性需注意；`.onnx` 不含訓練資訊

## 與相關概念的關係
- 相似：PMML（傳統 ML 模型交換格式）
- 下位：[[Concepts/TensorRT]]（ONNX 的主要下游消費者之一）
- 下位：[[Concepts/PyTorch]]（主要匯出來源）
- 上位：[[Concepts/模型加速與部署]]

## 來源
- [[Summaries/TensorRT-tutorial]]
- [[Summaries/Torch-tutorial]]
