---
title: "Vision AI 平台開發手冊"
source: "https://hackmd.io/he51pD0AS2Oc0_obFEANzQ"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
###### tags: 專案

## Warm up

- 展示App功能
- 目標設定
	- 主要功能
		- 可能的 UI
		- Web UI
		- OpenAI App 邊緣裝置部署架構與 Codeless NodeRED 架構
		- 能平展的 App 與 能平展的 Component

## 開發地圖

> [!danger] Danger
> 離線時無法預覽。

## Component建立與上架

## Vision AI 平台 Component 定義

- 開發出 NodeRED 上可讓 Codeless user 可使用的 Node
- 通常包含以下兩個部分
	1. Python 開發的 WebAPI Service -> 封裝為 Docker image in ACR
		- (此處以FastAPI為示範的開發框架)
		2. NodeRED 呼叫的上述 Web API 的 subflow -> 封裝為 node package in Gitlab

## 0\. 開發/組裝/部署，三個環境的介紹

### a. 開發環境: python 程式碼開發的環境

在原來可執行環境中額外安裝 **fastapi、uvicorn==0.16.0、python-multipart、gunicorn**

- windows x86: 確認程式可正常執行，使用windows開發需使用跨平台語言(ex: python)
- linux x86: 確認程式可正常執行

### b. 組裝環境: 由 Vision AI 平台提供的 NodeRED 環境

- linux x86: 使Component可在組裝環境供大家拉flow使用

### c. 部署環境: 將來實際安裝執行的 Test/Product 環境

- linux x86
- nx arm64 (手動build)
- nano arm64 (手動build)

## 1\. FastAPI 程式開發: 可在聯合開發環境(AI365探索環境)或自己開發環境

