# AI 行為準則

> 新 Session 必先讀本文件。讀 `30-Wiki/index.md` 與 `log.md` 取最新狀態。

---

## 系統架構

| 資料夾 | 職責 | 主導 |
|--------|------|------|
| `10-Action/11-Inbox/` | GTD 收集箱：草稿、臨時想法 | 人類放，AI 唯讀 |
| `10-Action/12-Daily/` | 每日日誌 | 人類主導，AI 可更新 |
| `10-Action/13-Projects/` | 跨日專案追蹤 | 人類主導，AI 可更新 |
| `20-Atlas/` | LYT MOC 導航 | 人類建，AI 可建議 |
| `30-Wiki/` | 知識編譯（index、log、Concepts、Summaries） | AI |
| `40-Raw/` | 原始素材（Papers、Web、notes、assets） | 人類放，AI 唯讀 |
| `50-Artifacts/` | 產出物（Reports、Articles、Slides） | AI 起草，人類審 |
| `templates/` | 模板庫 | — |

---

## AI 權限

**允許**：
- `11-Inbox/`：唯讀
- `12-Daily/`、`13-Projects/`：讀寫（修改既有內容前，先告知變更摘要，待確認後執行）
- `40-Raw/`：唯讀；`30-Wiki/`：寫；`log.md`：追加；`50-Artifacts/`：起草；`20-Atlas/`：建議

**禁止**：刪除 `10-Action/` 或 `40-Raw/` 任何檔案、修改 `log.md` 舊條目、翻譯技術術語（LLM/RAG/Agent/Prompt/Fine-tuning/LoRA/Transformer 等）、未授權操作

---

## 語言格式

繁體中文；技術術語保留英文；連結 `[[wiki-link]]`；YAML frontmatter：`tags`, `created`, `updated`, `source_count`；標題層 `#` / `##` / `###`；所有頁面 Obsidian 相容。

---

## 標準流程

### Process Inbox
1. 唯讀 `10-Action/11-Inbox/`
2. 分類：
   - 單次任務 → 追加 `12-Daily/YYYY-MM-DD.md`
   - 跨日專案 → 更新/新建 `13-Projects/<專案名>.md`
   - 知識素材 → 觸發 Ingest
3. 告知變更摘要，待確認後寫入
4. Inbox 原檔不得刪除，由人類自行清理

### Ingest
1. 讀 `40-Raw/` 目標素材
2. 建 `30-Wiki/Summaries/` 摘要頁
3. 更新相關 `Concepts/`（5–15 頁）
4. 衝突 → `log.md` 標記 `[CONFLICT]`，通知使用者
5. 更新 `30-Wiki/index.md`
6. 追加 `30-Wiki/log.md`

```
## [YYYY-MM-DD] ingest | 素材標題
- 摘要頁：[[Summaries/xxx]]
- 更新概念：[[Concepts/A]], [[Concepts/B]]
- 衝突：無 / [CONFLICT] 說明
```

### Query
1. 讀 `index.md` → 相關 `Concepts/`
2. 生成答案附引用連結
3. 有價值 → 提議存入 `50-Artifacts/`

### Lint
掃孤立頁、矛盾觀點、缺卡概念 → 產出 `50-Artifacts/Reports/wiki-health-YYYY-MM-DD.md`

### Weekly Review
讀 `12-Daily/` 本週日誌（唯讀）→ 提煉學習清單、識別重複主題 → 產出 `50-Artifacts/Reports/weekly-YYYY-WXX.md`

---

## 模板

### index.md
```markdown
# Wiki 知識總目錄
> 最後更新：YYYY-MM-DD | 頁面總數：N

## Concepts
| 頁面 | 摘要 | 標籤 | 來源數 |
## Summaries
| 頁面 | 原始素材 | 日期 |
```

### Concept 卡片（`30-Wiki/Concepts/概念名稱.md`）
```markdown
---
tags: [concept, <主題>]
created: YYYY-MM-DD
updated: YYYY-MM-DD
source_count: N
---
# 概念名稱
## 核心定義
## 運作原理
## 優點與限制
## 與相關概念的關係
- 相似：[[Concepts/xxx]]
- 對比：[[Concepts/yyy]]
- 上位：[[Concepts/zzz]]
## 來源
- [[Summaries/xxx]]
```

---
*v1.4 | 2026-09-04*
