---
title: "Obsidian + Claude Code 第二大腦完整建置：90 分鐘 7 步驟 SOP｜恆遠數位科技"
source: "https://foreverwebs.com/blog/obsidian-claude-code-second-brain-workflow-7-steps-sop?srsltid=AfmBOorv9AuyG7BBhDh602UJcq1giCzvSoOMqpJPjcBt9KmPL7e3p81G"
author:
  - "[[恆遠數位編輯團隊]]"
published: 2026-05-15
created: 2026-09-05
description: "上班族不靠 Notion 也能跑出 AI 筆記工作流。完整 7 步驟 SOP：安裝、PARA 結構、daily 模板、Claude Code 整合、5 個高頻 prompt、30 分鐘晨間儀式，附 Obsidian vs Notion 完整比較與遷移建議。"
tags:
  - "clippings"
---
你的 Notion 工作區一週至少有 1 次「我記得寫過、但找不到」的時刻嗎？

如果答案是肯定，問題不在 Notion，是在「雲端 + 結構化資料庫」這套架構本身——當你的筆記累積到 500 頁以上，雲端搜尋速度、AI 上下文理解、跨頁面關聯能力都會開始出現瓶頸。2026 年上半年矽谷 AI 圈悄悄把「第二大腦」工具切回 Obsidian，背後真正的原因是技術路線變了，而非復古情懷。

