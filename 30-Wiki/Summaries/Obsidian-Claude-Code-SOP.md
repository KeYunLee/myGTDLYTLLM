---
tags: [summary, obsidian, PKM, claude-code, 工作流]
created: 2026-09-05
source_count: 1
---

# Obsidian + Claude Code 第二大腦完整建置 SOP

## 原始素材
- [[40-Raw/Web_Clippings/Obsidian + Claude Code 第二大腦完整建置：90 分鐘 7 步驟 SOP｜恆遠數位科技]]

---

## 核心架構

三層解耦設計：
- **Obsidian** = 儲存層（IDE）
- **Markdown 檔案** = 資料層（Codebase）
- **Claude Code** = 智能層（Programmer）

任一層可替換，資料永遠在本地。

---

## Obsidian vs Notion 關鍵差異

| 維度 | Notion | Obsidian |
|------|--------|----------|
| 儲存 | 雲端 JSON | 本地 Markdown |
| AI 整合 | Notion AI（付費）| Claude Code/Cursor 直連 |
| 搜尋速度 | 500 頁後變慢 | 1 萬頁仍秒搜 |
| 離線 | 部分功能 | 100% 離線 |
| 協作 | 原生即時協作 | Git/iCloud 同步 |
| 月費 | $20/月（Plus+AI）| $0-4/月 |

**切換時機**：筆記 < 200 頁 + 需要團隊協作 → Notion；筆記 > 500 頁 + 深度 AI 整合 → Obsidian

---

## 7 步驟 SOP（總計 90 分鐘）

### Step 1：安裝 Obsidian + 建立 Vault（10 分鐘）
- 下載 obsidian.md，建立 Vault 放入 iCloud/Dropbox 同步目錄
- 命名用 evergreen 名：`my-brain`、`notes`

### Step 2：建立 PARA + Daily 資料夾結構（5 分鐘）
```
my-brain/
├── 0-inbox/        # 速記、未整理
├── 1-projects/     # 進行中專案
├── 2-areas/        # 持續關注領域
├── 3-resources/    # 參考資料
├── 4-archive/      # 已完成或不活躍
└── daily/          # YYYY-MM-DD.md
```

### Step 3：開啟核心 Plugin（10 分鐘）
- Daily notes、Templates、Backlinks、Outline、Tag pane、Bases（2026 新功能）
- 先不裝 Community plugin，跑兩週再評估

### Step 4：設計每日筆記模板（15 分鐘）
`templates/daily-template.md` 五個區塊：
1. **三件事**（聚焦）
2. **行事曆與會議**
3. **學到的事**（長期複利）
4. **想法閃光**（靈感捕捉）
5. **明天的我看了會感謝的記錄**（Context 傳遞）

### Step 5：裝 Claude Code 並指向 Vault（20 分鐘）
```bash
npm install -g @anthropic-ai/claude-code
cd ~/my-brain && claude
```
建立 `CLAUDE.md` 定義規則（讀/寫權限、語言規範、期望行為）

### Step 6：建立 5 個高頻 Prompt（20 分鐘）
1. 週報自動產出（掃 daily/ → 分類彙整）
2. 跨筆記主題識別（找 30 天內重複出現關鍵字）
3. 想法歸類（inbox → 建議分類，不直接搬）
4. 會議準備（找相關筆記 → 整理重點與問題）
5. 找連結機會（識別可加 wiki-link 的位置）

### Step 7：每天 30 分鐘晨間儀式（10 分鐘設定）
| 時間 | 動作 |
|------|------|
| 0-5 分 | Daily note 套模板 |
| 5-10 分 | 從昨日「明天的我」開始今日三件事 |
| 10-15 分 | Claude Code 跑「會議準備」|
| 15-25 分 | 處理 inbox/ |
| 25-30 分 | 瀏覽主題識別結果 |

---

## 進階工作流

### 跨筆記主題演化追蹤
Claude Code 跨多篇 daily 找「客戶 A 的決策模式演化」等主題時間軸，Notion AI 無法做到。

### 個人 RAG 系統
Vault < 50 萬字時，直接讓 Claude Code 全讀，不需要向量資料庫。

### 知識資產複利化
每季識別 5 個重複主題 → 各寫一篇 1500 字 deep dive → 年積 20 篇個人文章。

---

## 從 Notion 遷移的提醒
1. **只搬最近 6 個月**高頻筆記，80% 的舊筆記過去半年沒打開
2. **Database 要重新設計**，不是 1-to-1 對應（用 Bases + frontmatter 重建）
3. **團隊協作繼續用 Notion**，Obsidian 主打個人第二大腦

---

## 相關概念
- [[Concepts/第二大腦-PKM]]
- [[Concepts/LLM-Wiki-Pattern]]
- [[Concepts/AI-Coding-Assistant]]
- [[Concepts/Prompt-Engineering]]
