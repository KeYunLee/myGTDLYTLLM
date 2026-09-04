---
tags: [concept, tensorrt, nvidia, 模型加速, 推論優化]
created: 2026-09-04
updated: 2026-09-04
source_count: 2
---

# TensorRT

## 核心定義
TensorRT 是 NVIDIA 的高效能深度學習推論優化器（Inference Optimizer）與執行引擎，能對已訓練的模型進行圖優化、層融合、精度校準（FP16/INT8），大幅提升 GPU 推論速度。

## 運作原理

### 完整轉換流程
```
Framework weight (.pt/.h5/.pb)
    ↓ [framework2onnx]
.onnx（中間格式）
    ↓ [trtexec]
.trt（TensorRT Engine，平台特定）
    ↓ [TrtPredict.trt_inference()]
推論結果
```

### 轉換工具對應
| Framework | 轉 ONNX 工具 | 推薦 opset |
|-----------|------------|-----------|
| PyTorch | `torch.onnx.export()` | 11 |
| TensorFlow/Keras | `tf2onnx` | 11 |
| Caffe | `caffe-onnx` | 9 |

### 精度選擇
| 精度 | 指令 | 特性 |
|------|------|------|
| FP32 | 預設 | 最高精度，速度最慢 |
| FP16 | `--fp16` | 精度正常，速度提升 ★常用 |
| INT8 | `--int8` | 精度差，速度最快 |
| Best | `--best` | 速度最快，準度超差 |

### CUDA Context 管理（關鍵）
```python
# 方法一（多執行緒推薦）
cuda.init()
cuda_ctx = cuda.Device(gpu_id).make_context()
cuda_ctx.push()
# ... inference ...
cuda_ctx.pop()

# 方法二（單執行緒快速）
import pycuda.autoinit
```

### 重要注意事項
- `.trt` 檔為**平台特定**（GPU compute capability 不同則不相容）
- ONNX opset 13 在部分情況下轉 TRT 會失敗，優先使用 opset 11
- 使用 nvidia 官方 Docker image 可確保版本相容性

## 優點與限制
- **優點**：大幅加速 GPU 推論（可達 2-5x）；支援多種精度；支援動態 batch
- **限制**：轉換後不易更改模型；`trt` 檔與 GPU 架構綁定；轉換過程可能耗時

## 與相關概念的關係
- 相似：ONNX Runtime（跨平台推論）
- 對比：PyTorch（訓練框架，非推論優化）
- 上位：[[Concepts/模型加速與部署]]
- 下位：[[Concepts/Docker]]（TRT 通常在 Docker 中運行）
- 相關：[[Concepts/PyTorch]]、[[Concepts/ONNX]]

## 來源
- [[Summaries/TensorRT-tutorial]]
- [[Summaries/Torch-tutorial]]
