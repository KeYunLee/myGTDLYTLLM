---
tags: [atlas, moc, 模型部署, mlops, devops]
created: 2026-09-04
updated: 2026-09-05
---

# 🚀 MOC-ModelDeployment — 模型部署知識地圖

> 從訓練完成的模型到生產環境落地，涵蓋格式轉換、加速推論、容器化部署的導航地圖。
> 相關 Wiki 總目錄 → [[30-Wiki/index]]

---

## 📦 模型格式與轉換

> 模型序列化與跨框架互通

- [[30-Wiki/Concepts/ONNX]] — 開放神經網路交換格式，跨框架橋樑

---

## ⚡ 模型加速與推論優化

> 讓模型在生產環境中更快執行

- [[30-Wiki/Concepts/TensorRT]] — NVIDIA TensorRT 推論加速引擎
- [[30-Wiki/Concepts/模型加速與部署]] — 加速技術綜覽（量化、剪枝、蒸餾等）
- [[30-Wiki/Concepts/AI模型部署]] — 模型部署策略與 MLOps 實踐

---

## 🐳 容器化與 DevOps

> 環境一致性與自動化部署

- [[30-Wiki/Concepts/Docker]] — Docker 核心概念與使用
- [[30-Wiki/Concepts/容器化部署]] — 容器化模型服務的實踐方式

---

## 🏭 工業 AI 應用

> 工業場域的 AI 落地與 MLOps 維運

- [[30-Wiki/Concepts/PHM]] — 預測性設備維護：GreenPlum / MSSQL Pipeline、RabbitMQ 維運

---

## 🔗 相關地圖

- [[MOC-DeepLearning]] — 深度學習框架與訓練
- [[MOC-DevTools]] — 開發工具與環境管理

---

## 📚 待探索主題

- [ ] Kubernetes 模型服務編排
- [ ] Triton Inference Server
- [ ] MLflow / MLOps 工作流
- [ ] Edge Deployment（Jetson / Mobile）
