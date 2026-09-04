---
tags: [summary, 物件偵測, YOLO, SAHI, 圖像分類]
created: 2026-09-04
source: "[[40-Raw/Web_Clippings/YOLO 物件偵測]]"
---

# YOLO 物件偵測 × SAHI 切片推論

## 原始素材
- 來源：[[40-Raw/Web_Clippings/YOLO 物件偵測]]
- 類型：技術問答 / HackMD 剪輯

## 核心摘要

本文聚焦 **SAHI（Slicing Aided Hyper Inference）** 解決高解析度圖像中小物件偵測的原理，以及如何將切片策略應用於圖像分類任務。

## 關鍵技術要點

### SAHI 核心流程
- **切片（Slice）→ 推論（Inference）→ NMS 合併（Merge）**
- 主要支援：物件偵測（Primary）、實例分割（Full）
- 不支援：純圖像分類（無座標輸出，NMS 無法運作）

### 支援框架
| 框架 | 代表模型 |
|------|---------|
| Ultralytics | YOLOv8、YOLO11、YOLOv5 |
| MMDetection | Faster R-CNN、Mask R-CNN |
| Hugging Face | DETR、OwlViT |
| TorchVision | Faster R-CNN、RetinaNet |
| Detectron2 | Mask R-CNN |

### 分類任務的切片替代方案
1. **借用 SAHI `slice_image`**：切圖後自行迴圈送進分類模型，產出熱力圖
2. **`patchify`**：輕量 NumPy 切塊工具，固定大小切片（如 224×224）
3. **`torch.nn.functional.unfold`**：GPU Tensor 原生切塊（高效但需懂維度）

### 決策建議
- 需精準定位 → 改用物件偵測（YOLO + SAHI），不要硬用分類模型
- 純判斷有無 → 切片分類（Patch Classification）+ Heatmap

## 相關概念
- [[Concepts/物件偵測]]
- [[Concepts/圖像分類]]
- [[Concepts/Embedding-方法]]
