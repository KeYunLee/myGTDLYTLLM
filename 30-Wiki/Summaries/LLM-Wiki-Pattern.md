---
tags: [summary, LLM, 知識管理, PKM]
created: 2026-09-05
source_count: 3
---

# LLM Wiki Pattern — Karpathy 的知識複利架構

## 原始素材
- [[40-Raw/Web_Clippings/llm-wiki]]（Karpathy 原始 GitHub Gist）
- [[40-Raw/Web_Clippings/AI大神Karpathy的「LLM Wiki」是什麼？改善工作流、打造知識複利｜104職場力]]
- [[40-Raw/Web_Clippings/Karpathy LLM Wiki 是什麼？一個卡片盒筆記法使用者的實測]]

---

## 核心主張

Andrej Karpathy（特斯拉 Autopilot 推手、OpenAI 共同創辦人）提出 **LLM Wiki Pattern**，核心概念是將 LLM 用作「知識編譯器」而非查詢工具：

> *"The wiki is a persistent, compounding artifact."*

- **傳統 RAG**：每次查詢重新從原始文件推導答案，stateless，沒有累積
- **LLM Wiki**：LLM 一次性「編譯」原始素材為結構化 wiki，持續維護，stateful，複利增長

類比：就像編譯器把原始碼轉為二進位產物，只編譯一次，之後查詢速度快且低成本。

---

## 三層架構

### 第一層：原始來源（Raw Sources）—— 不可變
- 論文、網頁文章、影片逐字稿、會議記錄
- LLM 只讀，絕不修改
- 可從零重新推導整座 Wiki

### 第二層：Wiki（LLM 維護）
- 一系列 `*.md` 檔案，一個概念一個頁面
- LLM 全權維護，人類只閱讀
- YAML Front Matter 記錄標籤、來源、更新時間
- 使用 `[[slug]]` 交叉引用

### 第三層：Schema（治理層）
- JSON 或 CLAUDE.md/AGENTS.md 等設定檔
- 定義「追蹤哪些概念、如何命名」
- 人類唯一需要主動管理的部分
- 新增 PageSpec → 下次 ingest 自動建頁面

---

## 三大操作流程

### Ingest（匯入）
1. 放入新素材到 raw/ 目錄
2. LLM 讀取、提取關鍵資訊
3. 建立摘要頁
4. 更新相關 10–15 個 wiki 頁面
5. 追加 log.md 記錄

### Query（查詢）
1. LLM 讀 index.md 找相關頁
2. 合成答案附引用
3. **有價值的答案本身可回存為新 wiki 頁**（知識複利循環）

### Lint（健康檢查）
- 找矛盾、找孤立頁、找過期資訊
- 主動識別「被多次提到但沒獨立頁面」的概念缺口
- 建議新的探究方向與來源

---

## 優點與缺點

### 優點
- **知識複利**：50 篇論文的 Wiki 比同等 RAG 深度遠超
- **查詢速度快**：合成工作在 ingest 時已完成
- **人類可讀**：純 Markdown，可 Git 版本管理
- **出處明確**：每頁記錄來源，可追蹤溯源
- **成本效率**：Prompt 快取機制壓低 API 費用

### 缺點
- **匯入成本高**：每個來源呼叫 Claude API 2 次以上
- **依賴 LLM 品質**：幻覺會寫進 wiki 傳播
- **Schema 設計難**：頁面邊界不易定義，容易過廣或過窄
- **Model Collapse 風險**：LLM 反覆讀自己寫的內容，細節可能被磨平
- **頁面過時**：Schema 描述模糊時，新素材可能漏更新舊頁

---

## 與 Zettelkasten/LYT 的比較

| 維度 | LLM Wiki（Karpathy）| Zettelkasten/LYT |
|------|---------------------|-------------------|
| 卡片單位 | 主題聚合（多來源合一頁）| 原子概念（一卡一概念）|
| 邊界決定 | 人定義 Schema | 概念本身 |
| AI 角色 | 全程維護 wiki | 輔助連結、摘要 |
| 分類問題 | 需決定「放哪張頁」| 連結取代分類 |
| 複利方式 | 頁面內容越來越豐富 | MOC 越來越密集 |

> *Karpathy：Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase.*

---

## 歷史縱深

Karpathy 把此 pattern 連結至 Vannevar Bush 1945 年的 **Memex** 願景——「人的延伸記憶系統」。Bush 的問題是「誰來維護？」，LLM 解答了這個問題：維護成本近乎零。

---

## 相關概念
- [[Concepts/LLM-Wiki-Pattern]]
- [[Concepts/RAG]]
- [[Concepts/第二大腦-PKM]]
- [[Concepts/Prompt-Engineering]]
