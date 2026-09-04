---
tags: [concept, PHM, 工業AI, mlops]
created: 2026-09-05
updated: 2026-09-05
source_count: 2
---

# PHM（Predictive Health Monitoring）

## 核心定義

PHM（預測性健康監控/預測性設備維護）是一種利用感測器數據與機器學習模型，對設備的健康狀態進行持續監控並預測潛在故障的系統。目標是在設備實際故障發生之前提出警告，降低非計劃性停機損失。

## 運作原理

### 系統架構層次

```
感測器（1Hz 原始訊號）
  → GreenPlum（時序資料倉儲）
    → AP 應用程式（資料前處理 + 特徵工程）
      → MSSQL DB（特徵儲存）
        → 模型訓練（離線）/ 模型推論（線上）
          → 異常偵測 + 告警
```

### 資料管線關鍵參數

| 項目 | 數值 |
|------|------|
| 原始資料頻率 | 1 Hz |
| 7 天資料量 | ~604,800 筆 |
| AP 記憶體峰值 | ~30–50 MB（同時持有兩個 DataFrame） |
| Batch 策略 | 每 7 天為一 batch，30 天 = 4 批 |

### Imputation（資料補點）

設備感測器常有缺失值，使用 `ffill`（前向填充）策略補點：

```python
df = df.merge(...).sort_values('datetime').ffill()
del continuous_data  # 記憶體管理關鍵：補點後立即釋放原始 DataFrame
```

### 記憶體管理最佳實務

1. **分批處理**：避免一次載入全部時間範圍
2. **及時 del**：每個 DataFrame 使用完畢立即刪除
3. **bulk_save_objects**：一次批次 INSERT 後立即 commit，避免 MSSQL tempdb 暴增

### Feature Engineering Pipeline

1. 資料品質檢查 → 清洗
2. 特徵萃取：
   - 頻域分析（FFT / 頻率轉換）
   - 統計特徵（mean / std / skewness）
   - 時域特徵（峰值 / 均方根）
   - 頻譜分析
3. 特徵選擇 → 儲存至 Feature DB

### 推論架構（Real-time）

```
Sensor → OnlineAPI → FeatureService → ModelService
  → AnomalyDetection → AlertSystem → Dashboard / Email
```

## 優點與限制

**優點**：
- 提前發現設備異常，降低非計劃停機
- 可針對特定設備（tool/chamber/parameter）建立客製化模型

**限制**：
- 資料量大，對 DB 寫入壓力高（MSSQL tempdb 問題）
- 模型需定期 retrain（依 ModelTraining status 管理）
- 需人工維運（RabbitMQ + APScheduler 掛掉時需手動重啟）

## 維運重點

- **RabbitMQ**：消息佇列，監控 `local.user_upload` 數值變化
- **APScheduler**：排程器，每小時第 22 分重啟 online container
- **container ≠ 程式**：container running 不代表 APScheduler 正常

## 與相關概念的關係

- 相似：[[Concepts/Anomaly-Detection]]（核心推論方法）
- 使用：[[Concepts/容器化部署]]（Docker 微服務架構）
- 資料層：[[Concepts/Python基礎]]（DataFrame 處理）
- 上位：工業 AI / MLOps

## 來源

- [[13-Projects/PHM系統]]
