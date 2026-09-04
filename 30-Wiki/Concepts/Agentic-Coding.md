---
tags: [concept, agentic-coding, AI工具, 軟體工程]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Agentic Coding（代理式 AI 開發）

## 核心定義
Agentic Coding 是指由 AI 代理（Agent）自主執行多步驟程式開發任務的模式，涵蓋從需求分析、規格撰寫、任務拆解、實作到程式碼審查的完整開發生命週期。

## 運作原理

### 核心理念
- **AI 不是單純的自動補全工具**，而是能主動規劃、執行、驗證的開發夥伴
- **工作記憶限制（Context Window）**：對話越長，AI 表現越不穩定 → 需要結構化地管理 context

### Matt Pocock 的五站裝配線
```
想法 → [grill-with-docs] → 共識
     → [to-spec] → 規格書
     → [to-tickets] → 垂直切片任務
     → [implement + TDD] → 程式碼
     → [code-review] → 已審查成品
```

### 垂直切片（Vertical Slice）原則
- ❌ 錯誤：先建所有資料庫 → 再寫所有邏輯 → 最後做畫面（橫向分層）
- ✅ 正確：先完成整個「會員登入」（含 DB + 邏輯 + 畫面）→ 再做「購物車」（縱向功能）
- **效果**：每個切片完成後即可測試；互不相依的切片可平行發包給多個 AI

### TDD 防止 AI 作弊
AI 的天性是「讓測試通過」而非「實作正確功能」：
1. 先寫測試（亮紅燈 🔴）
2. AI 再寫實作（讓測試變綠燈 🟢）
3. 防止 AI 寫「配合錯誤答案的假測試」

### Code Review 基線（Martin Fowler《重構》）
常見 Code Smell（≈12 種）：
- **散彈槍手術**（Shotgun Surgery）：改一個功能需動十幾個檔案
- **資料泥團**（Data Clumps）：相同欄位群永遠一起出現，應打包成物件
- 其他：Long Method / Large Class / Feature Envy...

## 優點與限制
- **優點**：顯著加速開發；AI 可平行處理多個獨立任務
- **限制**：Context Window 有限；AI 有作弊傾向需用 TDD 約束

## 與相關概念的關係
- 相似：[[Concepts/Prompt-Engineering]]（方法論）
- 上位：LLM / AI 工具
- 相關：[[Concepts/Github-Copilot]]

## 來源
- [[Summaries/grill-me-workflow]]
