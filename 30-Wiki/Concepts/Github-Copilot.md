---
tags: [concept, github-copilot, AI工具, 開發工具]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Github Copilot

## 核心定義
GitHub Copilot 是由 GitHub 與 OpenAI 共同開發的 AI 程式設計輔助工具，整合於 IDE（VS Code、JetBrains 等）與命令列（Copilot CLI），提供程式碼補全、生成、解釋等功能。

## 運作原理

### Copilot CLI 架構
- **CLI 模式**：在命令列中直接與 AI 互動，可執行 bash 指令、操作檔案
- `--allow-all-tools`：授權 AI 直接操作目錄與子目錄（含刪除），**無需逐步確認**，風險高

### 企業環境安裝（代理 Proxy）
```powershell
# 每次使用前需設定
$env:HTTPS_PROXY = "http://10.248.15.7:3128"
$env:HTTP_PROXY  = "http://10.248.15.7:3128"
$env:NO_PROXY    = "localhost,127.0.0.1"

# 安裝
npm install -g @github/copilot
npm install -g @fission-ai/openspec@latest

# 啟動
copilot --allow-all-tools
```

### 帳號類型
- **個人帳號**：github.com 登入
- **企業帳號（AUO）**：https://github.com/enterprises/AUOCorporation

### 登入流程（Device Code Flow）
1. CLI 內執行 `/login`
2. 選企業帳號
3. 取得一次性 code（如 5D8A-641F）
4. 前往 https://github.com/login/device 輸入 code
5. 等待約 1 分鐘即可使用

## 優點與限制
- **優點**：深度整合 IDE；支援企業 SSO；CLI 可自動化操作
- **限制**：企業環境需每次設定 proxy；`--allow-all-tools` 有資安風險

## 與相關概念的關係
- 相似：[[Concepts/Agentic-Coding]]（AI 代理開發）
- 對比：[[Concepts/Prompt-Engineering]]（Copilot 使用 inline context，非 system prompt）
- 上位：AI Coding Assistant

## 來源
- [[Summaries/Github-Copilot]]
