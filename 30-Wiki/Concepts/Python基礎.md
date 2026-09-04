---
tags: [concept, python, 基礎, 開發工具]
created: 2026-09-04
updated: 2026-09-04
source_count: 2
---

# Python 基礎

## 核心定義
Python 是一種高階、直譯式、通用程式語言，在機器學習、資料科學與後端開發領域廣泛使用。本卡片整理 Python 開發環境設定、語言特性與常用工具的實踐知識。

## 運作原理

### PYTHONPATH 機制
- `python3 script.py`：script 所在目錄作為 PYTHONPATH，**不可**使用相對 import
- `python3 -m package.module`：當前工作目錄（PWD）作為 PYTHONPATH，**可**使用相對 import
- 最佳實踐：使用 `sys.path.insert(0, os.path.dirname(__file__))` 以 script 位置為基準

### 語言特性

#### import 相對/絕對路徑規則
1. 直接執行 `.py`：不可使用相對 import（`from .xxx import`）
2. 使用 `-m` 執行：可使用相對 import，且呼叫層至少需有一個 `.`

#### yield 迭代器（Generator）
```python
def gen(n):
    while n <= 3:
        n += 1
        yield n
# 單次：next(gen(0))
# 迴圈：for i in gen(0): ...
```

#### 裝飾器（Decorator）
```python
@funA
@funB
def fun(): ...
# 等價於：fun = funA(funB(fun))
# 執行順序：由內到外（先 funC → funB → funA）
```

#### super() 繼承
```python
class Child(Parent):
    def __init__(self, ...):
        super().__init__(parent_args)  # 呼叫父類別 __init__
```

#### threading.Lock 機制
- `lock = threading.Lock()`
- `with lock:` 區塊內為互斥區域
- 適用：多執行緒修改共享變數（Flask server）

### 效能工具
| 工具 | 用途 |
|------|------|
| cProfile | 函數級效能分析（`-m cProfile -o out.prof`） |
| line_profiler | 行級效能分析 |
| tqdm | 進度條顯示 |

### 型態註解（Type Hints）
```python
def parse(json: str) -> dict: ...
price: float = 19.99
```

## 優點與限制
- **優點**：語法簡潔、生態豐富（pip/conda）、ML 框架首選語言
- **限制**：GIL 限制真正多執行緒；直譯效能低於編譯語言

## 與相關概念的關係
- 相似：[[Concepts/Linux-Bash]]（開發環境）
- 上位：[[Concepts/虛擬環境管理]]
- 相關：[[Concepts/PyTorch]]

## 來源
- [[Summaries/Python基礎]]
- [[Summaries/Linux-cheatsheet]]
