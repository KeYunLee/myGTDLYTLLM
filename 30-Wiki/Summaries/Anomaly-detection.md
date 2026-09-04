---
tags: [summary, anomaly-detection, AI模型]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Anomaly Detection 資源摘要

## 原始素材
- 來源：[[40-Raw/Web_Clippings/Anomaly detection]]
- Source URL：https://hackmd.io/kfoHCUtdTbKAn9uXVxED0w

## 摘要

本篇整理工業異常偵測（Anomaly Detection）的主要資源，聚焦在 **MVTec AD** 基準資料集上的 SOTA 方法，包含標準版與 Few-Shot 版本。

### 核心方法分類

#### 1. Embedding-based 方法
基於特徵嵌入的非監督式異常偵測，代表資源：
- 無監督異常檢測 Part 3 基於嵌入的方法（CSDN）
- Meta 的 **FAISS** GPU 加速向量搜尋函式庫

#### 2. PaDiM（Patch Distribution Modeling）
- **核心思想**：對每個 patch 位置建立多變量高斯分佈，偵測與正常分佈偏差之處
- 原理：利用預訓練 CNN 提取 patch 特徵 → 建立 patch 統計模型 → 推論時計算 Mahalanobis 距離
- 相關資源：GitHub 實作 + 論文筆記

#### 3. PatchCore（Towards Total Recall）
- **核心思想**：將正常樣本特徵存入記憶體庫（memory bank），推論時找最近鄰計算異常分數
- 論文：arxiv 2106.08265
- 由 Amazon Research 開發
- 相關資源：GitHub + paper + CSDN 解析

## 關聯概念
- [[Concepts/Anomaly-Detection]]
- [[Concepts/Embedding-方法]]
