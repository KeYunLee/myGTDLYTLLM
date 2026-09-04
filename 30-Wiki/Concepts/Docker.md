---
tags: [concept, docker, devops, 容器化]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Docker

## 核心定義
Docker 是基於 Linux 容器（LXC）的開放原始碼容器化平台，允許將應用程式及其相依套件打包成可攜帶的映像（Image），並在任何環境中一致地運行。

## 運作原理

### 核心概念
| 概念 | 說明 |
|------|------|
| Image（映像） | 應用程式的靜態快照，包含 OS、套件、程式碼 |
| Container（容器） | Image 的執行實例，彼此隔離 |
| Dockerfile | 建置 Image 的指令腳本 |
| Registry（倉庫） | 存放 Image 的遠端服務（Docker Hub / Azure CR / NGC） |
| Manifest | 多平台（amd64/arm64）映像清單 |

### 典型工作流程
```
Dockerfile → docker build → Image → docker push → Registry
Registry → docker pull → docker run → Container（執行中）
```

### 跨平台建置（buildx）
```bash
docker buildx build --platform amd64  # x86 伺服器
docker buildx build --platform arm64  # Jetson / Apple Silicon
docker manifest create ... && docker manifest push  # 合併多平台 manifest
```

### 關鍵 Dockerfile 指令
- `FROM --platform=$TARGETPLATFORM`：多平台基底映像
- `RUN --mount=type=cache`：快取加速建置
- `ARG TARGETARCH`：動態取得目標架構

### Container 管理指令速查
```bash
docker ps -a         # 列出所有容器
docker exec -ti ID bash  # 進入執行中容器
docker start/stop/rm ID  # 啟動/停止/刪除容器
docker commit -m "msg" ID name:tag  # 容器 → Image
docker system prune  # 清理未用資源
```

## 優點與限制
- **優點**：環境一致性、快速部署、版本控管、跨平台（buildx + manifest）
- **限制**：GPU 支援需 `--gpus all` / `--runtime nvidia`；ARM 交叉編譯時效能有限

## 與相關概念的關係
- 相似：Kubernetes（容器編排）
- 上位：[[Concepts/模型加速與部署]]
- 下位：[[Concepts/TensorRT]]（通常在 Docker 環境中運行）

## 來源
- [[Summaries/Docker-tutorial]]
- [[Summaries/TensorRT-tutorial]]
