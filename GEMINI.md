# AI 行為準則

> 新 Session 必先讀本文件。

---

## 系統架構

**GTD + LYT + LLM Wiki** 三層：

| 資料夾 | 職責 | 主導 |
|--------|------|------|
| `10-Action/` | GTD 行動（Inbox、Daily、Projects） | 人類 |
| `20-Atlas/` | LYT MOC 導航地圖 | 人類建，AI 可建議 |
| `30-Wiki/` | 知識編譯（index、log、Concepts、Summaries） | AI |
| `40-Raw/` | 原始素材（Papers、Web、notes、assets） | 人類放，AI 讀 |
| `50-Artifacts/` | 產出物（Reports、Articles、Slides） | AI 起草，人類審 |
| `templates/` | 模板庫 | — |

---

## AI 權限

**允許**：讀 `40-Raw/`、讀 `10-Action/12-Daily/`（只讀）、寫 `30-Wiki/`、追加 `log.md`、起草 `50-Artifacts/`、建議 `20-Atlas/`

**禁止**：編輯 `10-Action/`、刪除 `40-Raw/`、修改 `log.md` 舊條目、翻譯技術術語（LLM/RAG/Agent/Prompt/Fine-tuning/LoRA/Transformer 等）、未授權操作

---

## 語言格式

- 繁體中文；技術術語保留英文
- 連結：`[[wiki-link]]`
- YAML frontmatter：`tags`, `created`, `updated`, `source_count`
- 標題層：`#` / `##` / `###`

---

## 標準流程

### Ingest（處理素材）
1. 讀 `40-Raw/` 目標素材
2. 建 `30-Wiki/Summaries/` 摘要頁
3. 更新相關 `Concepts/`（5–15 頁）
4. 衝突 → `log.md` 標記 `[CONFLICT]`
5. 更新 `30-Wiki/index.md`
6. 追加 `30-Wiki/log.md`

**log 格式**：
```
## [YYYY-MM-DD] ingest | 素材標題
- 摘要頁：[[Summaries/xxx]]
- 更新概念：[[Concepts/A]], [[Concepts/B]]
- 衝突：無 / [CONFLICT] 說明
```

### Query（查詢）
1. 讀 `index.md` → 相關 `Concepts/`
2. 生成答案附引用連結
3. 有價值 → 提議存入 `50-Artifacts/`

### Lint（健康檢查）
掃孤立頁、矛盾觀點、缺卡概念、建議主題 → 產出 `50-Artifacts/Reports/wiki-health-YYYY-MM-DD.md`

### Weekly Review（每週回顧）
讀 `10-Action/12-Daily/` 本週日誌（只讀）→ 提煉學習清單 → 識別重複主題 → 產出 `50-Artifacts/Reports/weekly-YYYY-WXX.md`

---

## index.md 格式

```markdown
# Wiki 知識總目錄
> 最後更新：YYYY-MM-DD | 頁面總數：N

## Concepts
| 頁面 | 摘要 | 標籤 | 來源數 |
## Summaries
| 頁面 | 原始素材 | 日期 |
```

## Concept 卡片模板（`30-Wiki/Concepts/概念名稱.md`）

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

## 重要提醒

- Session 開始：先看 `30-Wiki/index.md` 與 `log.md` 最新狀態
- Wiki 是累積資產，好答案存入 Wiki
- 矛盾 → 標 `[CONFLICT]`，通知使用者
- 不確定先問，不擅自操作
- 所有頁面須 Obsidian 相容

---
*v1.2 | 2026-09-04*
