---
title: "PHM維運"
source: "https://hackmd.io/70w3jowYSvqvEpZQ4yr0NQ"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
[主機](#%E4%B8%BB%E6%A9%9F)  
[網頁](#%E7%B6%B2%E9%A0%81)  
[重啟container](#%E9%87%8D%E5%95%9Fcontainer)  
[DB空間](#%E8%B3%87%E6%96%99%E5%BA%ABDB%E7%A9%BA%E9%96%93)  
[硬碟空間](#%E7%A1%AC%E7%A2%9F%E7%A9%BA%E9%96%93)

## 主機

## tcapcphmd01(10.96.18.68): online推論用主機，NT帳號登入，sudo權限需找正坤申請。

## tcapcphmd02(10.96.18.156): offline訓練用主機，NT帳號登入，sudo權限需找正坤申請。

## Monitor

## 網頁

- [PHM網頁](http://aaapc005:888/)
	- 帳密: NT帳號登入
		- 查看各廠 [整體健康度](http://aaapc005:888/online/onlineHome)
		- 查看某個專案 [健康度](http://aaapc005:888/online/onlineHome) ，輸入專案ID後，點擊Data Health，查看DB Last Time
- [tcapcphmd01's queues](http://tcapcphmd01:15672/#/queues)
	- 帳號: guest，密碼: guest
		- 觀察loacal.user\_upload數值是否正常running變化
		- 有變化: 程式正常執行
				- 無變化: 重啟container
- [Azure's queues](http://10.248.7.85/#/queues)
	- 帳號: phm，密碼: phmsa
		- 積queue時，需重啟地端的raw data result-consumer

## 重啟container

- tcapcphmd01(10.96.18.68): NT帳號登入，確認服務是否正常運行

```bash
sudo docker ps -a                # 請確認c2870822d1be、282211b41497正常運行
sudo docker restart c2870822d1be # RabbitMQ container server
sudo docker restart 282211b41497 # RabbitMQ + APScheduler，container正常不代表程式也正常運行，當發現queue都沒資料時，可能是排程器(APScheduler)服務掛掉了，需要重啟
```

- 查看 [CPU使用趨勢](http://10.97.82.60/zabbix/history.php?action=showgraph&itemids%5B%5D=107982) ，目前CPU在每小時第20分開始高負載，運行40分鐘後結束高負載，所以每小時有20分鐘是低負載
- 查看tcapcphmd01主機loading，此台機台有24 core，load average盡量落在24以下

```bash
top
```

- 再次觀察 [tcapcphmd01's queues](http://tcapcphmd01:15672/#/queues) ，loacal.user\_upload數值是否正常running變化
	- 有變化: 代表程式已在運作
		- 無變化: 重啟更多微服務container
- 重啟微服務

```bash
sudo docker restart f9a80b6c28c9 bcb63b197d25 35541e8699ae 9ffa458e7548 f3c4f77d3524 00564b9a30b9 2a4d918ada38 09fd6543fe4e 4fae38805152 74aef08c8238 6e76b40b91c0 aded8acbbce3 e07ca358ba64 c11662d4d2aa a50bf0a4c4ee 62471b5f7e57 1011f2734928 9ca59d6b1619 7671cec76e0f 26f1e9ac29b2 9bef66ea1926 ed887ae78005 f8d1d296b0a9 ecde4a5ceb57 9a4f339133cf 7003f4b89e3e b27dbe2c5a88
```

- 每小時第22分鐘會排程重啟online container，讓新訓練的模型可以開始推論

```bash
sudo crontab -e
```

```bash
ls -lt /home/PHM/phm_offline/poc
```

```bash
cd /home/PHM/venv/venv3.7/bin
python /home/PHM/phm_offline/poc/restart_phm_docker_container.py online
```

- 重啟raw data result-consumer，降低Azure's queues

## 資料庫DB空間

- 查看 [DB空間](http://tcapcphmd01:7000/system_function/dbcheck)
	- 安全儲存空間<80%，超過需執行DB資料刪除或搬移，已於tcapcphmd02排程每日10:30執行資料搬移
		- ONLINE\_JOB\_CHECK
				- ONLINE\_DATA
		```bash
		​​​​​​​​# 使用host環境進行
		​​​​​​​​/home/PHM/venv/venv3.7/bin/python /home/PHM/poc/backup_online_data.py # 備份log放在/home/PHM/poc/backup_online_data.log
		​​​​​​​​# 使用container環境進行，目前沒用container執行，改用host環境進行
		​​​​​​​​sudo docker exec c125c70eb00d /bin/bash -c "cd /web_app/poc && python3 backup_online_data.py
		```
- 網頁開不起來需重啟主程式

```bash
sudo docker restart 3c229a6bf4c2
```

## 硬碟空間

- 查看空間，主要是tcapcphmd01空間不夠，以下都是查看 [tcapcphmd01主機空間](http://10.97.82.60/zabbix/history.php?action=showgraph&itemids%5B%5D=108020)

```bash
df -h
```

- 刪除log，已設定排程執行

```bash
# 先列出前20占空間的
sudo du -a /var/lib/docker/containers | sort -n -r | head -n 20  
# 刪除大的log(只能刪除log!!!)
sudo truncate -s 0 /var/lib/docker/containers/3c229a6bf4c224471e500473929664e6c739723cc978f9127ce8006421cd993a/3c229a6bf4c224471e500473929664e6c739723cc978f9127ce8006421cd993a-json.log 
# 在此路徑下>1.3GB的log都刪除，已建立每日排程crontab -e
sudo find /var/lib/docker/containers -type f -name "*.log" -size "+1500000000c" -exec /usr/bin/truncate --size=0 {} \;
```

```bash
sudo crontab -e
```

- 刪除temp，仍需手動執行

```bash
# 看現在佔最大的前30個路徑
sudo du -a /var/lib/docker/overlay2 | sort -n -r | head -n 30
# 先看下面的第一個 temp
sudo du -a /var/lib/docker/overlay2/c35e5553ff5e129095e4986cdc33906b430c290afadc0035f2bb8a6a7a53ae92| sort -n -r | head -n 30
# 刪除temp
sudo rm -rf /var/lib/docker/overlay2/22b7662dc8e2dbcc40bf86ee67769f62280f0cb782f404f45f45e37af4913b82/merged/web_app/services/rawdata_customer/async_services/temp
```

選擇 Repo