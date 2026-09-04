---
tags: [summary, github-copilot, AI工具, 開發工具]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Github Copilot CLI 安裝摘要

## 原始素材
- 來源：[[40-Raw/Web_Clippings/Github Copilot]]
- Source URL：https://hackmd.io/skK8UX6MSRS1uUuTFXnl_A

## 摘要

本篇記錄企業環境（AUO）下安裝與使用 GitHub Copilot CLI 的完整步驟。

### 安裝流程

1. **開啟 PowerShell**（Windows 或 VSCode 內建終端）
2. **設定 Proxy 環境變數**：
   ```powershell
   $env:HTTPS_PROXY = "http://10.248.15.7:3128"
   $env:HTTP_PROXY  = "http://10.248.15.7:3128"
   $env:NO_PROXY    = "localhost,127.0.0.1"
   ```
3. **設定 npm PATH 與 PowerShell 7 PATH**（自動判斷是否已加入）
4. **安裝套件**：
   ```bash
   npm install -g @github/copilot
   npm install -g @fission-ai/openspec@latest
   ```
5. **登入企業帳號**：https://github.com/enterprises/AUOCorporation
6. **每次執行前需重新 import 環境變數**：
   ```bash
   copilot --allow-all-tools
   ```
7. **CLI 內登入**：`/login` → 企業帳號 → 輸入一次性 code

### 重要注意事項
- `--allow-all-tools` 代表授權 AI 直接操作目錄（含刪除），使用需謹慎
- 企業 GitHub Copilot 使用 AUOCorporation 企業帳號登入

## 關聯概念
- [[Concepts/Github-Copilot]]
- [[Concepts/AI-Coding-Assistant]]
