---
tags: [concept, action-recognition, openpose, 骨架追蹤, 深度學習]
created: 2026-09-05
updated: 2026-09-05
source_count: 1
---

# Action Recognition（動作識別）

## 核心定義

Action Recognition 是透過影像序列分析，識別影片中人物正在執行的動作類別。在照護場域中，用於自動辨識看護行為（翻身、拍背、餵食等），以提升照護品質監督效率。

## 運作原理

### 整體 Pipeline

```
影片（mp4）
  ↓
Pose Estimation（骨架估測）：Openpose → pose_json
  ↓
Multi Object Tracker：opjson2trackerDF → Tracker.csv（含 person_id + 骨架座標）
  ↓
Labeling（人工標注動作類別）→ tracker_label.csv
  ↓
資料前處理：preprocess → .pkl
  ↓
DDNet 訓練：train → my_model.h5
  ↓
推論：inference → test_result.csv
  ↓
視覺化：posedf2video → 輸出影片
```

### Pose Estimation（Openpose）

Openpose 是 CMU 開發的人體骨架估測模型，輸出每幀每人的 18/25 個關節點座標（JSON 格式）。

```bash
./build/examples/openpose/openpose.bin \
  --video input.mp4 --display 0 \
  --write_video output.mp4 \
  --write_json ./json/
```

### Multi Object Tracker

將 Openpose 每幀的骨架與跨幀的同一人進行關聯（tracking），產出每個 person_id 的完整時序骨架序列。

核心參數：
- `--maxDistance 100`：同一人前後幀最大距離
- `--filter 10`：過濾短暫出現的 person
- `--zeromax 9`：骨架零值容忍上限

### Labeling SOP

1. 開啟 Tracker.csv（含 person_id + frame + 骨架座標）
2. 對照 Tracker.mp4 查看每個 person_id 的實際動作
3. 新增 `label` 欄位，標記每個 frame 的動作類別
4. 依時序標記（起始到結束 frame 填入同一 label）

### DDNet 模型

DDNet（Double-feature Double-motion Network）是基於骨架序列的動作識別模型：
- 輸入：骨架時序 pkl
- 輸出：動作類別機率
- 訓練腳本：`python -m train --train_pkl ... --valid_pkl ...`

## 動作類別設計原則

- **避免重疊定義**：「換尿布」與「翻身」有高度相似動作，需縮小各自定義，以「彎腰手部行為」作為廣義 others 類別承接重疊部分
- **角色區分**：看護動作 vs 長者動作需分開處理
- **側身休息**：獨立標為 `restsideways`，作為翻身行為的前後狀態依據

## 優點與限制

**優點**：
- 非侵入式（攝影機），不需穿戴裝置
- 骨架特徵具隱私保護（不直接儲存影像）
- 可多人同時追蹤

**限制**：
- 需要高品質影像（遮擋、光線不足會影響骨架品質）
- Labeling 需人工作業，費時
- 動作邊界模糊（如「餵食」vs「摸臉」）需靠上下文判斷

## 與相關概念的關係

- 使用：[[Concepts/物件偵測]]（YOLOv5 偵測人體/尿布）
- 相似：[[Concepts/圖像分類]]（分類問題，但以時序骨架為輸入）
- 上位：電腦視覺 / Video Understanding
- 應用：[[13-Projects/頤康照護場域]]

## 來源

- [[13-Projects/頤康照護場域]]
