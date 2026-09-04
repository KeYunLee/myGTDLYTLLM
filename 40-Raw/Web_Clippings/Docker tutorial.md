---
title: "Docker tutorial"
source: "https://hackmd.io/_3DV_kAxSU6_GJ-5FpgFUw"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
###### tags: 基礎

[Docker 筆記 Part 2 ｜指令操作](https://visonli.medium.com/docker-%E5%85%A5%E9%96%80-%E7%AD%86%E8%A8%98-part-2-91e4dfa2b365)  
[Docker – Dockerfile 指令教學，含範例解說](https://www.jinnsblog.com/2018/12/docker-dockerfile-guide.html)  
[了解 CMD 與 ENTRYPOINT](https://ithelp.ithome.com.tw/articles/10250988)

## Dockerfile

- 基本case

```dockerfile
FROM nvcr.io/nvidia/tensorrt:21.07-py3
MAINTAINER davidkylee

COPY ./install_package/ /app/install_package/
WORKDIR /app/

RUN ls /install_package/
RUN pip3 install cython matplotlib pandas pydot scikit-learn --no-index -f /app/install_package/
RUN pip3 install tensorflow==2.2.2 --no-index -f /app/install_package/
RUN pip3 install opencv-python --no-index -f /app/install_package/
RUN pip3 install albumentations --no-index -f /app/install_package/
RUN pip3 install tqdm requests flask gunicorn --no-index -f /app/install_package/
```

- 進階case

```dockerfile
# main stage
FROM --platform=$TARGETPLATFORM python:3.6.9-slim AS main

# mount wheel資料夾至container內, 安裝apt套件並把暫存cache刪除
ARG DEBIAN_FRONTEND=noninteractive
RUN --mount=type=bind,source=/wheel,target=/mnt/pypi \
    --mount=type=cache,target=/var/cache/apt \
    --mount=type=cache,target=/var/lib/apt \
    apt-get update && apt-get install -y --no-install-recommends zip unzip htop screen libgl1-mesa-glx libgl1 libglib2.0-0 libegl1

# python packages
RUN pip3 install --upgrade pip
RUN --mount=type=bind,source=/wheel,target=/mnt/pypi \
    --mount=type=bind,source=/requirements.txt,target=/mnt/requirements.txt \
    pip3 install --no-cache-dir -r /mnt/requirements.txt -f /mnt/pypi/
RUN --mount=type=bind,source=/wheel,target=/mnt/pypi \
    --mount=type=bind,source=/requirements_offline.txt,target=/mnt/requirements_offline.txt \
    pip3 install --no-cache-dir -r /mnt/requirements_offline.txt -f /mnt/pypi/ --no-index
RUN echo "/usr/lib/python3.6/dist-packages" > /usr/local/lib/python3.6/site-packages/tensorrt.pth

# app
ARG TARGETARCH
ADD app.tar /app
ADD yolov7_weight.tar /app
# COPY weight/$TARGETARCH /app/weight/device/

# cuda
RUN echo "/usr/lib/\`arch\`-linux-gnu/tegra" >> /etc/ld.so.conf.d/nvidia-tegra.conf && \
    echo "/usr/local/cuda-10.2/targets/\`arch\`-linux/lib" >> /etc/ld.so.conf.d/nvidia.conf && \
    echo "/usr/local/cuda-11.1/targets/\`arch\`-linux/lib" >> /etc/ld.so.conf.d/nvidia.conf && \
    ldconfig

WORKDIR /app
ENV PYTHONPATH="/app/"
ENV LC_ALL="C.UTF-8" LANG="C.UTF-8"
# ENV FLASK_APP="website.Flask_Server_all"
# ENV FLASK_RUN_PORT=5020
ENV NVIDIA_DRIVER_CAPABILITIES=compute,utility
ENV NVIDIA_VISIBLE_DEVICES=all

CMD ["sh", "-c", "gunicorn svc:app --workers ${GUNICORN_W:-1} --threads 1 --worker-class uvicorn.workers.UvicornWorker --bind 0.0.0.0:${PORT:-5109}"]
# gunicorn svc:app --workers 4 --threads 1 --worker-class uvicorn.workers.UvicornWorker --bind 0.0.0.0:5109
```

---

## build image

- 當下平台

```bash
docker build -f Dockerfile -t simple-flask-app:latest .
```

- 指定平台

```bash
docker buildx build -f ReID_AdaptiveL2/Dockerfile -t openaiacr.azurecr.io/auo/reid-svc:0.0.1-x86  --platform amd64 --load .
```

```bash
docker buildx build -t openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0-nx --platform arm64 --load . --build-arg http_proxy=http://<account>:<password>@auhqwsg:8080 --build-arg https_proxy=http://<account>:<password>@auhqwsg:8080 --no-cache
```

- 設定proxy

```bash
docker buildx build -f ReID_AdaptiveL2/Dockerfile -t openaiacr.azurecr.io/auo/reid-svc:0.0.1-x86  --platform amd64 --load . --build-arg http_proxy=http://username:password@auhqwsg:8080 --build-arg https_proxy=http://username:password@auhqwsg:8080
```

| parameter | value | 說明 |
| --- | --- | --- |
| \-f | Dockerfile | 指定你的Dockerfile |
| \-t | imagename:tag | 命名你的image與tag |
| –platform | amd64 or arm64 | 指定你的Docker環境 |
| –load | local\_workdir | 本機工作路徑 |
| –build-arg | ENV\_PATH=xxxx | 設定BUILD時的環境變數 |
| –no-cache |  | build時不使用cache資料 |

---

## docker login

```bash
# 登入
docker login openaiacr.azurecr.io
# 指定user, 換其他user時使用
docker login -u openaiacr openaiacr.azurecr.io
```

---

## docker pull

- 基本用法

```bash
docker pull openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0
# 如image非manifest, 則直接pull image
# 如image為manifest, 則依作業系統pull對應image
```

- 指定platform: 指定後, 地端tag會覆蓋舊的platform

```bash
# 指定platform為amd64
docker pull --platform=linux/amd64 openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0
# 指定platform為arm64
docker pull --platform=linux/arm64 openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0
# 如依序執行amd64與arm64, 最後tag openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0為arm64的image
```

---

## docker inspect

- 查看image細節

```bash
docker inspect openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0
```

---

## docker push

```bash
docker push openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0-nx
```

## docker manifest

Push上hub後在執行

- rm

```bash
docker manifest rm openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0
```

- create

```bash
docker manifest create openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0 openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0-x86 openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0-nx
```

- push

```bash
docker manifest push openaiacr.azurecr.io/auo/fall-detection-flow:2.1.0
```

## run container

- 確認image

```bash
docker image list
docker image ls
```

- 正常非背景執行: 有加–rm，故exit後會container會直接消失

```bash
docker run --gpus all -it --rm -v /home/nfs:/home/nfs -v /home/davidkylee/.cache:/root/.cache nvcr.io/nvidia/tensorrt:20.11-py3-iHealthcare2-ffmpeg
```

- 正常非背景執行: 沒加–rm，exit後可再開啟，–name後接你想要的container name

```bash
docker run --gpus all -it -v /home/nfs:/home/nfs -v /home/davidkylee/.cache:/root/.cache --name davidkylee nvcr.io/nvidia/tensorrt:20.11-py3-iHealthcare2-ffmpeg
```

- 背景執行: 加-d變成背景執行

```bash
docker run --gpus all -it -d -v /home/nfs:/home/nfs -v /home/davidkylee/.cache:/root/.cache --name davidkylee nvcr.io/nvidia/tensorrt:20.11-py3-iHealthcare2-ffmpeg
```

- docker run 參數介紹

| parameter | value | 說明 |
| --- | --- | --- |
| \-d |  | 背景執行, 不可與–rm同時使用 |
| \-it |  | 如果沒用-d背景執行則必須下此參數 |
| –rm |  | 離開container後, container直接關閉, 不建議使用 |
| –gpus | all or 'device=0' | 使用GPU, 可指定GPU |
| –runtime | nvidia | 使用GPU |
| –name | your\_name | 管理container名稱 |
| –network | host | container使用主機網路,此指令使-p失效 |
| \-v | local\_dir:container\_dir | mount本地資料夾 |
| \-p | local\_port:container\_port | 主機與container的port mapping |
| \-e | ENV\_PATH=xxxx | 設定container環境變數 |
| –log-opt | max-size=10m | 限制log檔的大小 |
| –log-opt | max-file=9 | 限制log檔的數量 |
| –device | /dev/video0 | 使container看的到device |
| –privileged |  | 給docker root權限 |

- 查看container有哪些，加-a則連未啟動的container都會list出來

```bash
docker ps -a
```

- 將背景執行container變前景

```bash
docker exec -ti [id or name] bash
```

- 如果container在背景，但未啟動

```bash
docker start [id or name]
```

- 把container暫停

```bash
docker stop [id or name]
```

- 把container刪除，注意需先暫停後才能刪除

```bash
docker rm [id or name]
```

---

## commit image

```bash
docker ps -a # or docker container list
docker commit -m "add package for run openpifpaf" -a "davidkylee" [somecontainerid] nvcr.io/nvidia/tensorrt:20.11-py3-openpifpaf
```

---

## copy file

- user to container

```bash
docker cp [file path] [container]:[file path in container]
```

- container to user

```bash
docker cp [container]:[file path in container] [file path]
```

---

## save image

[如何備份 Docker Image 為 tar 檔](https://ithelp.ithome.com.tw/articles/10191387)  
[把image另外儲存成檔案](https://peihsinsu.gitbooks.io/docker-note-book/content/docker-save-image.html)

```bash
docker save --output myimage.latest.tar myimage:latest
```

---

## load image

```bash
docker load -i myimage.latest.tar
```

---

## remove image

```bash
docker rmi xxxxxxx:xxxxxx
```

---

## rename image

```bash
docker tag aaaa:aaaa bbbb:bbbb
docker rmi aaaa:aaaa
```

---

## docker 安裝arm模擬器

```bash
docker pull tonistiigi/binfmt
sudo docker run --privileged --rm tonistiigi/binfmt --uninstall arm64
sudo docker run --privileged --rm tonistiigi/binfmt --install arm64
docker buildx ls
```

---

## clean docker stroage

[清理Docker垃圾](https://www.796t.com/article.php?id=350138)

- 查詢docker空間狀況

```bash
docker system df
```

- docker system prune: 可以用於清理磁碟，刪除關閉的容器、無用的資料卷和網路，以及dangling映象(即無tag的映象)

```bash
docker system prune
```

- docker system prune -a: 清理得更加徹底，可以將沒有容器使用Docker映象都刪掉。注意，這兩個命令會把你暫時關閉的容器，以及暫時沒有用到的Docker映象都刪掉了…所以使用之前一定要想清楚額

```bash
docker system prune -a
```

選擇 Repo