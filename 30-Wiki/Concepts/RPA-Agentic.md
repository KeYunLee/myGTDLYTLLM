---
tags: [concept, rpa, agentic-coding, LLM, AI工具]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# RPA Agentic（AI 代理式 RPA）

## 核心定義

傳統 RPA（Robotic Process Automation）依賴硬編碼座標與流程腳本；**AI Agentic RPA** 則讓 LLM Agent 透過「截圖→視覺解析→動作執行」的迴圈，動態判斷 UI 狀態並決策下一步操作，無需預先錄製或硬寫座標。

OpenClaude 是典型實作：以 Claude（透過 Azure AI Foundry）為推理核心，搭配 Windows Control Skill 控制桌面 GUI。

## 運作原理

### 視覺-動作迴圈
```
截圖（screenshot.py）→ LLM 視覺解析（看圖取座標）
  → 執行動作（click.py / key_press.py / type_text.py）
  → 再截圖確認 → 迭代直到任務完成
```

### 工具組成（Windows Control Skill）
| 腳本 | 功能 |
|------|------|
| `screenshot.py` | 截圖輸出 base64 PNG |
| `click.py <x> <y>` | 滑鼠點擊指定座標 |
| `key_press.py <combo>` | 按鍵組合（Ctrl+A、Enter 等）|
| `type_text.py <text>` | 鍵盤輸入文字 |

### 關鍵技巧
- **Clipboard Paste**：`clip.exe + ctrl+v` 避免 IME 攔截特殊字元
- **座標校正**：1920×1080 螢幕下截圖，座標若版面改變須重新校正
- **CLAUDE.md System Prompt**：定義 proxy 設定、虛擬環境優先順序等行為規則

## 優點與限制

### 優點
- 不需要預先錄製，UI 版面變動後仍可自適應
- LLM 可理解中文 UI、複雜邏輯判斷（如「是否需要登入」）
- 可封裝為可重用腳本，降低維護成本

### 限制
- 依賴 LLM API（Azure Foundry），有延遲與費用
- 截圖座標硬編碼仍有版面變動風險
- Azure Foundry 不支援 thinking 模式，需停用相關功能

## 與相關概念的關係
- 相似：[[Concepts/Agentic-Coding]]（同為 AI Agent 驅動的自動化）
- 對比：[[Concepts/Prompt-Engineering]]（RPA Agentic 的 Prompt 以視覺解析為主）
- 上位：[[Concepts/AI-Coding-Assistant]]（Agentic RPA 是 AI 輔助工具的延伸）

## 來源
- [[Summaries/OpenClaude-RPA]]
