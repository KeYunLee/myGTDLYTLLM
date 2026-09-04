---
tags: [concept, 模型部署, AI模型, 推論, mlops]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# AI 模型部署（AI Model Deployment）

## 核心定義
AI 模型部署是將訓練好的 AI/ML 模型整合至生產環境，使其能接收真實輸入並提供推論服務的工程實踐。部署不只是「把模型跑起來」，而是需考量效能、可靠性、可維護性與成本的系統工程。

## 運作原理

### 部署環境分類

| 環境 | 代表設備 | 特性 |
|------|---------|------|
| 雲端（Cloud）| AWS / GCP / Azure GPU | 算力強、彈性高、成本高 |
| 邊緣（Edge）| Jetson NX / Xavier | 低延遲、無網路依賴、算力受限 |
| 行動裝置 | iPhone / Android | 超低功耗；需轉 CoreML / TFLite |
| 嵌入式 | Hailo / Coral TPU | 超低功耗；框架支援有限 |

### 完整部署流程
```
訓練模型（.pt / .h5）
    ↓ 轉換（Framework → ONNX）
中間格式（.onnx）
    ↓ 優化（ONNX → TRT / TFLite / CoreML）
推論引擎（平台特定）
    ↓ 包裝
API 服務（FastAPI / gRPC / ROS）
    ↓ 容器化
Docker Image
    ↓ 部署
生產環境（K8s / Jetson / 雲端）
```

### 效能指標
- **Latency（延遲）**：單次推論耗時（ms）
- **Throughput（吞吐量）**：每秒推論次數（FPS / QPS）
- **Memory（記憶體）**：GPU VRAM 佔用
- **Accuracy（精度）**：量化後精度損失

### 關鍵優化手段
| 技術 | 效果 | 工具 |
|------|------|------|
| FP16 量化 | 2x 速度提升，精度影響小 | TensorRT `--fp16` |
| INT8 量化 | 4x+ 速度提升，精度有損 | TensorRT `--int8` |
| 模型剪枝（Pruning） | 減少參數量 | PyTorch Pruning |
| 知識蒸餾（Distillation）| 小模型學大模型 | 自訂訓練流程 |
| 批次推論（Batching）| 提高 GPU 利用率 | Dynamic batch |

## 優點與限制
- **優點**：系統化地考量訓練到服務的全流程；可針對目標環境定制優化
- **限制**：需要跨領域知識（ML + 系統 + DevOps）；優化後的模型難以遷移至其他平台

## 與相關概念的關係
- 下位：[[Concepts/TensorRT]]（GPU 推論優化）
- 下位：[[Concepts/Docker]]（部署環境容器化）
- 下位：[[Concepts/ONNX]]（框架間轉換橋樑）
- 下位：[[Concepts/Anomaly-Detection]]（視覺 AI 部署場景之一）
- 相似：[[Concepts/模型加速與部署]]（同義詞，偏重加速面向）

## 來源
- [[Summaries/Anomaly-detection]]
