---
tags: [summary, tensorrt, nvidia, 模型加速, 部署]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# TensorRT Tutorial 摘要

## 原始素材
- 來源：[[40-Raw/Web_Clippings/TensorRT tutorial]]
- Source URL：https://hackmd.io/L7uoraI7SqG3yFzchWae3Q

## 摘要

本篇為 TensorRT 完整部署工作流程筆記，涵蓋 Framework → ONNX → TRT 的完整轉換鏈。

### 核心流程（方法一：推薦）

```
Framework weight → torch/tf/keras 2onnx → .onnx → onnx2trt → .trt → TrtPredict inference
```

### 版本相容整理

| Framework | 版本 | 轉 ONNX 套件 | ONNX opset | TRT 版本 |
|-----------|------|-------------|-----------|---------|
| TensorFlow | 1.12-1.15, 2.1-2.7 | tf2onnx==1.9.3 | 11 | 8.0.1.6 |
| Torch | git 環境 | 內建 export | 11 | 8.0.1.6, 7.2.1 |
| Caffe | openpose==1.5.1 | caffe-onnx | 9 | 7.2.1 |

### torch2onnx
- 需指定 `dummy_input`（image size）、`input_names`、`output_names`、`opset_version=11`
- 支援動態 batch：`dynamic_axes={'input_batch': {0: 'dynamic'}}`
- 多種 model load 方式（直接 `torch.load` / 含 key 的字典）

### tf2onnx
- pb 檔轉 onnx（穩定）：`python3 -m tf2onnx.convert --saved-model tmp_model/ --output xxx.onnx --opset 11 --inputs-as-nchw input_6`
- h5 轉 onnx 可能因自定義 layer 報錯

### onnx2trt（trtexec）
- `trtexec --onnx=input.onnx --fp16 --saveEngine=output.trt --batch=1 --workspace=25`
- 支援 `--int8`（準度差）/ `--fp16`（常用）/ `--best`（速度最快但準度超差）
- 動態 batch 需指定 `--minShapes/--optShapes/--maxShapes`

### ONNX 視覺化
- netron：`pip install netron`；`netron.start(onnx_path, address=(host, port))`

### TrtPredict 推論 wrapper
- `TrtPredict.__init__`：cuda.init() → load engine → allocate_buffers
- `trt_inference(input_dict)` → 返回 `outputs_dict`
- 兩種 CUDA init 方式：手動 `cuda.Device(gpu_id).make_context()` / 自動 `pycuda.autoinit`
- 多執行緒需注意 context push/pop

## 關聯概念
- [[Concepts/TensorRT]]
- [[Concepts/ONNX]]
- [[Concepts/模型加速與部署]]
- [[Concepts/Docker]]
