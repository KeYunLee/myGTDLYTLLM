---
tags: [summary, ONNX, YOLO, docker, 模型部署]
created: 2026-09-04
source: "[[40-Raw/Web_Clippings/yolov7-to-onnx]]"
---

# YOLOv7 → ONNX 轉換流程

## 原始素材
- 來源：[[40-Raw/Web_Clippings/yolov7-to-onnx]]
- 類型：SOP 操作筆記 / HackMD 剪輯

## 核心摘要

YOLOv7 模型（`.pt`）轉換為 ONNX 格式的完整流程，分為「外網 Docker 測試」與「內網 DevOps Pipeline CI/CD」兩段。

## 關鍵技術要點

### 外網測試流程（Docker 手動）
1. `docker pull python:3.9` → 啟動容器 + 掛載權重資料夾
2. `git clone` WongKinYiu/yolov7、安裝 ONNX 相關套件：
   - `onnx>=1.9.0`、`onnx-simplifier>=0.3.6`、`onnx-graphsurgeon`
3. 執行轉換指令：
   ```bash
   python3 export.py --weights best.pt --grid --end2end --simplify \
     --topk-all 100 --iou-thres 0.65 --conf-thres 0.35 \
     --img-size 640 640 --max-wh 640
   ```
4. `docker cp` 將 `best.onnx` 複製回 Host
5. `docker commit` 記錄環境快照

### 內網 DevOps CI/CD 流程
- DevOps Git repo → Dockerfile 建立 → Pipeline 自動 Build Image
- Dockerfile 關鍵設定：`python:3.9` base，安裝 ffmpeg、YOLOv7 requirements + onnx-graphsurgeon
- Run pipeline 即可觸發自動 build，失敗直接看 DevOps 執行 log

### 重要轉換參數
| 參數 | 說明 |
|------|------|
| `--grid` | 啟用 grid 輸出（後處理直接在模型內）|
| `--end2end` | NMS 也包在模型內，輸出即為最終偵測結果 |
| `--simplify` | 使用 onnx-simplifier 優化計算圖 |
| `--topk-all` | 最多保留 N 個偵測結果 |

## 相關概念
- [[Concepts/ONNX]]
- [[Concepts/物件偵測]]
- [[Concepts/Docker]]
- [[Concepts/容器化部署]]