Andrej Karpathy 在 2026 年 2 月一則 [關於 LLM Wiki 的 X 貼文](https://x.com/karpathy) 意外把 Obsidian 推上熱搜——他展示了用 Markdown + LLM 維運個人知識庫的完整工作流，讓 AI 直接讀 vault 裡所有筆記、跨文件做關聯推理。Obsidian 同期推出 Bases（內建資料庫功能）與官方 CLI 工具，徹底打開「LLM agent + 本地 vault」的整合空間。 [RAR Design 整理的這份分析](https://rar.design/posts/obsidian-ai-second-brain-2026) 把 Obsidian 在 2026 年突然爆紅的原因講得很清楚：真正的轉折是 AI agent 終於追上來了，工具本身並未改變。

這篇給你完整的 7 步驟建置 SOP——從零安裝 Obsidian，到讓 Claude Code 直接讀寫你的 vault，到每天早上 30 分鐘跑出今日工作清單。重點是上班族能直接照做的工作流，做完那天起 Notion 焦慮會消失，工具介紹反而是其次。

![Obsidian + Claude Code 第二大腦建置](https://r2.foreverwebs.com/media/obsidian-second-brain-cover.jpg)

Obsidian + Claude Code 第二大腦建置

## Obsidian vs Notion：什麼時候該切過去

先說重點：Notion 沒有變差，Obsidian 也不是萬靈丹。兩者解決的問題不同。先用一張表把差異攤平。

| 維度 | Notion | Obsidian | 差異會影響什麼 |
| --- | --- | --- | --- |
| 資料儲存 | 雲端、JSON 結構 | 本地 Markdown 檔案 | AI 能不能直接讀取你的內容 |
| 搜尋速度 | API 查詢，500 頁後變慢 | 本地檔案系統，1 萬頁仍秒搜 | 筆記累積後的日常使用體驗 |
| AI 整合 | Notion AI（付費月費） | Claude Code / Cursor 免費直連 | AI 工作流的彈性與成本 |
| 離線使用 | 有快取但功能受限 | 100% 離線可用 | 出差、飛機上、咖啡廳無 WiFi |
| 協作能力 | 原生即時協作 | 透過 Git / iCloud 同步 | 個人 vs 團隊使用情境 |
| 學習曲線 | 拖拉式、上手快 | Markdown + 雙向連結，需 1-2 週適應 | 會不會半路放棄 |
| 月費 | Plus $10/月、AI $10/月 | 免費，可選 Sync $4/月 | 一年攤下來的開銷 |

看完表格判斷標準很清楚——筆記累積在 200 頁以內、需要團隊即時協作、希望少花時間設定的人，Notion 仍是最佳選擇；筆記累積 500 頁以上、想跟 AI agent 深度整合、希望資料 100% 自己掌握的人，Obsidian 開始展現價值。

ℹ️ **兩個都不是終點**

Notion 跟 Obsidian 不是「擇一」題目，可以共存——Notion 拿來跟團隊協作、Obsidian 拿來當個人第二大腦。我們服務的上班族客戶有 35% 採用這種雙軌制。重點是「你的核心知識資產存在哪裡」的決定，工具反而是其次。

## Obsidian + Claude Code 第二大腦完整架構

動手前先把架構畫清楚，知道每個元件做什麼事，後面 7 步驟才不會迷路。

<svg id="mermaid_R_1kmatbsnpftivb_" width="100%" xmlns="http://www.w3.org/2000/svg" style="max-width: 786.6015625px;" viewBox="0 0 786.6015625 494" role="graphics-document document" aria-roledescription="flowchart-v2"><g><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-pointEnd" viewBox="0 0 10 10" refX="5" refY="5" markerUnits="userSpaceOnUse" markerWidth="8" markerHeight="8" orient="auto"><path d="M 0 0 L 10 5 L 0 10 z" style="stroke-width: 1; stroke-dasharray: 1, 0;"></path></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-pointStart" viewBox="0 0 10 10" refX="4.5" refY="5" markerUnits="userSpaceOnUse" markerWidth="8" markerHeight="8" orient="auto"><path d="M 0 5 L 10 10 L 10 0 z" style="stroke-width: 1; stroke-dasharray: 1, 0;"></path></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-pointEnd-margin" viewBox="0 0 11.5 14" refX="11.5" refY="7" markerUnits="userSpaceOnUse" markerWidth="10.5" markerHeight="14" orient="auto"><path d="M 0 0 L 11.5 7 L 0 14 z" style="stroke-width: 0; stroke-dasharray: 1, 0;"></path></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-pointStart-margin" viewBox="0 0 11.5 14" refX="1" refY="7" markerUnits="userSpaceOnUse" markerWidth="11.5" markerHeight="14" orient="auto"><polygon points="0,7 11.5,14 11.5,0" style="stroke-width: 0; stroke-dasharray: 1, 0;"></polygon></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-circleEnd" viewBox="0 0 10 10" refX="11" refY="5" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><circle cx="5" cy="5" r="5" style="stroke-width: 1; stroke-dasharray: 1, 0;"></circle></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-circleStart" viewBox="0 0 10 10" refX="-1" refY="5" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><circle cx="5" cy="5" r="5" style="stroke-width: 1; stroke-dasharray: 1, 0;"></circle></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-circleEnd-margin" viewBox="0 0 10 10" refY="5" refX="12.25" markerUnits="userSpaceOnUse" markerWidth="14" markerHeight="14" orient="auto"><circle cx="5" cy="5" r="5" style="stroke-width: 0; stroke-dasharray: 1, 0;"></circle></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-circleStart-margin" viewBox="0 0 10 10" refX="-2" refY="5" markerUnits="userSpaceOnUse" markerWidth="14" markerHeight="14" orient="auto"><circle cx="5" cy="5" r="5" style="stroke-width: 0; stroke-dasharray: 1, 0;"></circle></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-crossEnd" viewBox="0 0 11 11" refX="12" refY="5.2" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><path d="M 1,1 l 9,9 M 10,1 l -9,9" style="stroke-width: 2; stroke-dasharray: 1, 0;"></path></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-crossStart" viewBox="0 0 11 11" refX="-1" refY="5.2" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><path d="M 1,1 l 9,9 M 10,1 l -9,9" style="stroke-width: 2; stroke-dasharray: 1, 0;"></path></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-crossEnd-margin" viewBox="0 0 15 15" refX="17.7" refY="7.5" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto"><path d="M 1,1 L 14,14 M 1,14 L 14,1" style="stroke-width: 2.5;"></path></marker><marker id="mermaid_R_1kmatbsnpftivb__flowchart-v2-crossStart-margin" viewBox="0 0 15 15" refX="-3.5" refY="7.5" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto"><path d="M 1,1 L 14,14 M 1,14 L 14,1" style="stroke-width: 2.5; stroke-dasharray: 1, 0;"></path></marker><g><g></g><g><path d="M538.211,72L538.211,78.167C538.211,84.333,538.211,96.667,505.139,110.53C472.067,124.393,405.923,139.787,372.851,147.483L339.779,155.18" id="mermaid_R_1kmatbsnpftivb_-L_A_B_0" style=";" data-edge="true" data-et="edge" data-id="L_A_B_0" data-points="W3sieCI6NTM4LjIxMDkzNzUsInkiOjcyfSx7IngiOjUzOC4yMTA5Mzc1LCJ5IjoxMDl9LHsieCI6MzM1Ljg4MjgxMjUsInkiOjE1Ni4wODY2NTIzNDk3NzE0fV0=" data-look="classic" marker-end="url(#mermaid_R_1kmatbsnpftivb__flowchart-v2-pointEnd)" fill="none" stroke="currentColor"></path><path d="M265.724,210L270.349,216.167C274.974,222.333,284.224,234.667,312.713,247.84C341.202,261.012,388.93,275.025,412.794,282.031L436.658,289.037" id="mermaid_R_1kmatbsnpftivb_-L_B_C_0" style=";" data-edge="true" data-et="edge" data-id="L_B_C_0" data-points="W3sieCI6MjY1LjcyMzU2MjA0NzEwMTQ0LCJ5IjoyMTB9LHsieCI6MjkzLjQ3NDYwOTM3NSwieSI6MjQ3fSx7IngiOjQ0MC40OTYwOTM3NSwieSI6MjkwLjE2NDA2NDEyMzEyNzA1fV0=" data-look="classic" marker-end="url(#mermaid_R_1kmatbsnpftivb__flowchart-v2-pointEnd)" fill="none" stroke="currentColor"></path><path d="M528.496,348L528.496,354.167C528.496,360.333,528.496,372.667,519.552,384.637C510.607,396.608,492.718,408.215,483.773,414.019L474.829,419.823" id="mermaid_R_1kmatbsnpftivb_-L_C_D_0" style=";" data-edge="true" data-et="edge" data-id="L_C_D_0" data-points="W3sieCI6NTI4LjQ5NjA5Mzc1LCJ5IjozNDh9LHsieCI6NTI4LjQ5NjA5Mzc1LCJ5IjozODV9LHsieCI6NDcxLjQ3MzI3ODk4NTUwNzI1LCJ5Ijo0MjJ9XQ==" data-look="classic" marker-end="url(#mermaid_R_1kmatbsnpftivb__flowchart-v2-pointEnd)" fill="none" stroke="currentColor"></path><path d="M334.902,430.629L306.51,423.024C278.117,415.42,221.332,400.21,192.939,381.105C164.547,362,164.547,339,164.547,316C164.547,293,164.547,270,170.947,252.778C177.348,235.555,190.148,224.111,196.549,218.388L202.949,212.666" id="mermaid_R_1kmatbsnpftivb_-L_D_B_0" style=";" data-edge="true" data-et="edge" data-id="L_D_B_0" data-points="W3sieCI6MzM0LjkwMjM0Mzc1LCJ5Ijo0MzAuNjI5MjY4NTE0NTg3MjR9LHsieCI6MTY0LjU0Njg3NSwieSI6Mzg1fSx7IngiOjE2NC41NDY4NzUsInkiOjMxNn0seyJ4IjoxNjQuNTQ2ODc1LCJ5IjoyNDd9LHsieCI6MjA1LjkzMDk4OTU4MzMzMzM0LCJ5IjoyMTB9XQ==" data-look="classic" marker-end="url(#mermaid_R_1kmatbsnpftivb__flowchart-v2-pointEnd)" fill="none" stroke="currentColor"></path><path d="M315.816,72L315.816,78.167C315.816,84.333,315.816,96.667,309.682,108.546C303.548,120.425,291.28,131.849,285.146,137.562L279.012,143.274" id="mermaid_R_1kmatbsnpftivb_-L_E_B_0" style=";" data-edge="true" data-et="edge" data-id="L_E_B_0" data-points="W3sieCI6MzE1LjgxNjQwNjI1LCJ5Ijo3Mn0seyJ4IjozMTUuODE2NDA2MjUsInkiOjEwOX0seyJ4IjoyNzYuMDg0OTc1MDkwNTc5NywieSI6MTQ2fV0=" data-look="classic" marker-end="url(#mermaid_R_1kmatbsnpftivb__flowchart-v2-pointEnd)" fill="none" stroke="currentColor"></path><path d="M86.711,72L86.711,78.167C86.711,84.333,86.711,96.667,99.956,108.729C113.2,120.791,139.69,132.582,152.934,138.478L166.179,144.373" id="mermaid_R_1kmatbsnpftivb_-L_F_B_0" style=";" data-edge="true" data-et="edge" data-id="L_F_B_0" data-points="W3sieCI6ODYuNzEwOTM3NSwieSI6NzJ9LHsieCI6ODYuNzEwOTM3NSwieSI6MTA5fSx7IngiOjE2OS44MzMxNjM0OTYzNzY4LCJ5IjoxNDZ9XQ==" data-look="classic" marker-end="url(#mermaid_R_1kmatbsnpftivb__flowchart-v2-pointEnd)" fill="none" stroke="currentColor"></path><path d="M454.402,210L454.402,216.167C454.402,222.333,454.402,234.667,460.536,246.546C466.67,258.425,478.938,269.849,485.072,275.562L491.207,281.274" id="mermaid_R_1kmatbsnpftivb_-L_G_C_0" style=";" data-edge="true" data-et="edge" data-id="L_G_C_0" data-points="W3sieCI6NDU0LjQwMjM0Mzc1LCJ5IjoyMTB9LHsieCI6NDU0LjQwMjM0Mzc1LCJ5IjoyNDd9LHsieCI6NDk0LjEzMzc3NDkwOTQyMDMsInkiOjI4NH1d" data-look="classic" marker-end="url(#mermaid_R_1kmatbsnpftivb__flowchart-v2-pointEnd)" fill="none" stroke="currentColor"></path><path d="M675.762,210L675.762,216.167C675.762,222.333,675.762,234.667,663.204,246.717C650.646,258.768,625.531,270.535,612.973,276.419L600.415,282.303" id="mermaid_R_1kmatbsnpftivb_-L_H_C_0" style=";" data-edge="true" data-et="edge" data-id="L_H_C_0" data-points="W3sieCI6Njc1Ljc2MTcxODc1LCJ5IjoyMTB9LHsieCI6Njc1Ljc2MTcxODc1LCJ5IjoyNDd9LHsieCI6NTk2Ljc5MzE5NTE5OTI3NTQsInkiOjI4NH1d" data-look="classic" marker-end="url(#mermaid_R_1kmatbsnpftivb__flowchart-v2-pointEnd)" fill="none" stroke="currentColor"></path></g><g><g transform="translate(538.2109375, 109)"><g data-id="L_A_B_0" transform="translate(-32, -12)"><foreignObject width="64" height="24"><p>每日記錄</p></foreignObject></g></g><g transform="translate(344.79656, 262.06762)"><g data-id="L_B_C_0" transform="translate(-57.17578125, -12)"><foreignObject width="114.3515625" height="24"><p>Markdown 檔案</p></foreignObject></g></g><g transform="translate(528.49609375, 385)"><g data-id="L_C_D_0" transform="translate(-32, -12)"><foreignObject width="64" height="24"><p>讀寫權限</p></foreignObject></g></g><g transform="translate(164.546875, 316)"><g data-id="L_D_B_0" transform="translate(-61.421875, -12)"><foreignObject width="122.84375" height="24"><p>分析 / 摘要 / 連結</p></foreignObject></g></g><g transform="translate(315.81640625, 109)"><g data-id="L_E_B_0" transform="translate(-45.21484375, -12)"><foreignObject width="90.4296875" height="24"><p>Web Clipper</p></foreignObject></g></g><g transform="translate(86.7109375, 109)"><g data-id="L_F_B_0" transform="translate(-40, -12)"><foreignObject width="80" height="24"><p>錄音轉文字</p></foreignObject></g></g><g transform="translate(454.40234375, 247)"><g data-id="L_G_C_0" transform="translate(-32, -12)"><foreignObject width="64" height="24"><p>版本控制</p></foreignObject></g></g><g transform="translate(675.76171875, 247)"><g data-id="L_H_C_0" transform="translate(-40, -12)"><foreignObject width="80" height="24"><p>跨裝置同步</p></foreignObject></g></g></g><g><g id="mermaid_R_1kmatbsnpftivb_-flowchart-A-0" data-look="classic" transform="translate(538.2109375, 40)"><rect style="" x="-72" y="-32" width="144" height="64" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-32, -12)"><rect></rect><foreignObject width="64" height="24"><p>你的大腦</p></foreignObject></g></g><g id="mermaid_R_1kmatbsnpftivb_-flowchart-B-1" data-look="classic" transform="translate(241.72265625, 178)"><rect style="" x="-94.16015625" y="-32" width="188.3203125" height="64" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-54.16015625, -12)"><rect></rect><foreignObject width="108.3203125" height="24"><p>Obsidian Vault</p></foreignObject></g></g><g id="mermaid_R_1kmatbsnpftivb_-flowchart-C-3" data-look="classic" transform="translate(528.49609375, 316)"><rect style="" x="-88" y="-32" width="176" height="64" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-48, -12)"><rect></rect><foreignObject width="96" height="24"><p>本地檔案系統</p></foreignObject></g></g><g id="mermaid_R_1kmatbsnpftivb_-flowchart-D-5" data-look="classic" transform="translate(422.15625, 454)"><rect style="" x="-87.25390625" y="-32" width="174.5078125" height="64" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-47.25390625, -12)"><rect></rect><foreignObject width="94.5078125" height="24"><p>Claude Code</p></foreignObject></g></g><g id="mermaid_R_1kmatbsnpftivb_-flowchart-E-8" data-look="classic" transform="translate(315.81640625, 40)"><rect style="" x="-100.39453125" y="-32" width="200.7890625" height="64" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-60.39453125, -12)"><rect></rect><foreignObject width="120.7890625" height="24"><p>網頁 / PDF / 影片</p></foreignObject></g></g><g id="mermaid_R_1kmatbsnpftivb_-flowchart-F-10" data-look="classic" transform="translate(86.7109375, 40)"><rect style="" x="-78.7109375" y="-32" width="157.421875" height="64" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-38.7109375, -12)"><rect></rect><foreignObject width="77.421875" height="24"><p>會議 / 對話</p></foreignObject></g></g><g id="mermaid_R_1kmatbsnpftivb_-flowchart-G-12" data-look="classic" transform="translate(454.40234375, 178)"><rect style="" x="-68.51953125" y="-32" width="137.0390625" height="64" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-28.51953125, -12)"><rect></rect><foreignObject width="57.0390625" height="24"><p>Git 倉庫</p></foreignObject></g></g><g id="mermaid_R_1kmatbsnpftivb_-flowchart-H-14" data-look="classic" transform="translate(675.76171875, 178)"><rect style="" x="-102.83984375" y="-32" width="205.6796875" height="64" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-62.83984375, -12)"><rect></rect><foreignObject width="125.6796875" height="24"><p>iCloud / Dropbox</p></foreignObject></g></g></g></g></g><defs></defs><defs></defs><linearGradient id="mermaid_R_1kmatbsnpftivb_-gradient" gradientUnits="objectBoundingBox" x1="0%" y1="0%" x2="100%" y2="0%"><stop offset="0%" stop-color="#E5A600" stop-opacity="1"></stop><stop offset="100%" stop-color="#D1D9E6" stop-opacity="1"></stop></linearGradient></svg>

Obsidian + Claude Code 第二大腦資料流

架構的核心邏輯——Obsidian 是「儲存層」、Markdown 檔案是「資料層」、Claude Code 是「智能層」。三層解耦，任一層都可以替換不影響其他兩層。這跟你在 Notion 上整套被綁住完全不一樣。如果哪天 Anthropic 倒了或 Claude Code 收費翻倍，你的 Markdown 檔案還在、可以換 Cursor、換 Cody、換任何 AI agent 接上去。

這個 unbundle 邏輯跟我們在 [Claude Code 完整教學](https://foreverwebs.com/blog/claude-skills-complete-guide-pillar-2026) 提到的「站在 AI 巨人肩膀上」是同一個哲學——工具是巨人、檔案是你的。檔案在你手上，巨人換誰都行。

## 7 步驟完整建置 SOP

這份 SOP 設計給「Markdown 完全沒碰過、Claude Code 也沒用過」的上班族。每一步都有實際指令、可直接照做。預計總時間 90 分鐘。

### Step 1：安裝 Obsidian 與建立 Vault（10 分鐘）

下載 Obsidian 安裝包： [obsidian.md](https://obsidian.md/) 選對應作業系統版本。安裝後第一次打開會問「Open folder as vault」——選一個你希望放筆記的位置。建議放在 iCloud Drive 或 Dropbox 同步目錄裡，這樣 Mac、iPhone、其他電腦能同步。

Vault 命名規則建議：用 **my-brain** 或 **notes** 這種 evergreen 的名字，避免用「2026-knowledge」這種會過期的名稱。

### Step 2：建立基礎資料夾結構（5 分鐘）

在 vault 裡建立 5 個資料夾，這是 PARA + Daily 混合架構，適合上班族：

```
my-brain/
├── 0-inbox/        # 速記、未整理
├── 1-projects/     # 進行中的專案
├── 2-areas/        # 持續關注的領域
├── 3-resources/    # 參考資料
├── 4-archive/      # 已完成或不再活躍
└── daily/          # 每日筆記
```

PARA 是 Tiago Forte 提出的方法論——Projects / Areas / Resources / Archive。資料夾名前面加數字是為了強制排序順序（0、1、2...），打開 vault 第一眼就看到 inbox 最上面，符合「先處理未整理」的習慣。Daily 獨立一個資料夾，每天的筆記用 **YYYY-MM-DD.md** 命名。

### Step 3：開啟核心 plugin（10 分鐘）

Obsidian 開箱即用功能有限，需要開幾個內建 plugin 才能跑起來。Settings → Core plugins，開啟：

- Daily notes（每日筆記）—— 讓 Cmd+O 可以直接跳到今天
- Templates（模板）—— 每日筆記、會議紀錄、想法卡片用同一套格式
- Backlinks（反向連結）—— 看到「哪些筆記引用了這篇」
- Outline（大綱）—— 長筆記快速跳轉
- Tag pane（標籤面板）—— 用 #tag 分類
- Bases（資料庫，2026 新功能）—— 把 Markdown 變成可篩選表格

Community plugin（第三方）等熟練後再加，先不要裝。新手最常見的卡點是「裝太多 plugin 反而搞混」。先用 6 個核心 plugin 跑兩週，缺什麼再補。

### Step 4：設計每日筆記模板（15 分鐘）

在 vault 裡建立 **templates/daily-template.md** ，內容如下：

```
---
date: {{date:YYYY-MM-DD}}
tags: [daily]
---

# {{date:YYYY-MM-DD ddd}}

## 三件事

- [ ] 最重要的一件事：
- [ ] 第二件：
- [ ] 第三件：

## 行事曆與會議

- 

## 學到的事

- 

## 想法閃光

- 

## 明天的我看了會感謝的記錄

-
```

Settings → Templates → Template folder location 設成 **templates** 。設定好之後在 Daily notes plugin 設定裡選這個模板，往後每天 Cmd+O 跳到今天會自動套用。

為什麼這 5 個區塊？「三件事」強制聚焦、「會議」串行事曆、「學到的事」是長期複利、「想法閃光」捕捉靈光、「明天的我」是把今天的 context 傳給明天的自己——這是個人知識管理裡最被低估的一個區塊。實際跑兩週你會發現第五區塊救了你無數次。

![上班族 AI 筆記工作流](https://r2.foreverwebs.com/media/obsidian-second-brain-body1.jpg)

上班族 AI 筆記工作流

### Step 5：裝 Claude Code 並指向 vault（20 分鐘）

這一步是這套架構的核心。Claude Code 是 Anthropic 推出的 CLI 工具，能直接讀寫你 vault 裡所有 Markdown 檔案。安裝指令：

```
npm install -g @anthropic-ai/claude-code
```

裝完之後 cd 進 vault 資料夾、執行 **claude** ，第一次會引導你登入 Anthropic 帳號。完成後你就站在 vault 入口、Claude Code 可以讀取所有檔案。

建立一份 **CLAUDE.md** 在 vault 根目錄，告訴 Claude Code 怎麼處理你的筆記：

```
# 我的個人知識庫

## 規則
- 所有筆記都是 Markdown 格式，用 [[wiki-link]] 雙向連結
- 寫筆記時繁體中文為主，技術術語保留英文
- daily/ 下面是每日筆記，1-projects/ 是進行中專案

## 我希望你幫我做的事
- 每週五整理本週 daily/ 的「學到的事」，產出週報草稿
- 把零散想法歸類到對應的 1-projects/ 或 2-areas/
- 識別重複出現的主題、提醒我這可能值得獨立成一篇

## 我不希望你做的事
- 不要直接編輯我的 daily/ 檔案、只能讀
- 不要把英文術語翻成中文（例：保留 LLM、Agent、Prompt）
```

這份 CLAUDE.md 等於跟 AI 簽了一份合約。每次跑 Claude Code 它都會讀這個檔案、按你的規則辦事。寫得越具體 AI 行為越穩定。 [Claude Code env 保護完整實戰](https://foreverwebs.com/blog/claude-code-env-protection-permissions-hooks) 裡有更深入的權限設定，做完基礎再進階。

### Step 6：建立 5 個高頻 prompt（20 分鐘）

Claude Code 真正的價值在於「把常用工作流寫成可重複呼叫的 prompt」，比起「問一次答一次」更具影響力。建議從這 5 個開始：

- **週報自動產出** ：「讀 daily/ 下面這週 5 天的筆記，把「學到的事」與「想法閃光」彙整成週報，分技術、業務、人際 3 個類別，輸出 Markdown 格式」
- **跨筆記主題識別** ：「掃描所有 daily/，找出過去一個月重複出現 3 次以上的主題或關鍵字，列出來並標記出現過的日期」
- **想法歸類** ：「讀 0-inbox/ 下所有未整理筆記，建議每一篇應該歸到 1-projects/ / 2-areas/ / 3-resources/ 的哪個資料夾，不要直接搬」
- **會議準備** ：「我下午要跟 \[客戶名\] 開會，幫我從 2-areas/ 下找到所有跟這個客戶相關的筆記，整理成 3 個重點與 5 個可能提的問題」
- **找連結機會** ：「讀我最新一篇 daily/，識別 3 個可以加 \[\[wiki-link\]\] 連到既有筆記的位置，告訴我連到哪一篇、為什麼」

把這 5 個 prompt 存成 Claude Skill 或 alias，每天 30 秒就能呼叫。實際操作起來會跟 Notion AI 完全不同感受——Notion AI 是「在 Notion 介面裡按一個按鈕」，Claude Code 是「在你的 vault 旁邊跑著一個全知全能的助理」，沒有介面限制、prompt 可無限延伸。

⚠️ **權限設定別跳過**

Claude Code 預設能讀寫整個資料夾。處理機密筆記前，務必看一遍 [Claude Code.env 保護](https://foreverwebs.com/blog/claude-code-env-protection-permissions-hooks) ——設置 deny rules、用 permissions hook 保護 sensitive files。重點是任何 AI agent 直接接你硬碟都要有防護網，並非 Anthropic 本身不安全。

### Step 7：建立每天 30 分鐘的儀式（10 分鐘設定 + 長期執行）

最後一步是儀式化。工具裝完不執行儀式都會吃灰。設計一份「每天早上 30 分鐘」的固定流程：

| 時間 | 動作 | 用什麼工具 |
| --- | --- | --- |
| 0-5 分鐘 | Cmd+O 跳到今天的 daily note、套模板 | Obsidian Daily notes |
| 5-10 分鐘 | 從昨天的「明天的我會感謝」開始今天的三件事 | Obsidian 本人 |
| 10-15 分鐘 | 呼叫 Claude Code 跑「會議準備」prompt | Claude Code |
| 15-25 分鐘 | 處理 0-inbox/ 裡昨天速記的內容、歸類或刪除 | Obsidian + Claude Code |
| 25-30 分鐘 | 快速瀏覽 Claude Code 給的「主題識別」結果 | Claude Code |

這個 30 分鐘設計的關鍵是——把「整理筆記」變成「啟動工作的儀式」，而不是另一個任務。第一週可能會覺得花時間，第二週開始你會發現開會前已經有 prep、寫週報前已經有素材、新主題還在萌芽期就被識別出來。Karpathy 在他的工作流分享裡形容這叫「第二大腦的複利效應」——前 30 天投資、後 11 個月坐享其成。

![本地優先 AI 筆記架構](https://r2.foreverwebs.com/media/obsidian-second-brain-body2.jpg)

本地優先 AI 筆記架構

## 從 Notion 遷移過來的 3 個現實提醒

如果你已經用 Notion 累積大量筆記，遷移過來有 3 個現實提醒。

### 提醒 1：不要全部遷過來

最常見的錯誤——把 Notion 1500 頁筆記匯出成 Markdown 全塞進 Obsidian。結果是 vault 變垃圾場、新工作流跑不起來。正確做法是「只搬最近 6 個月、高頻使用的筆記」，剩下的留在 Notion 當 archive，每次需要時臨時抓。實際統計顯示，多數人手上的筆記 80% 過去半年沒打開過——這些不值得搬。

### 提醒 2：Database 沒有 1-to-1 對應

Notion 的 Database 是雲端結構化資料，匯出成 Markdown 會變成一堆獨立檔案。Obsidian 2026 新功能 Bases 能補上這個落差——可以把 frontmatter 當欄位，把資料夾當資料庫。但邏輯不一樣，要重新設計 schema 而不是逐字搬。預留 4-6 小時專門處理這部分。

### 提醒 3：團隊協作 Notion 還是贏

Obsidian 主打「個人第二大腦」，團隊即時協作不是它強項。如果你筆記裡有 30%+ 是團隊協作型內容，繼續留在 Notion。Obsidian 給「個人知識資產 + AI agent 整合」這條路線，跟 Notion 不衝突。

## 3 個進階工作流，跑通第二大腦真正威力

基礎 7 步驟跑完 4 週後，可以試這 3 個進階工作流，這是 Obsidian + Claude Code 比 Notion AI 強的核心場域。

### 進階 1：跨筆記主題演化追蹤

讓 Claude Code 定期掃描你的 vault、找出「跨越多篇筆記的主題演化」。例如過去 3 個月你在 12 篇 daily 提到「客戶 A 的決策節奏」，Claude Code 可以把這 12 個 fragment 拼出客戶 A 的決策模式時間軸。這是任何 Notion AI 做不到的——因為 Notion 沒有 native 跨頁面 context 理解能力，但 Markdown vault 對 Claude Code 是天生的 context window。

### 進階 2：個人 RAG 系統

把你的 vault 當作個人 RAG（Retrieval-Augmented Generation）系統。Claude Code 跑前先讀過 vault 全部內容、再回答你的問題。實際上跟 [企業 AI RAG 架構入門](https://foreverwebs.com/blog/enterprise-ai-rag-architecture-knowledge-base-guide) 講的企業級 RAG 是同一套技術，只是 scale 小了——個人版的 RAG 不需要向量資料庫，直接讓 LLM 全 vault 讀取就好（前提是 vault 不超過 50 萬字）。

### 進階 3：知識資產複利化

每季跑一次 Claude Code 的「主題識別 + 內容延伸」——把過去 90 天 daily 裡反覆出現的 5 個主題抓出來，每個主題寫成一篇 1500 字的 deep dive。一年下來你會累積 20 篇個人 thought leadership 文章，可以發部落格、可以彙整成內訓教材、可以變成提案說服力的彈藥。第二大腦最有價值的能力是「複利化」，比「儲存」更具影響力。

## 對照組：Notion AI 能做、Obsidian + Claude Code 做不到的事

不要全押 Obsidian。誠實列出 Notion AI 還是贏的場域：

| 能力 | Notion AI 表現 | Obsidian + Claude Code 表現 | 推薦 |
| --- | --- | --- | --- |
| 即時同步團隊筆記 | ✅ 原生強 | ❌ 要 Git workflow | Notion |
| 會議錄音轉文字 | ✅ Notion AI 內建 | ⚠️ 要接 Whisper API | Notion |
| 跨頁面 AI 摘要 | ⚠️ 受限 | ✅ Claude Code 全 vault 讀取 | Obsidian |
| 離線使用 | ⚠️ 部分功能 | ✅ 100% 離線 | Obsidian |
| 資料 ownership | ⚠️ Notion 雲端 | ✅ 本地 Markdown | Obsidian |
| 學習曲線 | ✅ 上手快 | ⚠️ 1-2 週適應 | Notion |
| 月費 | $20/月 (Plus + AI) | $0-4/月 | Obsidian |
| AI 工作流彈性 | ⚠️ Notion 介面限制 | ✅ CLI 無限延伸 | Obsidian |

實話——如果你筆記主要是團隊協作、會議紀錄、不想學新工具，Notion 還是最佳解。Obsidian 適合的是「想擁有資料、想跟 AI agent 深度整合、願意投資 90 分鐘設定」的上班族。

QObsidian 真的免費嗎？商業用途也可以？

個人使用 100% 免費。商業使用要買 Commercial license（$50/年/座），這比 Notion Team $10/月 還便宜。Sync（跨裝置同步）$4/月也是可選，用 iCloud / Dropbox 替代完全免費。

QClaude Code 不便宜，加上去整套月費會不會比 Notion AI 還貴？

看你用量。Claude Code 採用 usage-based 計費（按 token），輕量使用約 $5-10/月，中量約 $20-30/月。Notion Plus + AI 固定 $20/月。中重度用戶 Claude Code 會比 Notion AI 貴，但能做的事多 5-10 倍。對「想用 AI 大幅提升效率」的上班族，這個價差完全划算。

Q我用 iPhone 多、想在手機上也能跑這套，可以嗎？

Obsidian Mobile（iOS/Android）原生支援，搭配 iCloud / Dropbox 同步沒問題。Claude Code 沒手機版，但 Claude 手機 app 可以連到你的 vault（透過第三方整合）或單純當問答用。實際上多數第二大腦的「深度處理」會在電腦上做、手機只負責速記與查詢。

Q從 Notion 完整匯出 Markdown 應該怎麼做？

Notion 內建的 Export 功能選 Markdown & CSV、Include subpages 勾起來。匯出的.zip 解開後是一堆 Markdown 檔案。但別直接全搬進 Obsidian——先看清楚有沒有 Database 相關內容（會變一堆獨立檔案）、有沒有附件圖片（路徑需要重新整理）。建議先試搬 1 個 workspace 確認流程順、再搬其他的。

QObsidian 加密怎麼處理？vault 放本地會不會被偷？

純本地 vault 跟你其他檔案一樣依賴系統加密（macOS FileVault、Windows BitLocker）。如果處理機密內容，建議 vault 放 encrypted disk image 或 VeraCrypt 容器。雲端同步（iCloud/Dropbox）會經過廠商伺服器，敏感資料記得加密後同步。

Q我已經用了 Notion 三年、累積 2000+ 頁，現在切過去不會崩潰嗎？

會崩潰，如果一次全搬。建議走「雙軌制」——Obsidian 拿來建新筆記、Notion 保留歷史 archive。3 個月後新筆記累積到一定量、舊筆記絕大部分不再打開，這時候才考慮要不要全部遷移。多數人最後選擇 80% 留在 Obsidian、20% 高頻歷史筆記手動遷過來。

## 真正的勝利屬於你的工作流，而非工具本身

90 分鐘建好基礎、4 週跑完日常儀式、3 個月看到第二大腦複利——這套流程的勝利不在 Obsidian 也不在 Claude Code，是在「你真的開始把工作思考留下痕跡」這件事。多數上班族最大的問題是從來沒留下系統性的記錄，工具選擇反而是其次。

Notion 跟 Obsidian 都能解決這件事。差別在於 Obsidian + Claude Code 給你一個「未來 5 年都能複利」的個人資產，而 Notion 給你一個「方便但被綁住」的雲端服務。看你想要哪一個。

**想為你公司同事辦一場 Obsidian + Claude Code 工作坊？**

如果你看完想推給整個團隊，但又怕同事抗拒——可以 [預約一場 90 分鐘的 AI 工作流診斷](https://foreverwebs.com/services/ai-consult) ，我們幫你評估團隊適不適合切過去、設計分階段導入計畫、給可量化的 ROI 預估。一個人換工具是個人勝利，整個團隊換工具才是組織能力。

## 留言(0)

尚無留言，成為第一個留言的人吧！

專業服務

CONTENTS

- [1.Obsidian vs Notion：什麼時候該切過去](#obsidian-vs-notion什麼時候該切過去)
- [2.Obsidian + Claude Code 第二大腦完整架構](#obsidian-claude-code-第二大腦完整架構)
- [3.7 步驟完整建置 SOP](#7-步驟完整建置-sop)
- [3.1Step 1：安裝 Obsidian 與建立 Vault（10 分鐘）](#step-1安裝-obsidian-與建立-vault10-分鐘)
- [3.2Step 2：建立基礎資料夾結構（5 分鐘）](#step-2建立基礎資料夾結構5-分鐘)
- [3.3Step 3：開啟核心 plugin（10 分鐘）](#step-3開啟核心-plugin10-分鐘)
- [3.4Step 4：設計每日筆記模板（15 分鐘）](#step-4設計每日筆記模板15-分鐘)
- [3.5Step 5：裝 Claude Code 並指向 vault（20 分鐘）](#step-5裝-claude-code-並指向-vault20-分鐘)
- [3.6Step 6：建立 5 個高頻 prompt（20 分鐘）](#step-6建立-5-個高頻-prompt20-分鐘)
- [3.7Step 7：建立每天 30 分鐘的儀式（10 分鐘設定 + 長期執行）](#step-7建立每天-30-分鐘的儀式10-分鐘設定-長期執行)
- [4.從 Notion 遷移過來的 3 個現實提醒](#從-notion-遷移過來的-3-個現實提醒)
- [4.1提醒 1：不要全部遷過來](#提醒-1不要全部遷過來)
- [4.2提醒 2：Database 沒有 1-to-1 對應](#提醒-2database-沒有-1-to-1-對應)
- [4.3提醒 3：團隊協作 Notion 還是贏](#提醒-3團隊協作-notion-還是贏)
- [5.3 個進階工作流，跑通第二大腦真正威力](#3-個進階工作流跑通第二大腦真正威力)
- [5.1進階 1：跨筆記主題演化追蹤](#進階-1跨筆記主題演化追蹤)
- [5.2進階 2：個人 RAG 系統](#進階-2個人-rag-系統)
- [5.3進階 3：知識資產複利化](#進階-3知識資產複利化)
- [6.對照組：Notion AI 能做、Obsidian + Claude Code 做不到的事](#對照組notion-ai-能做obsidian-claude-code-做不到的事)
- [7.真正的勝利屬於你的工作流，而非工具本身](#真正的勝利屬於你的工作流而非工具本身)

SERVICES

## 想了解更多？看看我們的相關服務

### [AI 自動化顧問](https://foreverwebs.com/services/ai-consult)

N8N + ChatGPT 企業流程整合

了解更多

### [企業形象網站架設](https://foreverwebs.com/services/seo-web)

融合 SEO 的品牌官網設計

了解更多

### [SEO 優化代操](https://foreverwebs.com/services/seo)

讓專業團隊為你操刀自然排名

了解更多