---
tags: [concept, fastapi, python, web框架, 模型部署]
created: 2026-09-05
updated: 2026-09-05
source_count: 1
---

# FastAPI

## 核心定義

FastAPI 是基於 Python 的現代高效能 Web 框架，以 ASGI（Async Server Gateway Interface）為基礎，自動生成 OpenAPI 文件，廣泛用於封裝 AI 推論服務為 REST API。

## 運作原理

### 基本啟動

```bash
uvicorn main:app --host=0.0.0.0 --port=5001
# 測試：瀏覽器開啟 localhost:5001/docs
```

### 多 Worker（生產環境）

```bash
gunicorn main:app --workers 3 --threads 1 \
  --worker-class uvicorn.workers.UvicornWorker \
  --bind 0.0.0.0:5001
```

### 常見輸入格式

| 格式 | 說明 | Header |
|------|------|--------|
| Binary 圖片 | `File(...)` | `multipart/form-data` |
| JSON dict | `Dict[str, Any]` | `application/json` |
| 簡單型態 | query string | `application/json` |
| dict + 圖片 | Pydantic + File | `multipart/form-data` |

### AI 推論服務封裝模式

```python
# 1. 先將模型封裝成類別
class our_model:
    def __init__(self, model_path):
        self.model = torch.load(model_path)
    def predict(self, img):
        return self.model(img)

# 2. 全域載入模型（避免每次請求重新載入）
model = our_model("your_model_path")

# 3. FastAPI 端點呼叫
@app.post("/predict/")
def predict(file: bytes = File(...)):
    image = bytes_to_cv2image(file)
    return {"result": model.predict(image)}
```

### threading.Lock（推論競爭條件防護）

```python
import threading
lock = threading.Lock()

with lock:
    result = model.predict(image)  # 確保同一時間只有一個執行緒推論
```

> ⚠️ 多 worker 同時呼叫深度學習模型時，若無 lock 會產生 race condition 導致結果錯誤或崩潰。

## Docker 封裝標準流程

```dockerfile
FROM python:3.7.3
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
ENV PORT 5001
CMD uvicorn main:app --host=0.0.0.0 --port=${PORT}
```

## GitLab CI/CD 整合

- git tag 觸發 CI → build Docker image → push 至 Azure ACR
- tag 格式：`x.x.x`（語意化版本）
- CI 驗證：啟動 container + `sleep 15` + 檢查 process 狀態

## 優點與限制

**優點**：
- 自動生成 `/docs` Swagger UI，便於測試
- 型別標注 + Pydantic 驗證，減少 API 錯誤
- 高效能，適合 AI 推論服務

**限制**：
- 單 process 下多執行緒共用模型需手動加 lock
- 非同步（async）與同步混用需注意阻塞問題

## 與相關概念的關係

- 相似：Flask（更輕量，但無自動文件）
- 使用：[[Concepts/Docker]]（封裝為 image）
- 使用：[[Concepts/容器化部署]]（部署至邊緣裝置）
- 上位：Python Web 框架

## 來源

- [[13-Projects/Vision-AI平台]]
