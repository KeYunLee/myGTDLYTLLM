# Wiki 操作日誌

> ⚠️ **Append-only**：只可追加新條目，禁止修改或刪除舊條目。
> 格式：`## [YYYY-MM-DD] <操作類型> | <標題>`
> 操作類型：`ingest` / `query` / `lint` / `weekly-review` / `init`

## [2026-09-04] cleanup | 移除 Entities/ 與 Comparisons/ 類別
- 操作：刪除空目錄 `30-Wiki/Entities/`、`30-Wiki/Comparisons/`
- 更新：`30-Wiki/index.md`（移除兩個空 section）
- 更新：`GEMINI.md` v1.2（架構表、Ingest、Query、index 格式模板）
- 原因：兩目錄自 init 後從未使用，符合 caveman 原則——廢物刪掉

---

## [2026-09-04] init | 知識庫初始化

- 建立資料夾架構（10-Action / 20-Atlas / 30-Wiki / 40-Raw / 50-Artifacts）
- 建立 CLAUDE.md 行為準則合約 v1.0
- 建立 index.md、log.md、Home.md、MOC-LLM.md
- 建立模板：daily-template、concept-template、summary-template、project-template
- 衝突標記：無

---

## [2026-09-04] ingest | 40-Raw/Web_Clippings 全部 10 個素材

- 摘要頁（10 頁）：
  - [[Summaries/Anomaly-detection]]
  - [[Summaries/Caveman-prompt]]
  - [[Summaries/Docker-tutorial]]
  - [[Summaries/Github-Copilot]]
  - [[Summaries/Linux-cheatsheet]]
  - [[Summaries/Python基礎]]
  - [[Summaries/TensorRT-tutorial]]
  - [[Summaries/Torch-tutorial]]
  - [[Summaries/grill-me-workflow]]
  - [[Summaries/圖片分類與物件偵測]]
- 更新概念（11 頁）：
  - [[Concepts/Anomaly-Detection]], [[Concepts/Docker]], [[Concepts/TensorRT]]
  - [[Concepts/PyTorch]], [[Concepts/Python基礎]], [[Concepts/Linux-Bash]]
  - [[Concepts/物件偵測]], [[Concepts/Prompt-Engineering]], [[Concepts/Agentic-Coding]]
  - [[Concepts/模型加速與部署]], [[Concepts/Github-Copilot]]
- 衝突：無

---

## [2026-09-04] lint | 全域失效連結健康檢查

- 掃描範圍：`30-Wiki/`、`20-Atlas/`、`50-Artifacts/`
- 發現失效連結：32 個（分兩類）
- **類型 A — 新建 Concept 頁面（8 頁）**：
  - [[Concepts/ONNX]]（修復 PyTorch.md、TensorRT.md、TensorRT-tutorial、Torch-tutorial）
  - [[Concepts/虛擬環境管理]]（修復 Linux-Bash.md、Python基礎.md、Linux-cheatsheet、Python基礎 Summary）
  - [[Concepts/AI-Coding-Assistant]]（修復 Prompt-Engineering.md、Caveman-prompt、Github-Copilot Summary）
  - [[Concepts/容器化部署]]（修復 Docker-tutorial Summary）
  - [[Concepts/TDD]]（修復 grill-me-workflow Summary）
  - [[Concepts/Embedding-方法]]（修復 Anomaly-detection Summary）
  - [[Concepts/圖像分類]]（修復 物件偵測.md）
  - [[Concepts/AI模型部署]]（修復 Anomaly-Detection.md）
- **類型 B — MOC-LLM.md 佔位符標注（24 個連結）**：
  - 在 `20-Atlas/MOC-LLM.md` 中對尚未建立的 LLM Concepts / Entities / Comparisons 加上 `*(待建立)*` 標注
  - 涉及：Transformer, Attention, Tokenization, Pre-training, Fine-tuning, LoRA, RLHF, DPO, RAG, Agent, Function-Calling, GPT-4, Claude, Gemini, Llama, RAG-vs-FineTuning, LoRA-vs-Full-FineTuning
- 更新：`30-Wiki/index.md`（total_pages 21 → 29，新增 8 頁 Concepts 列表）
- 衝突：無

---

## [2026-09-04] ingest | 40-Raw/Web_Clippings/Tensorflow tutorial

