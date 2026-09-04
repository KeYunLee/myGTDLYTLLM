---
title: "OpenClaude"
source: "https://hackmd.io/kO8NkTorT3mMZ8DM-iBjLg"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
## 環境設定

- .env檔

```bash
# ==========================================================================
# Azure AI Foundry (Microsoft Foundry — Anthropic Claude)
#===========================================================================
CLAUDE_CODE_USE_FOUNDRY=1
ANTHROPIC_FOUNDRY_BASE_URL=https://AUO-RPA-foundry.services.ai.azure.com/anthropic/
ANTHROPIC_FOUNDRY_API_KEY=<YOUR_AZURE_API_KEY_HERE>
CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS=1
CLAUDE_CODE_SKIP_FOUNDRY_AUTH=1
# Azure Foundry 不支援 thinking.type.enabled，停用 thinking
DISABLE_INTERLEAVED_THINKING=1
CLAUDE_CODE_DISABLE_THINKING=1
MAX_THINKING_TOKENS=0
```

## System Prompt設定

- 寫在執行目錄下的CLAUDE.md檔內  
	python套件安裝:
	- 優先使用.venv的pip安裝套件，沒有虛擬環境就建立一個虛擬環境，確保在虛擬環境中安裝套件
		- 需要用proxy
		- 使用.venv\\Scripts\\python.exe虛擬環境
	```bash
	​​​​pip install -i https://pypi.org/simple --proxy http://proxy-sslbypass.corpnet.auo.com:3128 --trusted-host pypi.org --trusted-host files.pythonhosted.org {套件名稱}
	```
	相關環境變數設定
	```bash
	​​​​$env:HTTPS_PROXY = "http://10.248.15.7:3128"
	​​​​$env:HTTP_PROXY = "http://10.248.15.7:3128"
	​​​​$env:NO_PROXY = "localhost,127.0.0.1"
	​​​​# 添加 npm 路徑（如果還沒有的話）
	​​​​$npmPath = "$env:APPDATA\npm"
	​​​​$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")
	​​​​if ($currentPath -notlike "*$npmPath*") {
	​​​​    [Environment]::SetEnvironmentVariable("Path", "$currentPath;$npmPath", "User")
	​​​​    Write-Host "已將 $npmPath 添加到 PATH" -ForegroundColor Green
	​​​​} else {
	​​​​    Write-Host "$npmPath 已經在 PATH 中" -ForegroundColor Yellow
	​​​​}
	​​​​$env:Path = [Environment]::GetEnvironmentVariable("Path", "User") + ";" + [Environment]::GetEnvironmentVariable("Path", "Machine")
	​​​​# 添加 PowerShell路徑，確保AI能建立venv與其他操作
	​​​​$addPath = "C:\Program Files\PowerShell\7"
	​​​​$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")
	​​​​if ($currentPath -notlike "*$addPath*") {
	​​​​    [Environment]::SetEnvironmentVariable("Path", "$currentPath;$addPath", "User")
	​​​​    Write-Host "已將 $addPath 添加到 PATH" -ForegroundColor Green
	​​​​} else {
	​​​​    Write-Host "$addPath 已經在 PATH 中" -ForegroundColor Yellow
	​​​​}
	​​​​$env:Path = [Environment]::GetEnvironmentVariable("Path", "User") + ";" + [Environment]::GetEnvironmentVariable("Path", "Machine")
	```

## 設定Skill

- Window control技能，放到專案的以下資料夾

```bash
~\.openclaude\skills\openclaw-skills-windows-control-1.0.1
```

## 啟動OpenClaude

```bash
Get-Content .env | Where-Object { $_ -notmatch '^\s*#' -and $_ -match '=' } | ForEach-Object {
    $key, $value = $_ -split '=', 2
    [System.Environment]::SetEnvironmentVariable($key.Trim(), $value.Trim(), 'Process')
}

openclaude --model claude-sonnet-5 --allow-dangerously-skip-permissions
```

## 執行Prompt

## 執行第一次

