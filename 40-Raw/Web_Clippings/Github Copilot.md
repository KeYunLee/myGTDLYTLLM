---
title: "Github Copilot"
source: "https://hackmd.io/skK8UX6MSRS1uUuTFXnl_A"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
## 安裝Github Copilot CLI

- 開始PowerShell: 可用windowns內的或是VSCode內的
- 設定環境變數: 直接複製，在PowerShell上執行

```bash
$env:HTTPS_PROXY = "http://10.248.15.7:3128"
$env:HTTP_PROXY = "http://10.248.15.7:3128"
$env:NO_PROXY = "localhost,127.0.0.1"

# 添加 npm 路徑（如果還沒有的話）
$npmPath = "$env:APPDATA\npm"
$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")
if ($currentPath -notlike "*$npmPath*") {
    [Environment]::SetEnvironmentVariable("Path", "$currentPath;$npmPath", "User")
    Write-Host "已將 $npmPath 添加到 PATH" -ForegroundColor Green
} else {
    Write-Host "$npmPath 已經在 PATH 中" -ForegroundColor Yellow
}
$env:Path = [Environment]::GetEnvironmentVariable("Path", "User") + ";" + [Environment]::GetEnvironmentVariable("Path", "Machine")

# 添加 PowerShell路徑，確保AI能建立venv與其他操作
$addPath = "C:\Program Files\PowerShell\7"
$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")
if ($currentPath -notlike "*$addPath*") {
    [Environment]::SetEnvironmentVariable("Path", "$currentPath;$addPath", "User")
    Write-Host "已將 $addPath 添加到 PATH" -ForegroundColor Green
} else {
    Write-Host "$addPath 已經在 PATH 中" -ForegroundColor Yellow
}
$env:Path = [Environment]::GetEnvironmentVariable("Path", "User") + ";" + [Environment]::GetEnvironmentVariable("Path", "Machine")
```

- 安裝Github Copilot CLI

```bash
npm install -g @github/copilot
npm install -g @fission-ai/openspec@latest
```

- 登入Github Copilot(NT帳號): [https://github.com/enterprises/AUOCorporation](https://github.com/enterprises/AUOCorporation)
- 執行Github Copilot CLI: **每次執行前確保環境變數都已import**

```bash
$env:HTTPS_PROXY = "http://10.248.15.7:3128"
$env:HTTP_PROXY = "http://10.248.15.7:3128"
$env:NO_PROXY = "localhost,127.0.0.1"

copilot --allow-all-tools # allow-all-tools 代表授權AI直接操作你執行copilot時的目錄與子目錄(包含刪除)，都不需要透過你的同意
```

- 登入Github Copilot CLI: `/login`
	- 選企業帳號登入
		- 企業Github Copilot: [https://github.com/enterprises/AUOCorporation](https://github.com/enterprises/AUOCorporation)
		- Copilot跳出以下訊息
		- Enter one-time code: 5D8A-641F at [https://github.com/login/device](https://github.com/login/device)
		- 至 [https://github.com/login/device](https://github.com/login/device) 網址輸入 one-time code **5D8A-641F**
		- 等待約1分鐘即可使用

選擇 Repo