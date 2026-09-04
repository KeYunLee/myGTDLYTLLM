---
tags: [project, vision-ai, fastapi, nodered, docker]
created: 2026-09-05
updated: 2026-09-05
status: active
---

# Vision AI 平台專案

> AUO 內部 Vision AI 平台：以 NodeRED 為組裝環境，FastAPI 封裝 AI 推論服務，部署至邊緣裝置（x86 / Jetson NX / Nano）。

---

## 平台架構

```
開發環境（Windows/Linux x86）
  → FastAPI Service（Python）+ Docker 封裝
    → GitLab CI/CD → Azure ACR（AUO DockerHub）
      → 組裝環境（NodeRED, tcai008）
        → Gateway → 邊緣裝置部署（NX arm64 / Nano arm64）
```

### 三個環境

| 環境 | 說明 | 平台 |
|------|------|------|
| 開發環境 | 程式碼開發、單元測試 | Windows x86 / Linux x86 |
| 組裝環境 | NodeRED 拉 flow 組裝 App | Linux x86（tcai008） |
| 部署環境 | 實際執行環境 | Linux x86 / NX arm64 / Nano arm64 |

---

## Component 開發 SOP

### Step 1：FastAPI 服務開發

```bash
# 啟動測試
PORT=5001
uvicorn main:app --host=0.0.0.0 --port=${PORT}
# 瀏覽器開啟 localhost:5001/docs 測試 API
```

**多 worker 啟動（gunicorn）**：

```bash
PORT=5001
WORKER=3
gunicorn main:app --workers ${WORKER} --threads 1 \
  --worker-class uvicorn.workers.UvicornWorker --bind 0.0.0.0:${PORT}
```

**threading.Lock 防 race condition**（推論時必用）：

```python
import threading
lock = threading.Lock()
with lock:
    inference_function()
```

**Port 申請**：http://tcai008/vision_ai/#!/0

### Step 2：Dockerfile 封裝

```dockerfile
FROM openaiacr.azurecr.io/python:3.7.3
WORKDIR /app
ARG PIP_INDEX_URL=http://autcbdpypi.corpnet.auo.com/simple
COPY ./requirements.txt ./requirements.txt
RUN pip install --no-cache-dir --upgrade -r ./requirements.txt
COPY ./yoursrc/ ./yoursrc/
COPY ./main.py ./main.py
COPY ./download_weight.sh ./download_weight.sh
RUN /bin/sh download_weight.sh
ENV PORT 5001
CMD uvicorn main:app --host=0.0.0.0 --port=${PORT}
```

**Weight 上傳**：Azure Blob Storage `itvideoaiappadls` → 容器 `build-weight`

```bash
wget '<Blob URL>' -O - | tar -x
```

### Step 3：GitLab CI 自動 Build & Push

- `.gitlab-ci.yml` 設定 `COMPONENT_NAME` 與 `COMPONENT_PORT`
- trigger：git tag（格式 `x.x.x`）或 Web UI
- CI 成功後自動推上 Azure ACR

### Step 4：NodeRED Node 封裝

1. Clone node package 範本
2. 進入個人 NodeRED 環境：`http://tcai008/user/<NT帳號>`
3. 建立 subflow → 匯出 flows.json → 更新 package.json
4. commit & push → git tag
5. 至 Assembly platform → 註冊 Component

### Step 5：App 開發、打包、上架

| 步驟 | 說明 |
|------|------|
| 申請 AppID | AppDev 填寫 App 資訊 |
| 打包 App | 上傳 flows.json + 選擇 Component → 下載 app.yaml |
| 上架 | 填寫說明/架構圖/影片，按下上架審核 |

### Step 6：遠端部署

- Email ADTEB3：`[Vision AI] App 上架完成，申請匯入至 Gateway`
  - 提供：Open AI App 連結、目標 Gateway
- **必須錄影**第一次部署過程（審核 + App Store 用途）

---

## Python 套件核心（requirements.txt）

```
python-multipart, numpy, opencv-python, pandas, Pillow
fastapi, uvicorn==0.16.0, gunicorn
```

Python 版本：3.6.9 / 3.7.3 / 3.8.10

---

## 待辦 / 開放問題

- [ ] 各 Component 的 Port 清單整理
- [ ] arm64 手動 Build 流程文件化
- [ ] GPIO Node 測試（組裝環境尚無硬體）

---

## 相關知識

- [[Concepts/FastAPI]]
- [[Concepts/容器化部署]]
- [[Concepts/Docker]]
- [[Concepts/物件偵測]]
