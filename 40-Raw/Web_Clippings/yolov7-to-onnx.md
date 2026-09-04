---
title: "yolov7-to-onnx"
source: "https://hackmd.io/ZuaEIouiSWG2QSXfIcPFxQ"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
## 外網測試

- 下載base image

```bash
docker pull python:3.9
```

- 進入container

```bash
docker run -d -it -v [權重檔資料夾]:[權重檔資料夾] --network host --name python3-onnx python:3.9 bash
docker exec -it python3-onnx bash
```

- 下載程式

```bash
apt update
apt install vim
git clone https://github.com/WongKinYiu/yolov7.git
mkdir model_hub
mv ./yolov7 ./model_hub
vim ./model_hub/yolov7/requirements.txt
```

- 修改requirements.txt中的ONNX套件，需要安裝

```bash
# Export --------------------------------------
# coremltools>=4.1  # CoreML export
onnx>=1.9.0  # ONNX export
onnx-simplifier>=0.3.6  # ONNX simplifier
# scikit-learn==0.19.2  # CoreML quantization
# tensorflow>=2.4.1  # TFLite export
# tensorflowjs>=3.9.0  # TF.js export
# openvino-dev  # OpenVINO export
```

- 安裝套件

```bash
pip install -r ./model_hub/yolov7/requirements.txt
pip uninstall opencv-python
pip install opencv-python-headless
pip install nvidia-pyindex
pip install onnx-graphsurgeon
```

- 準備權重檔

```bash
mkdir ./weights
cp /blobfuse/auo-to-ihealthcare/davidkylee/fab-guard/best.pt ./weights/
```

- 執行pt轉onnx

```bash
python3 ./model_hub/yolov7/export.py --weights ./weights/best.pt --grid --end2end --simplify --topk-all 100 --iou-thres 0.65 --conf-thres 0.35 --img-size 640 640 --max-wh 640
```

- 離開container

```bash
exit
```

- 複製onnx檔案從container到host主機

```bash
docker cp python39-onnx:/app/weights/best.onnx ./best.onnx
```

- 記錄環境

```bash
docker commit -m "add yolov7 and onnx package" -a "davidkylee" python39-onnx python:3.9-yolov7toonnx
docker image list | grep python
```

## Build image in DevOps(內網)

- 建立DevOps專案: [https://dev.azure.com/AUO-OpenAI/OpenAI-Web/\_git/yolov7\_to\_onnx](https://dev.azure.com/AUO-OpenAI/OpenAI-Web/_git/yolov7_to_onnx)
- git clone專案至maz-apcdev01: 後續在maz-apcdev01開發
- 放入Source code: yolov7 source code
- 調整requirements.txt

```bash
# Export --------------------------------------
# coremltools>=4.1  # CoreML export
onnx>=1.9.0  # ONNX export
onnx-simplifier>=0.3.6  # ONNX simplifier
# scikit-learn==0.19.2  # CoreML quantization
# tensorflow>=2.4.1  # TFLite export
# tensorflowjs>=3.9.0  # TF.js export
# openvino-dev  # OpenVINO export
```

- 串寫Dockerfile

```dockerfile
FROM python:3.9

ENV PYTHONUNBUFFERED 1
ENV TZ=Asia/Taipei
ENV ACCEPT_EULA=Y

RUN apt-get update && apt-get install ffmpeg libsm6 libxext6  -y

# 設定工作目錄
WORKDIR /app

# Copy source code to container
COPY ./yolov7/ /app/yolov7

# 安裝 Pythons dependencies
RUN pip install --no-cache-dir -r ./yolov7/requirements.txt
RUN pip install --no-cache-dir nvidia-pyindex
RUN pip install --no-cache-dir onnx-graphsurgeon

# 開放容器的 8000 端口供外界訪問
# EXPOSE 8000

# 非root用戶執行應用程式
# RUN adduser --disabled-password --gecos '' appuser
# RUN chown -R appuser /app
# USER appuser
```

- Commit and push 專案至DevOps
- 建立DevOps pipeline: [https://dev.azure.com/AUO-OpenAI/OpenAI-Web/\_build?definitionId=842](https://dev.azure.com/AUO-OpenAI/OpenAI-Web/_build?definitionId=842)  
	建立方式就是依樣畫葫蘆別人的， [https://dev.azure.com/AUO-OpenAI/OpenAI-Web/\_build?definitionId=711](https://dev.azure.com/AUO-OpenAI/OpenAI-Web/_build?definitionId=711)
- 建立後即可執行Build image，按下Run pipeline按鈕
- 如遇執行fail，DevOps的執行log相對完善，查看問題相對容易

選擇 Repo