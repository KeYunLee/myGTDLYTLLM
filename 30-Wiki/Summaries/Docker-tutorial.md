---
tags: [summary, docker, devops, 基礎]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Docker Tutorial 摘要

## 原始素材
- 來源：[[40-Raw/Web_Clippings/Docker tutorial]]
- Source URL：https://hackmd.io/_3DV_kAxSU6_GJ-5FpgFUw

## 摘要

本篇為 Docker 完整操作筆記，涵蓋從映像建置到容器管理的完整工作流程。

### 核心主題

#### Dockerfile 撰寫
- **基本 case**：FROM + COPY + WORKDIR + RUN + pip install 離線套件
- **進階 case**：多平台 `--platform=$TARGETPLATFORM`、`--mount=type=bind/cache` 掛載、環境變數設定（NVIDIA_DRIVER_CAPABILITIES、LC_ALL）、gunicorn uvicorn worker 作為 entrypoint

#### 建置映像（docker build / buildx）
- 當下平台：`docker build -f Dockerfile -t name:tag .`
- 跨平台：`docker buildx build --platform amd64/arm64 --load`
- 支援 `--build-arg` 傳入 proxy、`--no-cache`

#### 映像管理
- pull / push / inspect / save / load / rmi / tag（rename）
- **docker manifest**：多平台映像合併（rm → create → push 流程）

#### 容器操作
- run 參數：`-d`（背景）、`-it`、`--rm`、`--gpus all`、`--name`、`-v`（volume mount）、`-p`（port mapping）、`-e`（環境變數）、`--log-opt`（日誌大小限制）
- exec / start / stop / rm / commit

#### 其他
- 安裝 arm64 模擬器（binfmt）
- 清理 docker 空間：`docker system df` / `docker system prune` / `prune -a`

## 關聯概念
- [[Concepts/Docker]]
- [[Concepts/容器化部署]]
