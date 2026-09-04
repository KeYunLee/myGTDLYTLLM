---
tags: [concept, LLM, 知識管理, PKM]
created: 2026-09-05
updated: 2026-09-05
source_count: 3
---

# LLM Wiki Pattern

## 核心定義

由 Andrej Karpathy（OpenAI 共同創辦人、特斯拉 Autopilot 架構推手）提出的個人知識庫建構模式。核心是將 LLM 用作**知識編譯器**，把原始素材一次性「編譯」為結構化、互連的 wiki，並持續維護，而非每次查詢重新推導答案。

> *"Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase."*

---

## 運作原理

### 根本差異：時間維度

| 模式 | 狀態 | 知識累積 |
|------|------|---------|
| 傳統 RAG | Stateless（無狀態）| 每次重新推導，永不累積 |
| LLM Wiki | Stateful（有狀態）| 一次編譯，持續複利增長 |

### 三層架構

1. **Raw Sources（不可變層）**
   - 論文、文章、影片逐字稿、會議記錄
   - LLM 只讀、不改；可從零重建整座 Wiki

2. **Wiki（LLM 維護層）**
   - 一系列 `*.md` 檔案，一個概念一個頁面
   - LLM 全權建立與維護，人類只閱讀
   - YAML Front Matter + `[[slug]]` 交叉引用

3. **Schema（治理層）**
   - `CLAUDE.md`、`AGENTS.md` 或 JSON 設定檔
   - 定義哪些概念要追蹤、命名規則、工作流程
   - **人類唯一需要主動管理的部分**

### 三大操作

- **Ingest**：新素材進來 → LLM 讀取 → 建摘要 → 更新 10-15 個 wiki 頁 → 追加 log
- **Query**：讀 index 找相關頁 → 合成答案 → 有價值答案可回存為新頁（複利循環）
- **Lint**：定期健康檢查——找矛盾、孤立頁、概念缺口、過期資訊

---

## 優點與限制

### 優點
- **知識複利**：Wiki 越成熟，查詢品質越高，遠超同等 RAG
- **查詢速度快**：合成工作已在 ingest 時完成，查詢輕量
- **人類可讀**：純 Markdown + Git，完整稽核軌跡
- **出處透明**：每頁記錄來源，錯誤可追溯
- **維護成本近零**：LLM 不會遺忘更新交叉引用

### 限制
- **匯入成本高**：每個來源至少呼叫 Claude API 兩次（路由 + 合成）
- **LLM 幻覺風險**：錯誤寫入 wiki 後持續影響後續查詢
- **Schema 設計難**：頁面邊界不易定義
- **Model Collapse**：LLM 長期讀自己的輸出再改寫，細節逐漸磨平（2024 Nature 論文）
- **Vibe Thinking 風險**：把整理外包等於把思考外包
- **線性掃描限制**：頁面數超過 ~500 後需引入 FAISS / ChromaDB

---

## 與相關概念的關係

- **相似**：[[Concepts/RAG]]（都從外部知識庫合成答案）
- **對比**：[[Concepts/RAG]]（Wiki 是有狀態的；RAG 是無狀態的）
- **對比**：[[Concepts/第二大腦-PKM]]（Zettelkasten 原子卡片 vs Wiki 主題聚合）
- **上位**：[[Concepts/Prompt-Engineering]]（Schema / CLAUDE.md 是核心 Prompt 設計）
- **工具**：[[Concepts/AI-Coding-Assistant]]（Claude Code / Cursor 作為維護介面）

## 來源
- [[Summaries/LLM-Wiki-Pattern]]
