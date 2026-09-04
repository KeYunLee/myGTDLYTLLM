---
tags: [project, PHM, 維運]
created: 2026-09-05
updated: 2026-09-05
status: active
---

# PHM 系統專案

> 預測性設備維護（Predictive Health Monitoring）系統，含線上推論與離線訓練兩台主機。

---

## 主機資訊

| 主機 | IP | 用途 | 備註 |
|------|----|------|------|
| tcapcphmd01 | 10.96.18.68 | online 推論 | sudo 需找正坤申請 |
| tcapcphmd02 | 10.96.18.156 | offline 訓練 | sudo 需找正坤申請 |

---

## 系統架構概覽

### Data Flow 關鍵路徑

```
GreenPlum T3（原始感測資料）
  → AP 應用程式（DataFrame 處理，記憶體峰值 ~30–50 MB）
    → Imputation 補點（merge / sort / ffill）
      → MSSQL DB（bulk INSERT ~604,800 筆/batch）
```

**記憶體生命週期（每 7 天 batch）**：
- Step 0：DELETE 舊 ImputationData / SegInfo
- Step 1：SELECT 全量 DataFrame（~10 MB）
- Step 2：Imputation → 記憶體峰值 ~30–50 MB → del continuous_data
- Step 3：bulk_save_objects → commit → del imputation_data → 記憶體歸零
- 完成：UPDATE ModelTraining status → 1300

> ⚠️ MSSQL 注意：大量 INSERT 時 tempdb version store 暴增，transaction 需及時釋放。

### 各 Pipeline 說明

| Pipeline | 說明 |
|----------|------|
| Data Ingestion | FileSystem/Database → Validation → DataAdapter → Storage |
| Feature Engineering | 資料品質檢查 → 特徵萃取（頻域/統計/時域/頻譜） → Feature Storage |
| Model Training | 資料切分 → 演算法選擇 → 超參數調整 → 訓練 → 驗證 → 儲存 |
| Real-time Prediction | Sensor → OnlineAPI → FeatureService → ModelService → AnomalyDetection → AlertSystem |
| Data Export | 支援 Raw Data / Features / Models / Predictions 匯出 |

---

## 維運操作

### 監控頁面

- PHM 網頁：`http://aaapc005:888/` — NT 帳號登入
  - 整體健康度：`/online/onlineHome`
  - DB Last Time：查看各專案 Data Health
- tcapcphmd01 Queue：`http://tcapcphmd01:15672/#/queues`（guest/guest）
  - `local.user_upload` 有變化 → 正常；無變化 → 重啟 container
- Azure Queue：`http://10.248.7.85/#/queues`（phm/phmsa）
  - 積 queue → 重啟地端 raw data result-consumer

### 重啟 Container（tcapcphmd01）

```bash
sudo docker ps -a                # 確認 c2870822d1be、282211b41497 正常運行
sudo docker restart c2870822d1be # RabbitMQ container server
sudo docker restart 282211b41497 # RabbitMQ + APScheduler
```

> ⚠️ container 正常 ≠ 程式正常。APScheduler 掛掉時 queue 無資料，需重啟。

### 重啟全部微服務

```bash
sudo docker restart f9a80b6c28c9 bcb63b197d25 35541e8699ae 9ffa458e7548 f3c4f77d3524 \
  00564b9a30b9 2a4d918ada38 09fd6543fe4e 4fae38805152 74aef08c8238 6e76b40b91c0 \
  aded8acbbce3 e07ca358ba64 c11662d4d2aa a50bf0a4c4ee 62471b5f7e57 1011f2734928 \
  9ca59d6b1619 7671cec76e0f 26f1e9ac29b2 9bef66ea1926 ed887ae78005 f8d1d296b0a9 \
  ecde4a5ceb57 9a4f339133cf 7003f4b89e3e b27dbe2c5a88
```

### 排程說明

| 時間 | 動作 |
|------|------|
| 每小時第 20 分 | 高負載開始（持續 40 分鐘） |
| 每小時第 22 分 | crontab 自動重啟 online container（載入新訓練模型） |
| 每日 10:30 | tcapcphmd02 排程資料搬移 |

```bash
# 手動重啟 online container
cd /home/PHM/venv/venv3.7/bin
python /home/PHM/phm_offline/poc/restart_phm_docker_container.py online
```

### DB 空間管理

- 監控：`http://tcapcphmd01:7000/system_function/dbcheck`
- 安全閾值：**< 80%**，超過執行刪除或搬移

```bash
# 備份 online data
/home/PHM/venv/venv3.7/bin/python /home/PHM/poc/backup_online_data.py
```

```bash
# 網頁掛掉時重啟主程式
sudo docker restart 3c229a6bf4c2
```

### 硬碟空間管理（tcapcphmd01）

```bash
df -h

# 列出前 20 占空間的 container log
sudo du -a /var/lib/docker/containers | sort -n -r | head -n 20

# 刪除超過 1.3GB 的 log（已設 crontab）
sudo find /var/lib/docker/containers -type f -name "*.log" -size "+1500000000c" \
  -exec /usr/bin/truncate --size=0 {} \;

# 查看 overlay2 大型 temp（需手動）
sudo du -a /var/lib/docker/overlay2 | sort -n -r | head -n 30
```

---

## 待辦 / 開放問題

- [ ] auto_cluster 模組流程（待後續 Process Inbox）
- [ ] Azure queue 積壓時的 SOP 文件化
- [ ] MSSQL tempdb 暴增問題優化

---

## 相關知識

- [[Concepts/PHM]]
- [[Concepts/Anomaly-Detection]]
- [[Concepts/容器化部署]]
