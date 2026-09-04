---
tags: [concept, anomaly-detection, AI模型, 無監督學習]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Anomaly Detection（異常偵測）

## 核心定義
Anomaly Detection 是在資料中識別與「正常」模式顯著不同的樣本之技術。工業視覺場景中，目標是在不需要大量異常標注的情況下，偵測出產品缺陷。

## 運作原理

### 主流方法分類

#### 1. Embedding-based 方法（嵌入式）
- 使用預訓練 CNN 提取特徵嵌入
- 在 embedding space 中，正常樣本聚集，異常樣本偏離
- 搭配 **FAISS** 進行高效 GPU 向量搜尋

#### 2. PaDiM（Patch Distribution Modeling）
```
預訓練 CNN → 提取 patch 特徵 → 建立多變量高斯分佈 → Mahalanobis 距離推論
```
- 每個 patch 位置建立獨立的高斯分佈模型
- 推論時計算馬哈拉諾比斯距離作為異常分數
- 可定位異常區域（Anomaly Localization）

#### 3. PatchCore（Towards Total Recall）
```
預訓練 CNN → 提取 patch 特徵 → 記憶體庫（Memory Bank）→ 最近鄰異常分數
```
- 只需正常樣本建立記憶體庫
- 推論時與記憶體庫最近鄰比較
- Amazon Research 開發，MVTec AD SOTA

### 基準資料集
- **MVTec AD**：15 種工業材質/物件，標準 benchmark
- **Few-Shot Anomaly Detection**：少樣本版本

## 優點與限制
- **優點**：無監督 / 少樣本，不需要大量異常標注；可定位缺陷位置
- **限制**：需要乾淨的正常訓練樣本；對未知缺陷類型泛化能力有限

## 與相關概念的關係
- 相似：[[Concepts/物件偵測]]（同為視覺感知）
- 對比：[[Concepts/物件偵測]]（異常偵測通常無監督，物件偵測有監督）
- 上位：[[Concepts/AI模型部署]]

## 來源
- [[Summaries/Anomaly-detection]]
