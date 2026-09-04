---
tags: [summary, prompt-engineering, AI工具, agentic-coding, workflow]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# grill-me AI 開發工作流摘要

## 原始素材
- 來源：[[40-Raw/Web_Clippings/grill-me]]
- Source URL：https://hackmd.io/3moTwV49TNG3bdTJebWMiA

## 摘要

**grill-me** 是 Matt Pocock 設計的一套 AI 輔助軟體開發工作流，包含五站「裝配線」，將「想法」轉化為「成品」。

### 五站裝配線

#### 第一站：grill-with-docs（拷問出共識）
- 產出兩份文件：
  1. **名詞表（Ubiquitous Language）**：讓人、AI、程式碼三方說同一種語言（Domain-Driven Design 概念）
  2. **架構決策紀錄（ADR）**：記錄難以回頭的決定，讓 AI「越用越懂你」

#### 第二站：to-spec（把共識寫成規格書）
- 原則：規格書**不放具體路徑與程式碼片段**（容易過期）
- 例外：只有 prototype 產生的片段比文字更精確時，才節錄

#### 第三站：to-tickets（把規格拆成任務）
- 核心方法：**垂直切片（Vertical Slice）**
- 強制 AI 按「使用者功能」拆任務（非按技術架構）
- 每個切片完整可測試；互不相依的任務可平行發包給多個 AI

#### 第四站：implement（TDD 防作弊）
- 核心：**測試驅動開發（TDD）**
- 先寫測試（亮紅燈）→ 再寫功能（變綠燈）
- 防止 AI「配合錯誤答案寫假測試」

#### 第五站：code-review（換腦袋審查）
- 兩個平行子代理：① 符合專案規範？② 符合原始規格？
- 以 Martin Fowler《重構》的 ~12 種「爛 code 症狀」為基線
- 典型症狀：散彈槍手術（Shotgun Surgery）、資料泥團（Data Clumps）

### 大型規劃：Wayfinder（v1.1）
- 適用場景：計畫大到一次對話裝不下
- 在 GitHub Issues 建立總覽地圖，拆成四種子票：research / grilling / prototype / task

### 輔助指令
- `/setup-matt-pocock-skills`：初始化技能
- `/ask-matt`：探索使用方式
- `/improve-codebase-architecture`：定期檢查架構改善機會
- `/handoff`：撰寫可移轉的 Markdown 文件

## 關聯概念
- [[Concepts/Prompt-Engineering]]
- [[Concepts/Agentic-Coding]]
- [[Concepts/TDD]]
