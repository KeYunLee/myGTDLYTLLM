---
tags: [summary, linux, bash, 基礎]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Linux 指令速查摘要

## 原始素材
- 來源：[[40-Raw/Web_Clippings/Linux]]
- Source URL：https://hackmd.io/q9GmShBaQU6Q1kNUKjrxQw

## 摘要

本篇為 Linux / bash 完整操作筆記，涵蓋日常開發與 ML 研究常用指令。

### 主要分類

#### 環境變數與 PYTHONPATH
- `export PYTHONPATH=<path>:$PYTHONPATH`
- 直接執行 `.py` vs `python3 -m module` 的 PYTHONPATH 差異
- `env PYTHONPATH=... CUDA_VISIBLE_DEVICES=... python3 ...` 臨時設定

#### CUDA / GPU 管理
- 指定 GPU：`env CUDA_VISIBLE_DEVICES=0,1 python3 xxxx.py`
- 查看 CUDA/cuDNN 版本：`nvcc --version` / `cat /usr/local/cuda/include/cudnn.h`
- TensorFlow/Keras GPU 自動增長記憶體：`gpu_options = tf.GPUOptions(allow_growth=True)`

#### 虛擬環境（三種工具對比）
| 工具 | 建立 | 啟動 | 刪除 |
|------|------|------|------|
| venv | `virtualenv venv_name` | `source ./bin/activate` | `rm -R venv_name` |
| virtualenvwrapper | `mkvirtualenv venv_name` | `workon venv_name` | `rmvirtualenv venv_name` |
| conda | `conda create --name venv_name python=3.6` | `conda activate venv_name` | `conda env remove --name venv_name` |

#### 串接指令（I/O 重導向）
- `>` / `>>` 標準輸出 / append
- `2>` 錯誤輸出、`&>` 合併輸出
- `|` pipeline、`&` 背景、`&&` 成功才繼續、`||` 失敗才執行

#### 資料夾/檔案操作
- 查看大小：`du -sh * | sort -rh`
- 搜尋：`find . -name "*.mp4" -mtime +1 -exec rm -rf {} \;`
- 壓縮/解壓縮：tar / zip / gzip / rar / 7z 完整指令對照

#### 資料內容處理
- grep + sed + awk 組合應用
- xargs 批次執行 script
- crontab 排程設定

#### 邏輯控制
- if / elif / else 條件判斷
- for loop 四種範例（glob 迭代、range 迭代、浮點數序列、副檔名處理）

#### 其他
- `lsof -p pid`：查看 process 開啟的檔案
- curl POST 帶 header + body
- kill process：`ps uxf | grep xxx | awk '{print $2}' | xargs kill`

## 關聯概念
- [[Concepts/Linux-Bash]]
- [[Concepts/Python基礎]]
- [[Concepts/虛擬環境管理]]