- 摘要頁：[[Summaries/Tensorflow-tutorial]]
- 新建概念（1 頁）：[[Concepts/TensorFlow]]
- 更新概念（1 頁）：[[Concepts/PyTorch]]（補充 TensorFlow 對比連結）
- 更新：`30-Wiki/index.md`（total_pages 29 → 31）
- 衝突：無

## [2026-09-04] atlas-update | 將 30-Wiki/Concepts 全部 concepts 加入 20-Atlas MOC
- 新建 MOC：[[20-Atlas/MOC-DeepLearning]]、[[20-Atlas/MOC-ModelDeployment]]、[[20-Atlas/MOC-AITools]]、[[20-Atlas/MOC-DevTools]]
- 更新：[[20-Atlas/Home]] 加入四個新 MOC 連結
- 涵蓋概念（20 個）：PyTorch, TensorFlow, Embedding-方法, 圖像分類, 物件偵測, Anomaly-Detection, ONNX, TensorRT, 模型加速與部署, AI模型部署, Docker, 容器化部署, AI-Coding-Assistant, Github-Copilot, Agentic-Coding, Prompt-Engineering, Python基礎, 虛擬環境管理, Linux-Bash, TDD
- 衝突：無

---

## [2026-09-04] ingest | 40-Raw/Web_Clippings 新增 4 個素材

- 摘要頁（4 頁）：
  - [[Summaries/OpenClaude-RPA]]
  - [[Summaries/RAG-技術優化]]
  - [[Summaries/YOLO-SAHI]]
  - [[Summaries/YOLOv7-to-ONNX]]
- 新建概念（2 頁）：
  - [[Concepts/RAG]]（RAG 系統架構、混合檢索、Reflexion 反思模式）
  - [[Concepts/RPA-Agentic]]（OpenClaude、視覺-動作迴圈、Windows Control Skill）
- 更新概念（3 頁）：
  - [[Concepts/物件偵測]]（補充 YOLO11、SAHI 切片推論）
  - [[Concepts/ONNX]]（補充 YOLOv7 端到端 ONNX 匯出參數）
  - [[Concepts/Embedding-方法]]（補充 RAG 向量嵌入策略、混合檢索、Re-ranking）
- 更新：`30-Wiki/index.md`（total_pages 31 → 37）
- 衝突：無

---

## [2026-09-05] process-inbox | 11-Inbox 四個素材（略過 auto_cluster）

- 分類：跨日專案 → 新建 `13-Projects/` 三個檔案
  - [[13-Projects/PHM系統]]（來源：PHM架構圖.md + PHM維運.md）
  - [[13-Projects/Vision-AI平台]]（來源：Vision AI 平台開發手冊.md）
  - [[13-Projects/頤康照護場域]]（來源：頤康照護場域.md）
- 新建概念（3 頁）：
  - [[Concepts/PHM]]（預測性設備維護：Pipeline、記憶體管理、RabbitMQ 維運）
  - [[Concepts/FastAPI]]（推論封裝、threading.Lock、Docker + GitLab CI）
  - [[Concepts/Action-Recognition]]（Openpose + Multi Object Tracker + DDNet）
- 更新概念（1 頁）：
  - [[Concepts/容器化部署]]（補充 Vision AI GitLab CI + ACR + NodeRED 部署模式）
- 更新：`30-Wiki/index.md`（total_pages 37 → 40）
- 略過：`auto_cluster 程式流程圖.md`（使用者指定暫不處理）
- 衝突：無

---

## [2026-09-05] ingest | 40-Raw/Web_Clippings 新增 4 個素材（LLM Wiki + Obsidian SOP）

- 摘要頁（2 頁）：
  - [[Summaries/LLM-Wiki-Pattern]]（合併 3 個素材：Karpathy gist + 104職場力 + 卡片盒筆記法實測）
  - [[Summaries/Obsidian-Claude-Code-SOP]]（恆遠數位科技 7 步驟 SOP）
- 新建概念（2 頁）：
  - [[Concepts/LLM-Wiki-Pattern]]（三層架構、ingest/query/lint、RAG vs Wiki、Model Collapse 風險）
  - [[Concepts/第二大腦-PKM]]（Zettelkasten / LYT / PARA / LLM Wiki 四大方法論比較）
- 更新概念（1 頁）：
  - [[Concepts/RAG]]（補充 RAG vs LLM Wiki Pattern 對比表格）
- 更新：`30-Wiki/index.md`（total_pages 40 → 44）
- 衝突：無
