---
tags: [summary, RAG, LLM, embedding]
created: 2026-09-04
source: "[[40-Raw/Web_Clippings/RAG 技術]]"
---

# RAG 技術優化策略

## 原始素材
- 來源：[[40-Raw/Web_Clippings/RAG 技術]]
- 類型：技術整理筆記 / HackMD 剪輯

## 核心摘要

本文整理 RAG（Retrieval-Augmented Generation）系統的進階優化技術，涵蓋「檢索精準度提升」與「跨文檔比較表格生成」兩大主題，並包含 Reflexion 反思迴圈架構。

## 關鍵技術要點

### RAG Reflexion 反思模式
- 流程：Draft → Evaluator（OK/NG）→ 若 NG 進入 Reflection → Refine → 再次 Evaluate
- 目的：自我修正答案品質，避免一次生成錯誤

### 提高檢索精準度的五大策略
1. **自適應切分（Adaptive Chunking）**：語義/段落切片，附加元數據（標題、類別）過濾
2. **混合檢索（Hybrid Search）**：BM25 稀疏檢索 + 向量稠密檢索，提升召回率
3. **分層篩選（Top-N + Re-ranking）**：先取 Top 20-30，再用 Cross-Encoder 或 MMR 重排序
4. **查詢擴展（Query Expansion）**：同義詞擴充 + Multi-step Retriever 多輪迭代
5. **向量數據庫調優**：HNSW 索引參數、Pinecone/Weaviate/Redis

### 跨文檔比較表格生成
- 用 GPT-4 + Function Calling 從各文件抽取結構化 JSON
- 建立同義詞映射表統一字段名稱
- 輸出 Markdown/HTML 表格，或交由 Pandas 程式化生成

### 技術棧
- **工具鏈**：LangChain、LlamaIndex（GPT Index）
- **向量庫**：Pinecone、Weaviate、FAISS、Redis

## 相關概念
- [[Concepts/RAG]]
- [[Concepts/Embedding-方法]]
- [[Concepts/Prompt-Engineering]]
