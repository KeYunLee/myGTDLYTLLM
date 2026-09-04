---
tags: [concept, linux, bash, 基礎, 開發工具]
created: 2026-09-04
updated: 2026-09-04
source_count: 2
---

# Linux / Bash

## 核心定義
Linux 是開源 Unix-like 作業系統核心；Bash（Bourne Again Shell）是其最廣泛使用的命令列介面與腳本語言。本卡片整理 ML 研究與工業部署場景下的 Linux / Bash 實踐知識。

## 運作原理

### 環境與套件管理
```bash
# 環境變數設定
export PYTHONPATH=/path/to/project:$PYTHONPATH
export CUDA_VISIBLE_DEVICES=0,1
# 臨時指定
env CUDA_VISIBLE_DEVICES=0 python3 main.py
# 永久設定（寫入 ~/.bashrc 或 ~/.zshrc）
source ~/.bashrc
```

### 三種虛擬環境工具比較

| 面向 | venv | virtualenvwrapper | conda |
|------|------|------------------|-------|
| 建立 | `virtualenv venv_name` | `mkvirtualenv venv_name` | `conda create -n name python=3.x` |
| 啟動 | `source ./bin/activate` | `workon venv_name` | `conda activate name` |
| 刪除 | `rm -R venv_name` | `rmvirtualenv venv_name` | `conda env remove -n name` |

### 串接指令（I/O 控制）
```bash
cmd > out.txt        # 標準輸出覆寫
cmd >> out.txt       # 標準輸出 append
cmd 2> err.txt       # 錯誤輸出
cmd &> all.txt       # 合併輸出
cmd1 | cmd2          # Pipeline
cmd &                # 背景執行
cmd1 && cmd2         # 成功才繼續
cmd1 || cmd2         # 失敗才執行
```

### 常用工具組合
```bash
# 搜尋 + 刪除
find <dir> -mtime +1 -type f -exec rm -rf {} \;
# grep + sed + awk 文字處理
grep 'pattern' file | sed 's/old/new/g' | awk '{print $3}'
# 批次執行
ls *.mp4 | xargs -i python3 process.py --video {}
# 刪除特定檔案以外的 weight
ls | grep -v '_final.\|_best.\|_last.' | xargs -i rm
```

### 排程（crontab）
```bash
crontab -e
# 每 5 分鐘執行
*/5 * * * * bash /path/to/script.sh
```

### 壓縮/解壓縮速查
| 格式 | 壓縮 | 解壓縮 |
|------|------|--------|
| tar | `tar cvf file.tar dir` | `tar xvf file.tar` |
| tar.gz | `tar zcvf file.tar.gz dir` | `tar zxvf file.tar.gz` |
| zip | `zip -r file.zip dir` | `unzip file.zip` |
| gzip | `gzip -k file` | `gzip -d file.gz` |

## 優點與限制
- **優點**：強大的文字處理能力；腳本自動化；與 Python 深度整合
- **限制**：語法較繁瑣；字串處理的引號規則容易混淆

## 與相關概念的關係
- 相似：[[Concepts/Python基礎]]（開發環境）
- 上位：[[Concepts/虛擬環境管理]]
- 相關：[[Concepts/Docker]]（Linux 容器基礎）

## 來源
- [[Summaries/Linux-cheatsheet]]
- [[Summaries/Python基礎]]
