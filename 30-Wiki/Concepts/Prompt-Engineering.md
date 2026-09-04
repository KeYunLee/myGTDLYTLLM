---
tags: [concept, prompt-engineering, LLM, AI工具]
created: 2026-09-04
updated: 2026-09-04
source_count: 2
---

# Prompt Engineering

## 核心定義
Prompt Engineering 是設計與優化輸入提示詞（Prompt），以引導大型語言模型（LLM）輸出期望結果的方法論。

## 運作原理

### System Prompt 設計模式

#### Caveman 模式（高密度輸出）
目標：去除廢話，保留所有技術實質
```markdown
Respond terse like smart caveman. All technical substance stay. Only fluff die.
Rules:
- Drop: articles, filler words, pleasantries, hedging
- Fragments OK. Technical terms exact. Code unchanged.
- Pattern: [thing] [action] [reason]. [next step].
Switch level: /caveman lite|full|ultra|wenyan
```
- 適合：熟悉技術的使用者，需要高資訊密度
- 自動恢復：安全警告、不可逆操作時切回正常模式

#### Skill-based 架構（grill-me 系統）
五站裝配線：
1. **grill-with-docs**：拷問出共識（Ubiquitous Language + ADR）
2. **to-spec**：共識 → 規格書（不含過期的路徑/程式碼）
3. **to-tickets**：規格 → 垂直切片任務（Vertical Slice）
4. **implement**：TDD 防作弊（先測試、後實作）
5. **code-review**：雙代理並行審查（規範 + 規格）

### 關鍵設計原則
- **Ubiquitous Language（通用語言）**：人、AI、程式碼三方統一術語
- **ADR（架構決策紀錄）**：記錄難以回頭的決定
- **Vertical Slice**：按使用者功能（非技術架構）拆任務
- **TDD**：先寫測試（紅燈）→ 再實作（綠燈），防 AI 作弊

### Wayfinder（大型規劃）
- 適用：規模大到一次對話裝不下的計畫
- 在 GitHub Issues 建立總覽地圖
- 子票類型：research / grilling / prototype / task

## 優點與限制
- **優點**：在不修改模型的情況下顯著改善輸出品質
- **限制**：需要了解模型特性；不同模型的 prompt 效果差異大

## 與相關概念的關係
- 相似：[[Concepts/AI-Coding-Assistant]]（應用場景）
- 上位：LLM / 大型語言模型
- 相關：[[Concepts/Agentic-Coding]]

## 來源
- [[Summaries/Caveman-prompt]]
- [[Summaries/grill-me-workflow]]