[FastAPI](https://fastapi.tiangolo.com/)

- 註冊 Vision AI 平台專屬的服務Port: [註冊 Port](http://tcai008/vision_ai/#!/0)
- [VideoApp Sample Code](http://tcaigitlab2/videoapp/build-fastapi-sample-svc)
- 上傳weight檔至聯合開發環境(AI365探索環境)中的H槽: [上傳網頁](http://gcp-aucm02.corpnet.auo.com/MLB/Explore/SettingShare)
- 介面定義: decompose、input、output
- 程式開發: 盡量先將自己的服務寫成類別或方法, 最後再用FastAPI呼叫即可

```python
# 將自己的服務寫成類別, 在yoursrc/model.py內
import torch
class our_model:
    def __init__(self, model_path):
        self.model_path = model_path
        self.model = torch.load(model_path)
    def predict(self, img):
        output = self.model(img_bgr)
        return output
```

```python
# 用FastAPI呼叫自己的服務, 在main.py內
from fastapi import FastAPI, File, Form
import numpy as np
import uvicorn
import os
import cv2
from PIL import Image
import io
from io import BytesIO
import base64
from starlette.responses import StreamingResponse
import threading
from fastapi.openapi.docs import get_swagger_ui_html
from fastapi.staticfiles import StaticFiles

from yoursrc.model import our_model

model_path = "your_model_path"
model = our_model(model_path)

app = FastAPI()
app.mount("/static", StaticFiles(directory="static"), name="static")

# 單binary圖片檔
from fastapi import File
@app.post("/service_url_for_binary/")
def service_name_for_binary(file: bytes = File(...)):
    image_bgr = bytes_to_cv2image(file)
    output = model.predict(image_bgr)
    return {"detections": output}

def bytes_to_cv2image(imgdata):
    cv2img = cv2.cvtColor(np.array(Image.open(BytesIO(imgdata))), cv2.COLOR_RGB2BGR)
    return cv2img

# 純dict(json)傳送
from typing import Dict, Any
@app.post("/service_url_for_dict/")
def service_name_for_dict(dict_obj: Dict[str, Any]):
    input_obj = dict_obj
    output = input_obj
    return output

# 多個簡單型態int, float, str, bool傳送
@app.post("/service_url_for_simply_type/")
def service_name_for_simply_type(str_obj: str = 'xxx', int_obj: int = 10, float_obj: float = 0.5):
    output = {
        str_obj: str_obj,
        int_obj: int_obj,
        float_obj: float_obj
    }
    return output

# 同時傳送dict與binary照片
def cv2image_to_base64(cv2img):
    retval, buffer_img = cv2.imencode('.jpg', cv2img)
    base64_str = base64.b64encode(buffer_img)
    str_a = base64_str.decode('utf-8')
    return str_a

from pydantic import BaseModel
from typing import Optional
class StructureBase(BaseModel):
    text: str
    x: Optional[int] = 10
    y: Optional[int] = 120
    f_scaled: Optional[float] = 1

    # 以下兩個 functions 請盡可能不要更動-----------------------
    @classmethod
    def __get_validators__(cls):
        yield cls.validate_to_json

    @classmethod
    def validate_to_json(cls, value):
        if isinstance(value, str):
            return cls(**json.loads(value))
        return value

@app.post("/service_url_for_both/")
def service_name_for_both(data: StructureBase = Form(...), file: UploadFile = File(...)):
    input_dict = data
    input_image_bgr = bytes_to_cv2image(file)
    str_base64 = cv2image_to_base64(input_image_bgr)
    return {"data": input_dict, "img_base64": str_base64}

# 用uvicorn啟動FastAPI: 使用python3 main.py才會執行到以下
if __name__ == "__main__":
    port = int(os.environ.get("PORT", 5001))
    uvicorn.run(app, log_level='info', host='0.0.0.0', port=port)
```

- offline啟動FastAPI: 先行測試source code

```bash
PORT=5001 # 請輸入您為這個服務註冊的 Port
uvicorn main:app --host=0.0.0.0 --port=${PORT}
```

- offline測試FastAPI: 使用瀏覽器連至 **localhost:5001/docs2** ，offline測試你的服務
- FastAPI使用多worker: 安裝gunicorn套件

```bash
PORT=5001
WORKER=3
gunicorn main:app --workers ${WORKER} --threads 1 --worker-class uvicorn.workers.UvicornWorker --bind 0.0.0.0:${PORT}
```

- NodeRed測試FastAPI: request node前function

```javascript
// 單binary圖片檔
msg.headers = {"content-type" : 'multipart/form-data'};
msg.payload = {
    "file":{
        "value": msg.payload,
        "options":{
            "filename": "FILENAME.jpg"
        }
    }
};
return msg;

// 純dict(json)傳送
msg.headers = {"content-type" : 'application/json'};
msg.payload = {
    "text": "hello world",
    "x": 12,
    "y": 34
};
return msg;

// 多個簡單型態int, float, str, bool傳送
msg.headers = {"content-type" : 'application/json'};

var url_main_part = "http://localhost:port/service_url_for_simply_type/";
params = { 
    "str_obj": 'xxx', 
    "int_obj": 10, 
    "float_obj": 0.5
};

var queryString = Object.keys(params).map(key => key + '=' + params[key]).join('&');
msg.url = url_main_part   + "?" + queryString;
return msg;

// 同時傳送dict與binary照片
msg.headers = {"content-type" : 'multipart/form-data'};
msg.payload = {
    "data": {
        "text": "hello world",
        "x": 12,
        "y": 34
    },
    "file": {
        "value": msg.payload,
        "options":{
            "filename": "FILENAME.jpg"
        }
    }
}
```

- 鎖定執行序: 同時多個執行序呼叫神經網絡模型時，常有干擾的 race condition 問題產生，可使用lock機制避免

法一:(建議寫法)

```python
# 建立鎖
import threading
lock = threading.Lock()
# 使用鎖
with lock:
    # 你的inference process
    inference_function()
    print("需要 lock 執行的 code")
    ...

#縮排外自動解 lock
print("其他不需要 lock 執行的 code")
```

法二:

```python
# 建立鎖
import threading
lock = threading.Lock()

# 使用鎖
lock.acquire()
# 你的inference process (注意在此段code ，任何異常或者是跳出，都要記得解鎖)
print("需要 lock 執行的 code")
...
# release 後
lock.release()

# 解 lock執行的 code
print("其他不需要 lock 執行的 code")
```

- Q&A
	- AI365環境安裝套件時出現儲存空間不足error，有兩種方法清除緩存
		1. 刪除index緩存 `D:\Users\<NT帳號>\.PyCharmCE2018.3\system\index`
				2. 點選pycharm invalidate caches / restart 清除緩存
		- 如何知道自己開發環境的python package
		1. 在虛擬環境中 `pip freeze > requirements.txt`
				2. 確認核心package版本
		- NodeRed是否有Global變數可設定: 有支援Global變數
		1. 設定global variable: `global.set("name", value)`
			- `global.set("nfps", 3)`
						- `global.set("qscalev", 2)`
						- `global.set("frame_size", "640x480")`
				2. 取得global variable: `global.get("name")`
			- `global.get("nfps")`
						- `global.get("qscalev")`
						- `global.get("frame_size")`
		- 如何設定GPIO
		1. 在FastAPI內操控GPIO
				2. 在NodeRed內操控GPIO
			- 有GPIO Node可供使用
						- 但目前組裝環境沒有相對應硬體測試GPIO

## 2\. Requirment.txt for python package

- python版本: 3.6.9、3.7.3、3.8.10
- Requirement.txt: AUO pypi 審核

```bash
python-multipart
numpy
opencv-python
pandas
Pillow
fastapi
click==7.1.2
asgiref==3.4.1
uvicorn==0.16.0
gunicorn
```

- 目標平台: linux only
	- windows x86
		- linux x86(gitlab CI): 使Component可在組裝環境供大家拉flow使用
		- nx(手動build): 供部屬落地使用
		- nano(手動build): 供部屬落地使用
		- 樹梅派
- 難度高的套件
	- Darknet
		- 客製化 GPU 的 build src
		- Open CV GPU build
				- OpenPose

## 3\. Build Docker Image 教學(封裝一個 Service)

### Dockerfile

- 選擇初始image: 依自己的環境選擇合適的初始image, 範例: 使用python3.6

```dockerfile
FROM openaiacr.azurecr.io/python:3.6.9
# FROM openaiacr.azurecr.io/python:3.7.3
# FROM openaiacr.azurecr.io/python:3.8.10
# 目前python版本有: 3.6.9、3.7.3、3.8.10
```

- 建立app資料夾: 將你的app都放在此資料夾下(統一管理), 並將此資料夾預設為工作路徑

```dockerfile
WORKDIR /
RUN mkdir -p /app
WORKDIR /app
```

- 安裝系統套件: 有需要時安裝

```dockerfile
RUN apt-get install unzip
```

- 安裝python套件: 準備好你的python package

```dockerfile
# 將pip下載位置指向auo的pypi
ARG PIP_INDEX=http://autcbdpypi.corpnet.auo.com
ARG PIP_INDEX_URL=http://autcbdpypi.corpnet.auo.com/simple
ARG PIP_TRUSTED_HOST=autcbdpypi.corpnet.auo.com
# 開始執行安裝
COPY ./requirements.txt ./requirements.txt
# RUN pip install --upgrade pip # 如有套件要求更新pip再更新即可
RUN pip install --no-cache-dir --upgrade -r ./requirements.txt
```

- Source code: 專案程式碼

```dockerfile
# 你的程式碼
COPY ./yoursrc/ ./yoursrc/
# FastAPI的網頁程式: 不用動
COPY ./main.py ./main.py
COPY ./static/ ./static/
```

- 檔案下載: **weight檔** 、config檔、script檔、…

```dockerfile
COPY ./download_weight.sh ./download_weight.sh
RUN /bin/sh download_weight.sh

# RUN pip install ./install_package/xxxx.whl --no-index -f ./install_package
```

- 在最後一行啟動FastAPI服務

```dockerfile
ENV PORT 5001
CMD uvicorn main:app --host=0.0.0.0 --port=${PORT}
```

### 上傳權重檔

- 打包所有未上傳至gitlab的檔案: **weight檔** 、config檔，打包成一個app.tar檔(避免漏掉檔案且僅需上傳一個檔案)

```bash
# linux
tar cvf app.tar ./weight ./config.json

# window
# 右鍵選擇加入壓縮檔案，壓縮檔格式選擇tar
```

- 登入Azure選擇自己的blob storage，將app.tar上傳
- 上傳權重檔至 [Azure blob stroage](https://portal.azure.com/#home) (程式碼.py以外的所有檔案)
	- 登入Azure portal: [https://portal.azure.com/#home](https://portal.azure.com/#home) (NT帳號)
		- 搜尋 **itvideoaiappadls** (儲存體帳戶)
		- 點選容器(container)
		- 選擇 build-weight，依專案名稱新增目錄後上傳權重檔
		- 若是發現無法上傳，請依照以下的 mail title 與 寄件者寄出，會盡速幫您處理
		- mail title: \[Vision AI\] 平台申請權重檔上傳空間權限
				- mail to: ADTEB3
				- mail content:
			- 申請者的NT帳號: 請填自己的 nt 帳號
						- 申請廠區: 請填自己的廠區 \[L6A, L5A\]

### 修改download\_weight.sh

- 在 [Azure blob storage](https://portal.azure.com/#home) 查詢檔案的url
	- 點選上傳的 **app.tar** 檔
		- 複製 **Blob URL**
		- 將連結貼到download\_weight.sh檔中的URL字串，字串請用單引號包好

```bash
wget '<Blob URL>' -O - | tar -x
# wget '<package URL>' -O - | tar -x
```

### 修改.gitlab-ci.yml for CI

- [tcaigitlab2 範例](http://tcaigitlab2/videoapp/build-fastapi-sample-svc/-/blob/master/.gitlab-ci.yml)

```yaml
# This file is a template, and might need editing before it works on your project.
docker-build-master:
  # Official docker image.
  image: docker:latest
  variables:
    # 設定 docker 內的 azure container registry  環境，以供上傳
    DOCKER_TLS_CERTDIR: ""
    COMPONENT_DOCKER_TAG: "dev"    
    COMPONENT_ACR_PATH: "openaiacr.azurecr.io"
    COMPONENT_FOLDER: "auo-training"    
    # [重要更動區] 更動以下的 COMPONENT_NAME 請寫上您申請的 COMPONENT NAME 與 PORT ----------
    COMPONENT_NAME: "fastapi-sample-svc"
    COMPONENT_PORT: "5001"
    COMPONENT_EXPORT_PORT: "5001"
    #---------------------------------------------------------------------------    
    COMPONENT_DOCKER_NAME: "$COMPONENT_ACR_PATH/$COMPONENT_FOLDER/$COMPONENT_NAME"
    COMPONENT_DOCKER_FULLPATH: "$COMPONENT_DOCKER_NAME:$COMPONENT_DOCKER_TAG"
    
  stage: build

  services:
    - docker:dind
  before_script:
    - cat /etc/issue
    - mkdir ~/.docker && echo $DOCKER_AUTH_CONFIG > ~/.docker/config.json
  script:
    # 了解 dokcer 內的網路設定與所有環境變數
    - ifconfig
    - env | sort
    # platform x86 主要的程式碼
    - export COMPONENT_PLATFORM="x86" # current platform 
  
    # 依據每次 commit 或者是 tag 所屬的情況制定出正確的 Docker image tag 
    - if [ -z $CI_COMMIT_TAG ]; then export COMPONENT_DOCKER_FULLPATH=${COMPONENT_DOCKER_NAME}:${COMPONENT_DOCKER_TAG}-${COMPONENT_PLATFORM}.${CI_COMMIT_SHORT_SHA}; else export COMPONENT_DOCKER_FULLPATH=${COMPONENT_DOCKER_NAME}:${CI_COMMIT_TAG}-${COMPONENT_PLATFORM}; fi
    # 針對 git tag 的形式作版號的檢查
    - if [ ! -z $CI_COMMIT_TAG ] && [[ ! $CI_COMMIT_TAG =~ ^[0-9]+\.[0-9]+\.[0-9]+[a-z]* ]] ; then echo "TAG do not follow rule";exit 1; fi
    # 真正的 build x86 語法
    - docker build  --label gitlab_user=$DOCKER_ENV_GITLAB_USER_LOGIN -t $COMPONENT_DOCKER_FULLPATH .
    # 確認 build image 是否有成功
    - docker images
    - if [ $(docker images | grep -F "$COMPONENT_DOCKER_NAME" | wc -l) != 0 ]; then echo "OK" ; else  echo "Error to build $COMPONENT_DOCKER_FULLPATH"; exit 1; fi
    # 在此 CI runner 環境底下，嘗試 run 剛 build 好的 docker image 成為 container 
    - docker run -d -p $COMPONENT_EXPORT_PORT:$COMPONENT_PORT --name temp_container $COMPONENT_DOCKER_FULLPATH
    # 略為等候，以便能取到更多的 container 的 logs message 
    - sleep 15

    - docker ps -a  # 列出全部的 docker container 
    - docker ps # 列出 status running containers
    - if [ $(docker ps | grep -F "temp_container" | wc -l) != 0 ]; then echo "OK" ; else  echo "Error to run container!";exit 1; fi
    - docker logs temp_container
    # 測試完畢以後，略作清理 container
    - docker stop temp_container 
    - docker rm temp_container
    # 若為真正的 git tag trigger ，真正的 push 上去
    - if [ ! -z $CI_COMMIT_TAG ]; then docker push $COMPONENT_DOCKER_FULLPATH ; else echo "skip docker push"; fi
    - echo "$COMPONENT_DOCKER_FULLPATH complete!" 

  only:
    # - master # 每次 master commit 都會執行
    - tags # 每次tag 都會執行
    - web  # Web UI trigger 執行
```

### commit & push 程式碼

- 上傳程式碼至gitlab: commit and push
- 注意: 請勿將權重檔上傳至gitlab，僅供上傳.py /.txt /.md /.sh/.ini/.yaml 等檔

### 執行CI

- 使用 gitlab 內 CI/CD -> Run Pipeline -> Run Pipeline
- 確定 CI-Job 出現以下 log 類似訊息 ，表示 FastAPI 成功，若有任何啟動的Exception and Error 請嘗試排解，或與同好 and 輔導員聯繫。

```shell
$ docker logs temp_container
INFO:     Started server process [7]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:5001 (Press CTRL+C to quit)
```

- 在gitlab自己專案中，tag自己最新的版本(ex:0.0.1)，git會自動進行CI
- CI 成功後，自動將 docker image上傳到Azure ACR(AUO's dockerhub)
	- CI-Job 最後的 log 會出現 ***ACR 位置***

```
$ echo "ACR path = $COMPONENT_DOCKER_FULLPATH complete!"
ACR path = openaiacr.azurecr.io/auo-training/fastapideryann:3.0.3-x86 complete!
Job succeeded
```

### 啟動 Service

- 自助版：請連線到以下網址，可以利用Vision AI 啟動您想啟動的 ACR image 於您的組裝環境之中
	- [組裝環境啟動 service](http://tcai008/vision_ai/#!/2)
- 人助版：請給予以下資料可將 ACR 上的 service 與您的組裝環境部署在一起
	- Email to: (ADTEB3)
		- 信件 title: \[ Vision AI \] FastAPI Svc 打包完成，申請安裝至 NodeRED 組裝環境
		- 信件收件者: ADTEB3
		- nt 帳號
				- 可否只用 CPU 執行
				- ACR 位置
				- port 號

## 4\. 封裝一個NodeRED NODE

1. AI365內執行git clone node package範本: [http://tcaigitlab2/videoapp/node-red-contrib-auo-nodepackage](http://tcaigitlab2/videoapp/node-red-contrib-auo-nodepackage)
2. 進入自己帳號的Nodered組裝環境: [http://tcai008/user/](http://tcai008/user/) <NT帳號>
3. 建立subflow，並開始開發你的subflow
4. 匯出flows.json檔，並複製到git對應資料夾內
5. 將subflow說明覆蓋至subflow.md檔
6. 修改package.json檔內容
7. 執行git commit and push
8. 執行tag: 記錄node package版號，提供Node package GIT URL使用
9. 請至註冊 [Assembly platform -> 註冊 Component](http://tcai008/vision_ai/#!/1) 填寫註冊
10. 靜候管理人員完成背後匯入組裝環境等事宜(工作日約一日內)

## App建立與上架

## 1\. 確認組裝環境

- 進入自己帳號的Nodered組裝環境: [http://tcai008/user/](http://tcai008/user/) <NT帳號>
- 確認Nodered中有你的Node package

## 2\. 開始開發flow(拉flow)

- 設定頁面開發: 鏡頭url、app參數設定
- 主流程開發
- 報表頁面開發: 顯示結果報表
- 四種圖像源的測試階段
	- 照片: 請利用這個 ***File Inject*** node 做單一圖片的偵測
		- 影像檔案:
		- 請切割一個短影片
				- 交由輔導員上傳
				- 上傳後，組裝環境可於 /video/\[您的檔名\] 內 取得該檔案
				- 拉出 ***camera***
			- cam\_url 處可填入 /video/\[您的檔名\]
						- Read input at native frame rate
				- true (按照 fps 播)
								- false (全力解碼 往後方拋送 frame)
		- RTSP 模擬訊號 (optional)
		- 拉出 ***camera***
			- 請切割一個短影片
						- 交由輔導員設置模擬串流
						- 輔導員會提供模擬串流
						- cam\_url 處可填入 模擬串流的 RTSP 位置
		- IPCam 訊號:
		- 拉出 ***camera***
			- cam\_url 填入可 network 與 OA 該開發環境或者(部署目標處)網路能連結到的 IPCam
- Node範例查詢: [Node-RED Cookbook](https://cookbook.nodered.org/) ，常用案例
	- [Generate CSV output](https://cookbook.nodered.org/basic/generate-csv)
		- [Set the URL of a request](https://cookbook.nodered.org/http/set-request-url)
		- [Get binary response](https://cookbook.nodered.org/http/get-binary-response)
		- [課堂上 ROI 等 Sample Flow](https://hackmd.io/z1PtwQl7QmiqmWcNwLdU8w)

## 3\. App 開發申請、打包與上架

### App 開發申請 (取得AppID)

1. 申請App 開發 [AppDev](https://open-ai-web.azurewebsites.net/AppDev) 按下
	> [!danger] Danger
	> 離線時無法預覽。
	，填完表單後應可獲得 AppID
	- App應用場域分類:
		- 請於安全防疫、程序管理擇一，或與Angus/Shinna討論。
		- App 類別請選擇
		- EdgeApp
		- App 平展方式請選擇:
		- 下載安裝
		- 參數填寫範例:  
		> [!danger] Danger
		> 離線時無法預覽。
		- 取得 AppID
		- 回到 [AppDev](https://open-ai-web.azurewebsites.net/AppDev)
			- 按下您正開發的App 選項的編輯按鈕
				- > [!danger] Danger
					> 離線時無法預覽。
						- 可開啟 App Profile 內容就有您的 AppId 了
				- > [!danger] Danger
					> 離線時無法預覽。

### App 打包

2. 至 [打包頁面](http://tcai008/build/) 打包 App ，Opan AI網頁->Assembly Plotform->Build Edge App
	- 在組裝環境下載已開發好的flow
		- 打包頁面上傳flows.json檔，並填寫App相關資訊(AppID 請到1. 讀取)
		- 打包頁面選擇自己使用的Component，如發現選不到要的Component，請通知輔導員
		- 下載已打包好的 app.yaml 檔

### App 上架準備

3. 上架前準備
	- 可先至 App Profile 頁面 \[App附件管理\] 上傳您準備的圖
		- > [!danger] Danger
			> 離線時無法預覽。

### App 上架

4. 正式上架
	- 至 [Open AI](https://open-ai-web.azurewebsites.net/AppDev) 申請上架，Open AI網頁->Developer Zone->App Dev->APP開發管理頁籤
		- 點選您想處理 ***上架步驟*** 的App **編輯** 按鈕
				- 依序 reivew 所有 Profile 項目
			- 內文編輯
				- 在此頁面中，按下 \[ 編輯 \] 即可輸入 app 相關資料
								- 產品說明
								- 產品架構圖
								- NodeRED App flow (若具有隱匿性，也可 Email 給審查課ADTEB3)
								- Dashboard UI 介面
								- 豐富介紹您App的資料(optional)
					- 可以辨識的 **物件/mura 類型** 與 **sample 圖片**
										- 強力的介紹影片
										- 安裝部署的過程
										- 簡易設定過程的影片
				- 可按下 上架審核項目-> 儲存變更 -> 上架審核

## 4\. App 遠端部署

1. Email ADTEB3 提供輔導員以下資訊
	- - title: \[ Vision AI \] App 上架完成，申請匯入至 Gateway
		```
		​​​​​​​​* 您的 opena ai app link: ex: 
		​​​​​​​​* 您將要部署的 gateway: ex: l6a_asrs
		```
	- 輔導員會協助匯入您的 app 至 Gateway
2. 到 Gateway 查看，若有您的App，即可進行 **第一次部署、啟動App 的錄影**
	- ***很重要*** 錄影目的:
		- 證明您的 App 能在該平台部署後，能妥適的被使用者的安裝使用。須交由關主審核。
				- 可將此錄影放置於您的 App Store 頁面，更能讓使用者在還沒有安裝您產品之前，就能更適切的了解您的 App 產品
		- 步驟
		- Gateway 頁面
				- 準備 power point 的桌面錄影
				- 按下桌面錄影
				- 開始執行部署與 啟動您的App 設定、並使用

選擇 Repo