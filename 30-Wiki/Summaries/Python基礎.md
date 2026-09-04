---
tags: [summary, python, 基礎]
created: 2026-09-04
updated: 2026-09-04
source_count: 1
---

# Python 基礎摘要

## 原始素材
- 來源：[[40-Raw/Web_Clippings/Python基礎]]
- Source URL：https://hackmd.io/iYiRsV_LTmOTuKrl-d7OOQ

## 摘要

本篇為 Python 開發基礎完整筆記，重點在環境配置、套件管理、語言特性、效能分析。

### 版本對應表
- Ubuntu 18.04 → Python 3.6.x；Ubuntu 20.04 → Python 3.8.x；Ubuntu 22.04 → Python 3.10.x
- Jetpack 4.x → Python 3.6.9；Jetpack 5.x → Python 3.8.10

### pip 完整操作
- 安裝：`pip install` / 離線安裝 `--no-index --find-link=.` / 指定虛擬環境 `--prefix`
- 下載：`pip download -d ./pip_package/ -r requirements.txt`
- 記錄：`pip freeze > requirements.txt`
- 參數：`--proxy`、`--cert`、`--python-version`、`--only-binary`、`--platform`
- build whl：`pip wheel --wheel-dir=/tmp/wheelhouse SomePackage`

### os.path 常用語法
- `os.getcwd()` / `os.path.join()` / `os.path.abspath(os.path.join(..., os.path.pardir))`
- `os.path.exists()` / `os.makedirs()` / `os.path.getsize()` / `os.path.getmtime()`

### Python 語言特性
- **import 相對/絕對路徑**：直接執行 `.py` 不可用相對路徑；需 `python3 -m` 方式並至少有一個 `.`
- **super() 繼承**：`super().__init__(...)` 呼叫父類別初始化
- **yield 迭代器**：`next(generator)` 單次取值 / for 迴圈使用
- **裝飾器 @fun**：`funB = funA(funB)` 等價；多個裝飾器從內到外執行
- **threading.Lock**：`with lock:` 保護共享資源，適用 Flask server 多執行緒

### 實用工具
- **tqdm**：`for i in tqdm(range(N)):` / while loop 搭配 `pbar.update(1)`
- **random.sample**：不重複隨機抽樣
- **cProfile**：`python3 -m cProfile -o out.prof script.py`；`pstats.Stats` 分析

### 開發工具
- **PyCharm debug**：Step Over(F8) / Step Into(F7) / Step Out(Shift+F8) / Resume(F9)
- **jupytext**：`.ipynb` ↔ `.py` 相互轉換，適合版本控管

### 型態註解
- function：`def get_value(json: str) -> dict:`
- variable：`price: float = 19.99`

## 關聯概念
- [[Concepts/Python基礎]]
- [[Concepts/虛擬環境管理]]
- [[Concepts/Linux-Bash]]
