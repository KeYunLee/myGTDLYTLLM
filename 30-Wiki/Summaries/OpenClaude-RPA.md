---
tags: [summary, rpa, agentic-coding, LLM]
created: 2026-09-04
source: "[[40-Raw/Web_Clippings/OpenClaude]]"
---

# OpenClaude RPA 自動化工作流

## 原始素材
- 來源：[[40-Raw/Web_Clippings/OpenClaude]]
- 類型：實作筆記 / HackMD 剪輯

## 核心摘要

OpenClaude 是以 Anthropic Claude 為底層的 Agentic Coding 工具，透過 **Azure AI Foundry** 橋接 Claude API，並搭配 Windows Control Skill 實現 RPA（Robotic Process Automation）自動化。

## 關鍵技術要點

### 環境設定
- 使用 `.env` 檔注入 Azure Foundry 端點與 API Key
- 關鍵環境變數：`CLAUDE_CODE_USE_FOUNDRY=1`、`ANTHROPIC_FOUNDRY_BASE_URL`
- 停用 thinking：`DISABLE_INTERLEAVED_THINKING=1`、`CLAUDE_CODE_DISABLE_THINKING=1`（Azure Foundry 不支援 interleaved thinking）

### Skill 架構
- Windows Control Skill 放置於 `~/.openclaude/skills/openclaw-skills-windows-control-1.0.1`
- 提供 `click.py`、`key_press.py`、`type_text.py`、`screenshot.py` 等腳本
- Agent 截圖後「看圖取座標」→ 執行 click，形成視覺-動作迴圈

### RPA 流程設計模式
1. **截圖** → Agent 視覺解析畫面座標
2. **執行動作**（click / type / key_press）
3. **再截圖確認**結果
4. 迭代直到任務完成

### 封裝成自動化腳本
- 用 `subprocess` 組合 skill 內建的 `.py` 腳本
- 座標硬編碼（1920×1080 校準），需重新截圖校正
- `set_clipboard + ctrl+v` 取代 `type_text.py`，避免 IME / 特殊字元問題

## 相關概念
- [[Concepts/RPA-Agentic]]
- [[Concepts/Agentic-Coding]]
- [[Concepts/Prompt-Engineering]]
