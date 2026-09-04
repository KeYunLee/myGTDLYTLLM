---
tags: [concept, AI工具, coding-assistant, github-copilot, LLM]
created: 2026-09-04
updated: 2026-09-04
source_count: 2
---

# AI Coding Assistant（AI 程式開發輔助工具）

## 核心定義
AI Coding Assistant 是整合大型語言模型（LLM）的程式開發輔助工具，能在 IDE 或命令列環境中提供程式碼補全、解釋、重構、測試生成、問題診斷等功能。代表產品包括 GitHub Copilot、Cursor、Continue 等。

## 運作原理

### 主要工作模式

| 模式 | 說明 | 代表工具 |
|------|------|---------|
| Inline completion | 即時補全下一行/區塊 | GitHub Copilot（IDE 外掛）|
| Chat / Q&A | 對話式詢問程式問題 | Copilot Chat、Claude |
| Agent / Agentic | 自主多步驟執行任務 | Copilot CLI `--allow-all-tools` |
| Code review | 分析並評論程式碼品質 | Copilot code review |

### GitHub Copilot CLI 重點
```bash
# 安裝（需 Node.js 18+）
npm install -g @githubnext/github-copilot-cli

# 企業環境：設定 Proxy
export HTTPS_PROXY=http://proxy.corp.com:8080
export NODE_EXTRA_CA_CERTS=/path/to/corp-ca.crt

# 開放 Agent 工具權限
gh copilot suggest --allow-all-tools "幫我寫一個 Dockerfile"
```

### 使用原則
- **Prompt 品質決定輸出品質**：給予充足上下文（目標、限制、技術棧）
- **TDD 配合使用**：讓 AI 先看測試再實作，減少「假實作」
- **分段驗證**：每個垂直切片完成後立即驗收，避免錯誤累積

## 優點與限制
- **優點**：顯著提升開發速度；降低搜尋文件的時間成本；支援多種語言與框架
- **限制**：企業環境需處理 Proxy / CA 憑證；輸出需人工驗證；context window 有限

## 與相關概念的關係
- 相似：[[Concepts/Prompt-Engineering]]（決定 AI 輸出品質的方法論）
- 相似：[[Concepts/Agentic-Coding]]（AI Coding Assistant 的進階應用模式）
- 下位：[[Concepts/Github-Copilot]]（具體產品實例）

## 來源
- [[Summaries/Caveman-prompt]]
- [[Summaries/Github-Copilot]]
