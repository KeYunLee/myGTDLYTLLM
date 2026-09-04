---
tags: [concept, embedding, 向量表示, 深度學習, 無監督學習]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Embedding 方法（嵌入式表示）

## 核心定義
Embedding 是將高維離散資料（文字、影像 patch、類別標籤）映射到低維連續向量空間的技術。在此向量空間中，語義相似的樣本在距離上也相近，使得相似度計算、分群、異常偵測等任務成為可能。

## 運作原理

### 應用場景分類

#### 1. 電腦視覺（Visual Embedding）
使用預訓練 CNN（如 ResNet、WideResNet）作為特徵提取器：
```python
# 提取中間層特徵作為 embedding
features = model.layer2(x)   # shape: [B, C, H, W]
# Adaptive Average Pooling → 固定維度向量
embedding = F.adaptive_avg_pool2d(features, 1).flatten(1)
```

#### 2. 異常偵測中的 Embedding（PatchCore 流程）
```
輸入影像 → 預訓練 CNN → patch 特徵矩陣
    → 記憶體庫（Memory Bank）← 正常樣本建立
    → 推論：計算與最近鄰的距離 → 異常分數
```

#### 3. 語言模型（Text Embedding）
- **Word2Vec / GloVe**：詞級別 embedding（靜態）
- **BERT / Sentence-BERT**：句子級別 embedding（上下文感知）
- 應用：語義搜尋、RAG 的 Retrieval 階段

### 相似度計算
```python
import numpy as np

# 歐氏距離（Euclidean Distance）
dist = np.linalg.norm(emb_a - emb_b)

# 餘弦相似度（Cosine Similarity）
cos_sim = np.dot(emb_a, emb_b) / (np.linalg.norm(emb_a) * np.linalg.norm(emb_b))

# 馬哈拉諾比斯距離（Mahalanobis）— PaDiM 使用
delta = x - mean
dist = np.sqrt(delta.T @ np.linalg.inv(cov) @ delta)
```

### 高效向量搜尋（FAISS）
```python
import faiss
index = faiss.IndexFlatL2(embedding_dim)
index.add(memory_bank)                     # 建立索引
distances, indices = index.search(query, k=1)  # 最近鄰搜尋
```

## 優點與限制
- **優點**：通用的語義表示；支援無監督學習；可配合 FAISS 進行大規模高效搜尋
- **限制**：embedding 品質高度依賴預訓練模型的領域相關性；維度詛咒需要降維處理

## 與相關概念的關係
- 上位：[[Concepts/Anomaly-Detection]]（Embedding-based 是異常偵測的主流方法之一）
- 相關：[[Concepts/PyTorch]]（Embedding 通常以 PyTorch 模型提取）
- 相關：[[Concepts/模型加速與部署]]（Embedding 推論常需加速）

## 來源
- [[Summaries/Anomaly-detection]]