> 用.venv\\Scripts\\python.exe虛擬環境，幫我去 [http://auo-skyeye.azurewebsites.net/performance?model=S\_L6A\_NB\_PI\_AOI](http://auo-skyeye.azurewebsites.net/performance?model=S_L6A_NB_PI_AOI) 上的左側 "瑕疵趨勢" ，進入畫面中後截圖，截圖統一放在./temp，截圖後直接餵給Agent看圖然取得座標位置，然後後再判斷後續操作，全程只能用window control技能與其script做出來，不要用playwright或webwright，途中也不要自己寫一個.py檔執行，中間如果要輸入帳密，帳號davidkylee，密碼 *自行填寫*

> 使用.venv虛擬環境，用.openclaude\\skills\\rpa-workflow-builder技能建立一個RPA腳本，腳本內容為，去 [http://auo-skyeye.azurewebsites.net/performance?model=S\_L6A\_NB\_PI\_AOI](http://auo-skyeye.azurewebsites.net/performance?model=S_L6A_NB_PI_AOI) 上的左側 "關於天眼"，進入畫面中後截圖，輸出截圖內容，最後在左側最下方帳號登出與關閉視窗，編寫過程中如要輸入帳密，帳號davidkylee，密碼 *自行填寫*

> 使用.venv虛擬環境，用.openclaude\\skills\\rpa-workflow-builder\\SKILL.md技能建立一個RPA腳本，腳本內容為，至 Amazon 上搜尋 \[Macbook Air 13-inch\]，篩選條件為評分四顆星以上，將第一個品項加入購物車，並進入購物車頁面確認有加入成功，最後關閉網頁。  
> [https://www.amazon.sg/](https://www.amazon.sg/)

> 使用.venv虛擬環境，用.openclaude\\skills\\rpa-workflow-builder\\SKILL.md技能建立一個RPA腳本，腳本內容為，至 momo購物網 上搜尋 \[sony 索尼 藍芽耳機\]，篩選條件為8000元以上的商品中最便宜的一個，點進商品頁面，截圖存下來，最後關閉網頁。 [https://www.momoshop.com.tw/](https://www.momoshop.com.tw/)

## 封裝成skill腳本

> 幫我歸納可執行的操作流程(之前debug的步驟不用呈現)，每個操作流程請對應到window control技能的script 或 Agent看圖取座標

| # | 操作 | 對應方式 |
| --- | --- | --- |
| 1 | 設定剪貼簿內容為目標網址 | Bash： `printf '%s' "http://auo-skyeye.azurewebsites.net/performance?model=S_L6A_NB_PI_AOI" \| clip` |
| 2 | 截圖確認桌面/工作列狀態 | `screenshot.py` → Agent 看圖取得工作列 Chrome 圖示座標 |
| 3 | 點擊 Chrome 圖示開啟瀏覽器 | `click.py <x> <y>` （Chrome 圖示座標） |
| 4 | 截圖確認瀏覽器已開啟 | `screenshot.py` → Agent 看圖取得網址列座標 |
| 5 | 點擊網址列 | `click.py <x> <y>` （網址列座標） |
| 6 | 全選網址列內容 | `key_press.py "ctrl+a"` |
| 7 | 貼上目標網址 | `key_press.py "ctrl+v"` |
| 8 | 送出網址、載入頁面 | `key_press.py "enter"` |
| 9 | 截圖確認頁面載入結果（是否需登入） | `screenshot.py` → Agent 看圖判斷是否出現登入畫面 |
| 10a | 若需登入：截圖找帳號欄位座標 | `screenshot.py` → Agent 看圖取得帳號輸入框座標 |
| 10b | 點擊帳號欄位並輸入帳號 | `click.py <x> <y>` → `type_text.py "davidkylee"` |
| 10c | 點擊密碼欄位並輸入密碼 | `click.py <x> <y>` → `type_text.py "1234qwEr1234qwEr"` |
| 10d | 送出登入 | `key_press.py "enter"` （或點擊登入按鈕座標） |
| 11 | 截圖確認已進入首頁、找左側「資料品質」座標 | `screenshot.py` → Agent 看圖取得「資料品質」文字座標 |
| 12 | 點擊「資料品質」進入畫面 | `click.py <x> <y>` |
| 13 | 截圖確認已進入資料品質畫面 | `screenshot.py` → Agent 看圖解析畫面內容與各元件座標，判斷後續操作 |

> 流程正確，依據上述流程 製作.py檔，其中x y 座標都要填實際值，請用skill中的.py檔腳本來組合出整個流程的腳本

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
天眼 AOI - 資料品質頁面自動化流程

組合 windows-control skill 內建的 .py 腳本（click.py / key_press.py /
type_text.py / screenshot.py），依序完成：
  1. 開啟 Chrome
  2. 貼上目標網址並前往 http://auo-skyeye.azurewebsites.net/performance?model=S_L6A_NB_PI_AOI
  3. 若出現登入畫面，輸入帳號密碼並登入
  4. 點擊左側「資料品質」進入畫面
  5. 每個關鍵步驟後截圖存檔，方便事後用 Agent 看圖確認

所有座標皆為本機 1920x1080 螢幕、透過實際截圖 + 人工/Agent 讀圖取得的真實值。
若畫面版面改變（例如選單增減、視窗大小不同），座標需重新截圖校正。

執行方式：
    E:\\Users\\DavidKYLee\\.openclaude\\skills\\openclaw-skills-windows-control-1.0.1\\.venv\\Scripts\\python.exe auo_dataquality_flow.py
"""
import base64
import subprocess
import sys
import time
from pathlib import Path

# Windows 主控台預設編碼（cp1252/cp950）印中文會噴 UnicodeEncodeError，強制改 utf-8
if hasattr(sys.stdout, "reconfigure"):
    sys.stdout.reconfigure(encoding="utf-8")
    sys.stderr.reconfigure(encoding="utf-8")

# ---------------------------------------------------------------------------
# 環境設定
# ---------------------------------------------------------------------------
SKILL_DIR = Path(
    r"E:\Users\DavidKYLee\.openclaude\skills\openclaw-skills-windows-control-1.0.1"
)
PYEXE = str(SKILL_DIR / ".venv" / "Scripts" / "python.exe")
SCRIPTS = SKILL_DIR / "scripts"
TEMP = Path(r"E:\Users\DavidKYLee\git\openclaude-main\temp")
TEMP.mkdir(parents=True, exist_ok=True)

TARGET_URL = "http://auo-skyeye.azurewebsites.net/performance?model=S_L6A_NB_PI_AOI"
USERNAME = "davidkylee"
PASSWORD = "自行輸入"

# ---------------------------------------------------------------------------
# 實際座標（截圖 + Agent 讀圖取得，1920x1080）
# ---------------------------------------------------------------------------
COORD_CHROME_TASKBAR_ICON = (466, 1059)   # 工作列 Chrome 圖示
COORD_ADDRESS_BAR = (854, 62)             # Chrome 網址列
COORD_USERNAME_FIELD = (1250, 448)        # 登入頁「帳號」欄位
COORD_PASSWORD_FIELD = (1250, 523)        # 登入頁「密碼」欄位
COORD_LOGIN_BUTTON = (1217, 682)          # 登入頁「登入」按鈕
COORD_DATA_QUALITY_MENU = (94, 374)       # 左側選單「資料品質」

# ---------------------------------------------------------------------------
# 呼叫 windows-control skill 腳本的共用函式
# ---------------------------------------------------------------------------
def run_skill_script(script_name: str, *args) -> subprocess.CompletedProcess:
    """呼叫 skill/scripts 底下的既有 .py 檔（click.py, key_press.py, type_text.py...）"""
    cmd = [PYEXE, str(SCRIPTS / script_name), *[str(a) for a in args]]
    result = subprocess.run(cmd, capture_output=True, text=True)
    print(f"  -> {script_name} {args}: {result.stdout.strip()}")
    if result.returncode != 0:
        print(f"  !! stderr: {result.stderr.strip()}", file=sys.stderr)
    return result

def click(x: int, y: int) -> None:
    run_skill_script("click.py", x, y)

def key_press(combo: str) -> None:
    run_skill_script("key_press.py", combo)

def type_text(text: str) -> None:
    """僅適合純 ASCII、無特殊符號的文字；中文輸入法環境下可能被 IME 攔截。"""
    run_skill_script("type_text.py", text)

def set_clipboard(text: str) -> None:
    """用系統 clip.exe 設定剪貼簿內容，取代 type_text.py 對特殊字元/IME 不穩定的問題。"""
    subprocess.run(["clip"], input=text.encode("utf-8"), check=True)

def paste_text(text: str) -> None:
    """設定剪貼簿後以 ctrl+v 貼上，避免特殊字元遺漏或被輸入法轉換。"""
    set_clipboard(text)
    key_press("ctrl+v")

def screenshot(tag: str) -> Path:
    """呼叫 screenshot.py 取得 base64 PNG，解碼後存成檔案，回傳 PNG 路徑。"""
    result = subprocess.run(
        [PYEXE, str(SCRIPTS / "screenshot.py")], capture_output=True, text=True
    )
    b64_data = result.stdout.strip()
    (TEMP / f"{tag}.b64").write_text(b64_data)
    png_path = TEMP / f"{tag}.png"
    png_path.write_bytes(base64.b64decode(b64_data))
    print(f"  -> 截圖已存: {png_path}")
    return png_path

# ---------------------------------------------------------------------------
# 主流程
# ---------------------------------------------------------------------------
def main() -> None:
    print("[1/6] 開啟 Chrome")
    click(*COORD_CHROME_TASKBAR_ICON)
    time.sleep(1)

    print("[2/6] 貼上目標網址並前往")
    set_clipboard(TARGET_URL)
    click(*COORD_ADDRESS_BAR)
    key_press("ctrl+a")
    key_press("ctrl+v")
    key_press("enter")
    time.sleep(1)
    screenshot("flow_01_after_navigate")

    # 若帳號密碼已快取（cookie/session 未過期），網站會直接顯示目標頁面，
    # 不會出現登入表單；下面登入段落可依實際截圖判斷是否需要執行。
    print("[3/6] 輸入帳號")
    click(*COORD_USERNAME_FIELD)
    key_press("escape")  # 關閉瀏覽器自動填入建議清單，避免誤觸
    paste_text(USERNAME)

    print("[4/6] 輸入密碼")
    click(*COORD_PASSWORD_FIELD)
    paste_text(PASSWORD)
    screenshot("flow_02_credentials_filled")

    print("[5/6] 點擊登入")
    click(*COORD_LOGIN_BUTTON)
    time.sleep(2)
    screenshot("flow_03_after_login")

    print("[6/6] 點擊左側「資料品質」")
    click(*COORD_DATA_QUALITY_MENU)
    time.sleep(1)
    screenshot("flow_04_data_quality")

    print("流程結束，請檢查 temp/flow_04_data_quality.png 確認結果。")

if __name__ == "__main__":
    main()
```

## 執行第一次(old)

> 用.venv\\Scripts\\python.exe虛擬環境，幫我去 [http://auo-skyeye.azurewebsites.net/performance?model=S\_L6A\_NB\_PI\_AOI上的左側](http://auo-skyeye.azurewebsites.net/performance?model=S_L6A_NB_PI_AOI%E4%B8%8A%E7%9A%84%E5%B7%A6%E5%81%B4) "儀錶板" ，進入畫面中後截圖，截圖統一放在./temp，截圖後直接餵給AI看圖然後再判斷後續操作，全程只能用window control技能與其script做出來，不要用playwright或webwright，也不要再寫一個.py檔，中間如果要輸入帳密，帳號davidkylee，密碼 *自行填寫*

## 封裝成skill腳本(old)

> 幫我封裝成一個Skill腳本，後續直接執行此腳本就可以完成任務，此腳本不需有LLM語言模型協助，就可獨立完成任務

> 幫我封裝成一個Skill terminal 腳本，後續直接執行此腳本就可以完成任務，此腳本不需有LLM語言模型協助，就可獨立完成任務  
> 麻煩用window control技能與其script做出一個power shell的腳本

> 幫我把上述操作封裝成一個Skill腳本，後續直接執行此腳本就可以完成任務，此腳本不需有LLM語言模型協助，就可獨立完成任務，

選擇 Repo