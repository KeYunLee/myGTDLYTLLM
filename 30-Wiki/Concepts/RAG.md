---
tags: [concept, RAG, LLM, embedding, 檢索]
created: 2026-09-04
updated: 2026-09-05
source_count: 2
---

# RAG（Retrieval-Augmented Generation）

## 核心定義

RAG 是一種將「外部知識檢索」與「大型語言模型生成」結合的架構模式。LLM 不再只依賴訓練時的靜態知識，而是在推論時動態從外部資料庫中取出相關文件片段（chunks），做為 Context 輸入給模型生成答案。

## 運作原理

### 基礎流程
```
使用者問題 → 向量化（Embedding）→ 向量資料庫相似度搜尋
           → 取出 Top-K chunks → 組成 Prompt → LLM 生成答案
```

### 進階：Reflexion 反思迴圈
```
Draft → Evaluator（OK/NG）→ [NG] Reflection Prompt → LLM Refine → 再 Evaluate
                          → [OK] 輸出最終答案
```

### 混合檢索（Hybrid Search）
- **稀疏檢索**（BM25）：精確匹配術語、代號
- **稠密檢索**（向量相似度）：語義相關段落
- 兩路並行 → 結果合併，大幅提升召回率

### 分層篩選與 Re-ranking
1. 第一輪：向量相似度取 Top 20-30 候選
2. 第二輪：Cross-Encoder 或 MMR（Maximal Marginal Relevance）重排序

## 優點與限制

### 優點
- 知識可即時更新（不需重訓模型）
- 答案有來源可追溯，降低幻覺（Hallucination）
- 適合企業私有文件知識庫

### 限制
- 檢索品質決定生成品質（Garbage in, garbage out）
- 長文檔切分策略影響大
- 多文檔跨文件比較時，需額外設計字段歸一化邏輯

## 最佳化策略

| 策略 | 做法 |
|------|------|
| 自適應切分 | 語義/段落切片，附加元數據過濾 |
| 嵌入模型 | text-embedding-ada-002 或領域微調模型 |
| 查詢擴展 | 同義詞擴充、Multi-step Retriever |
| 向量庫調優 | HNSW ef 參數、FAISS index_factory |

## RAG vs LLM Wiki Pattern

Karpathy 的 [[Concepts/LLM-Wiki-Pattern]] 提出了對 RAG 的根本批評：

| 維度 | 傳統 RAG | LLM Wiki |
|------|---------|---------|
| 狀態 | Stateless（無狀態）| Stateful（有狀態）|
| 知識累積 | 每次重新推導，永不複利 | 一次編譯，持續豐富 |
| 建構成本 | 低（索引即用）| 高（需 ingest + schema）|
| 查詢成本 | 高（每次重新合成）| 低（已預先合成）|
| 人類可讀 | 不易（向量 + chunks）| 容易（純 Markdown）|
| 適合場景 | 即時、大量、不需累積 | 精心策展、長期主題 |

> 個人知識庫 < 50 萬字時，可用「全 vault 讓 Claude Code 直接讀」取代向量 RAG 基礎設施。

## 與相關概念的關係
- 相似：[[Concepts/Embedding-方法]]（RAG 的向量化核心技術）
- 對比：[[Concepts/Prompt-Engineering]]（RAG 是動態 Context，Prompt 是靜態設計）
- 對比：[[Concepts/LLM-Wiki-Pattern]]（Wiki 是有狀態的替代方案）
- 上位：[[Concepts/AI模型部署]]（RAG 是 LLM 系統的檢索層架構）
- 相關：[[Concepts/Agentic-Coding]]（Agent + RAG = 更強的知識型 Agent）

## 來源
- [[Summaries/RAG-技術優化]]
- [[Summaries/LLM-Wiki-Pattern]]
