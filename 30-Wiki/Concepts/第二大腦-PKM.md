---
tags: [concept, PKM, 知識管理, obsidian]
created: 2026-09-05
updated: 2026-09-05
source_count: 2
---

# 第二大腦（PKM）

## 核心定義

**個人知識管理（Personal Knowledge Management, PKM）**，又稱「第二大腦」，是一套系統化地收集、整理、連結、提取個人知識的方法論與工具體系。目標是讓外部的筆記系統成為思考的延伸，而非儲藏。

核心命題：**知識的價值在於連結，而非儲存。**

---

## 運作原理

### 主要方法論

#### 1. Zettelkasten（卡片盒筆記法）
- 一張卡一個**原子概念**（Atomic note）
- 用雙向連結取代分類
- 邊界由概念本身決定，不用思考「放哪裡」
- 優點：無分類焦慮；缺點：需自行拼圖找全貌

#### 2. LYT（Linked Your Thinking）—— Nick Milo
- 在 Zettelkasten 基礎上加入 **MOC（Map of Content）** 導航層
- Dots（原子卡）+ Maps（MOC 索引）+ Atlas（全局視圖）
- 適合 Obsidian 環境

#### 3. PARA（Tiago Forte）
- **P**rojects / **A**reas / **R**esources / **A**rchive
- 以「可操作性」而非主題分類
- Projects = 有截止日的目標；Areas = 長期職責領域
- 適合任務導向型工作者

#### 4. LLM Wiki Pattern（Karpathy）
- LLM 作為知識編譯器，持續維護結構化 wiki
- 主題聚合（多來源合一頁）而非原子卡片
- Schema 驅動，AI 負責 bookkeeping

### Obsidian 技術棧

```
本地 Markdown vault
├── CLAUDE.md / GEMINI.md  → AI 行為規範（Schema）
├── raw/ / 40-Raw/          → 原始素材（不可變）
├── wiki/ / 30-Wiki/        → AI 維護的知識頁
├── daily/ / 12-Daily/      → 每日日誌
└── index.md / MOC         → 導航索引
```

**Claude Code 整合流程**：`cd vault && claude` → AI 直接讀寫所有 Markdown 檔案

---

## 優點與限制

### 優點
- **知識複利**：前期投資，長期享受已整理好的知識
- **資料自主**：純 Markdown + Git，工具可替換
- **AI 友好**：本地檔案是 LLM 天然的 context window
- **離線可用**：不依賴雲端服務

### 限制
- **初期學習曲線**：Markdown、雙向連結、plugin 需 1-2 週適應
- **孤島風險**：沒有維護習慣，系統容易荒廢
- **Vibe Thinking**：過度外包給 AI → 沒有真正內化（知識進了庫，沒進腦袋）
- **Schema 設計**：LLM Wiki 模式需要定義頁面邊界，難以規模化
- **協作能力弱**：個人導向，團隊協作仍需 Notion/Confluence

---

## 與相關概念的關係

- **相似**：[[Concepts/LLM-Wiki-Pattern]]（LLM Wiki 是 PKM 的一種實作方式）
- **相似**：[[Concepts/RAG]]（個人 RAG = 小規模 PKM 知識庫查詢）
- **對比**：[[Concepts/RAG]]（PKM 是有狀態的；RAG 通常是無狀態的）
- **工具**：[[Concepts/AI-Coding-Assistant]]（Claude Code / Cursor 作為 PKM 維護介面）
- **工具**：[[Concepts/Prompt-Engineering]]（CLAUDE.md / Schema 是核心 Prompt）
- **上位**：[[Concepts/Agentic-Coding]]（AI Agent 輔助知識管理是 Agentic 應用）

## 來源
- [[Summaries/LLM-Wiki-Pattern]]
- [[Summaries/Obsidian-Claude-Code-SOP]]
