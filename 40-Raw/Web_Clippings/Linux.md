---
title: "Linux"
source: "https://hackmd.io/q9GmShBaQU6Q1kNUKjrxQw"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
###### tags: 基礎

---

[Debian與Ubuntu](https://www.linuxadictos.com/zh-TW/debian-vs-ubuntu.html?_gl=1%2A14hjtsx%2A_ga%2Ayw1wluf3nlnuqnhaoekxbmtcmjnxlvcym1hwynnesvrmwdzsqw5wnfhnzunremhqznpjc21tuursuhnqq29lq0zism4%3F_gl%3D1%2A14hjtsx%2A_ga%2Ayw1wluf3nlnuqnhaoekxbmtcmjnxlvcym1hwynnesvrmwdzsqw5wnfhnzunremhqznpjc21tuursuhnqq29lq0zism4)

## 搜尋系統套件

## debian

[debian 查看軟體套件列表](https://www.debian.org/distrib/packages)

---

## 關機

[Linux 關機指令（shutdown、halt 與 poweroff）教學與範例](https://blog.gtwang.org/linux/how-to-shutdown-linux/)

```bash
shutdown -h now
```

---

## 切換使用者

[Ubuntu Linux 設定 sudo 使用者、群組教學與範例](https://officeguide.cc/ubuntu-linux-sudo-sudoer-user-group-configuration-tutorial-examples/)

## 切換root

```bash
sudo su -
```

## 環境變數

## 呼叫變數

```bash
abc=123
# 顯示abc變數內容
echo ${abc}
# 顯是def變數內容, 無定義def時, 用1代替
echo ${def:-1}
```

## 查看環境變數

```bash
env
```

## 查看PYTHONPATH

```bash
python3 -m site
python3 -> import sys -> sys.path
```

## 指定PYTHONPATH

- PYTHONPATH的判斷

> PYTHONPATH設定為package中import路徑匹配的根目錄

```bash
python3 folder1/xxxxx.py # PYTHONPATH為"folder1"，所以如果xxxxx.py中的import路徑為folder1.yyyyy.method，會報錯，需為yyyyy.method才能正常import
python3 -m folder1.xxxxx # PYTHONPATH為"執行當下PWD"，所以如果不在module所在資料夾時，需先export PYTHONPATH，module內部的import皆需統一從PYTHONPATH開始呼叫
```

- script in linux

```bash
export PYTHONPATH=/usr/local/lib/python3.4/dist-packages/:~/lib/python3.4/site-packages/:/home/nfs/user/davidkylee/PycharmProjects/ObjectDetection/lib/object_detection.zip:/home/nfs/user/davidkylee/PycharmProjects/ObjectDetection/lib/slim.zip
export LD_LIBRARY_PATH=/usr/lib/oracle/11.2/client64/lib/:/usr/local/cuda/lib64/:/usr/local/cuda/extras/CUPTI/lib64/
export PYTHONPATH=/usr/local/lib/python3.4/dist-packages/:~/lib/python3.4/site-packages/:~/tensorflow/models:~/tensorflow/models/slim
export LD_LIBRARY_PATH=/usr/lib/oracle/11.2/client64/lib/:/usr/local/cuda/lib64/:/usr/local/cuda 
export PYTHONPATH=<ProjectPKpath>:$PYTHONPATH

env PYTHONPATH=<ProjectPKpath>:$PYTHONPATH python3 -m site
env PYTHONPATH=<ProjectPKpath>:$PYTHONPATH CUDA_VISIBLE_DEVICES=1 python3 -m site

gedit ~/.bashrc
source ~/.bashrc
```

- script in window

```bash
setx path "%path%;C:\your\path\here\"
```

- python

```python
import sys
# 盡量使用絕對路徑
sys.path.insert(0, '/home/nfs/user/davidkylee/PycharmProjects/beseye/src')
# 以pwd位置當相對路徑起點，如果在其他工作目錄執行script會有問題
sys.path.insert(0, 'People-Counting-in-Real-Time-master_0.0.3.zip')
# 以python script檔位置當相對路徑起點，是比較好的寫法
sys.path.insert(0, os.path.join(os.path.dirname(__file__),'People-Counting-in-Real-Time-master_0.0.3.zip'))
```

## 各種引號功能介紹

[linux shell中單引號、雙引號、反引號、反斜槓的區別](https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/393854/)

- 無引號: 執行變數替換與檔名替換

```bash
x=*
echo $x
# output
hello.sh menus.sh misc.sh phonebook tshift.sh
```

- 單引號: 不執行變數替換與檔名替換

```bash
x=*
echo '$x'
# output
$x
```

- 雙引號: 執行變數替換但不執行檔名替換

> 這個例子可以看出無引號、單引號和雙引號之間的區別。在最後一種情況中，雙引號告訴shell在引號內照樣進行變數名替換，所以shell把$x替換為＊，因為雙引號中不做檔名替換，所以就把＊作為要顯示的值傳遞給echo。 對於第一種情況需要進一步說明，shell在給變數賦值時不進行檔名替換

```bash
x=*
echo "$x"
# output
*
```

- 反引號: 與$()功能類似

```bash
echo The date and time is \`date\`
# output
The date and time is 三 6月 15 06:10:35 CST 2005
```

- $(): 與反引號功能類似

```bash
echo Your current working directory is $(pwd)
# output
Your current working directory is /home/howard/script
```

- 反斜槓: 逃脫字元

```bash
echo -e "\n"
```

## 指定cuda GPU

```bash
env CUDA_VISIBLE_DEVICES=0,1 python3 xxxx.py
```

## 自動增長GPU記憶體用量

[TensorFlow 與 Keras 指定 NVIDIA GPU 顯示卡與記憶體用量教學](https://blog.gtwang.org/programming/tensorflow-keras-specify-gpu-and-memory-tutorial/)

- tensorflow

```python
import tensorflow as tf
W = tf.constant([1.0, 2.0, 3.0, 4.0], shape=[2, 2], name='W')
x = tf.constant([1.3, 2.4], shape=[2, 1], name='x')
y = tf.matmul(W, x)

# 自動增長 GPU 記憶體用量
gpu_options = tf.GPUOptions(allow_growth=True)
sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options))
print(sess.run(y))
```

- keras

```python
import tensorflow as tf

# 自動增長 GPU 記憶體用量
gpu_options = tf.GPUOptions(allow_growth=True)
sess = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options))

# 設定 Keras 使用的 Session
tf.keras.backend.set_session(sess)

# 使用 Keras 建立模型
# ...
```

## cuda版本

- 查看

```bash
cat  /usr/local/cuda/version.txt
nvcc --version
```

- 指定

```bash
export PATH=/usr/local/cuda-10.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64:$LD_LIBRARY_PATH
```

## cudnn版本

```bash
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
```

---

## 串接指令

[Linux 下的 & 及 | 的用法](https://www.ltsplus.com/linux/linux-and-andand)  
[Linux I/O 輸入與輸出重新導向，基礎概念教學](https://blog.gtwang.org/linux/linux-io-input-output-redirection-operators/)

- 標準輸出: 將ls存入output.txt，如output.txt已存在，會覆蓋原檔案，如指令執行錯誤不會存入檔案，一樣只會呈現在終端機上

```bash
ls > output.txt
```

- 標準輸出Append: 將date存入output.txt，如output.txt已存在，會Append至檔案內容最下端

```bash
date >> output.txt
```

- 錯誤輸出: 將錯誤輸出到error.txt檔案內，標準輸出仍在output.txt

```bash
ls non_exist > output.txt 2> error.txt
ls non_exist > output.txt 2>> error.txt
```

- 標準與錯誤輸出同檔案: 幾種方法都可

```bash
ls non_exist 2> output.txt 1>&2
ls non_exist &> output.txt
ls non_exist >& output.txt
```

- 標準輸入: 將input.txt丟給cat做呈現

```bash
cat < input.txt
```

- |: 水管pipeline

```bash
ls | grep keyword | nl | head -n 5
```

- &: 背景運行

```bash
python3 main.py &
python3 main.py &> xxx.log &
```

- &&: 分開兩個指令, 即第一道指令執行成功後, 才會執行第二道指令

```bash
make && make install
```

- ||: 表示第一道指令執行失敗後, 才會執行第二道

---

## IDE

## 開pycharm

```bash
pycharm &
```

## 開jupyter notebook

```bash
jupyter notebook --no-browser --ip tcai003
jupyter notebook --no-browser --ip xx.xx.xx.xx
```

---

## virtualenv

[Python虛擬環境virtualenv的安裝與使用詳解](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/365510/)  
[搞懂Python的virtualenv](https://ithelp.ithome.com.tw/articles/10199980)  
[如何在終端機下使用 conda 指令管理虛擬環境](https://www.happycoder.org/2020/04/23/how-to-use-conda-to-manage-python-virtual-environment/)  
[Python3 - Virtualenv & Virtualenvwrapper 拯救骯髒的套件環境](https://medium.com/@minecola/python3-%E6%8B%AF%E6%95%91%E9%AA%AF%E9%AB%92%E7%92%B0%E5%A2%83%E5%A4%A7%E4%BD%9C%E6%88%B0-with-virtualenv-4b398142463d)  
[linux上python虚拟环境迁移方法](https://zhuanlan.zhihu.com/p/134306869)  
[用conda建立及管理python虛擬環境](https://medium.com/python4u/%E7%94%A8conda%E5%BB%BA%E7%AB%8B%E5%8F%8A%E7%AE%A1%E7%90%86python%E8%99%9B%E6%93%AC%E7%92%B0%E5%A2%83-b61fd2a76566)  
[DAY03-搞懂Python的virtualenv](https://ithelp.ithome.com.tw/articles/10199980)

## 查看虛擬環境

### virtualenvwrapper

```bash
mkdir dir_Envs(你想儲存環境的路徑)

vim ~/.zshrc or vim ~/.bashrc
    export WORKON_HOME=dir_Envs
    export VIRTUALENVWRAPPER_PYTHON=(python3 的所在位置, 可以用 which python3 來查詢)

source ~/.zshrc or source ~/.bashrc
source /usr/local/bin/virtualenvwrapper.sh

workon
lsvirtualenv
```

### conda

```bash
conda env list
```

## 建立虛擬環境

### venv

```bash
cd my_project_folder
virtualenv venv_name
virtualenv -p /usr/local/bin/python3 venv_name
virtualenv --no-site-packages venv_name
```

### virtualenvwrapper

```bash
mkvirtualenv venv_name
```

### conda

```bash
conda create --name venv_name python=3.6
```

## 啟動虛擬環境

### venv

- linux

```bash
cd my_project_folder/venv_name
source ./bin/activate
```

- windows

```bash
.\venv\Scripts\activate.bat
```

### virtualenvwrapper

```bash
workon venv_name
```

### conda

```bash
conda activate venv_name
source activate venv_name
```

## 退出虛擬環境

### venv or virtualenvwrapper

```bash
deactivate
```

### conda

```bash
conda deactivate
source deactivate
```

## 刪除虛擬環境或package

### venv

```bash
rm -R venv_name
```

### virtualenvwrapper

```bash
rmvirtualenv venv_name
```

### conda

```bash
conda remove --name venv_name numpy
conda env remove --name venv_name
conda clean -a
```

---

## 查看系統

## 查看GPU指令

```bash
nvidia-smi -l 10
```

## 查看目前process

```bash
ps xf -U davidkylee -O lstart
ps uxf
```

## 查詢系統上各process所開啟的檔案

[Linux 列出行程開啟的檔案，lsof 指令用法教學與範例](https://blog.gtwang.org/linux/linux-lsof-command-list-open-files-tutorial-examples/)

- 列出process所開啟的檔案

```bash
lsof -p process_pid
```

- 列出開啟指定檔案的process

```bash
lsof file
```

## 查看目前系統loading

```bash
top
```

## 查看ip

### linux

```bash
ifconfig
```

### window

```bash
ipconfig
```

## 排程設定

[Linux 設定 crontab 例行性工作排程教學與範例](https://blog.gtwang.org/linux/linux-crontab-cron-job-tutorial-and-examples/)

```bash
crontab -l
crontab -e
```

內容填寫, 定時執行script

```bash
*/5 * * * * bash /home/proj/auomaskdetection/script/run_ftp_deploy.sh
0 * * * * /home/others/Desktop/clean_rtsp.sh
*/10 * * * * /home/others/Roger/AiPrint/AiHttpServer.sh
```

---

## 資料夾/檔操作

## 查看資料夾

```bash
ls
ll
dir
```

## 查看資料夾大小

[Linux常用指令-du-檢視檔案與目錄使用空間](http://it.givemepower.net/linux-command-du-view-files-and-directories-usage-space/)

```bash
du -sh .
time du -csh user/davidkylee/image/L6A/*/*
time du -sh ./*
du -sh * | sort -rh

du -sh * &> space-20251127.log
cat space-20251127.log | grep -v "Permission denied" | sort -rh
```

## 移動資料

```bash
mv /home/davidkylee/PycharmProjects/Autoencoder_graph_search /home/nfs/proj
```

## 修改權限

```bash
chmod +x xxx.sh
chmod -R g+w xxx/xxx/
chmod -R g+r xxx/xxx/
chmod -R a+w xxx/xxx/
```

## 更名

```bash
rename .JPG .jpg *.JPG
```

## 壓縮檔案

[GNU / Linux 各種壓縮與解壓縮指令](http://note.drx.tw/2008/04/command.html)  
[Linux tar/gzip 檔案壓縮與解壓縮、split/cat檔案分割與合併的實務應用](https://www.jinnsblog.com/2018/03/linux-tar-and-split-cat-example.html)  
[tar壓縮與分割大檔案](https://www.itread01.com/content/1549976943.html)

- tar

```bash
# 壓縮
tar cvf FileName.tar DirName
# 檔案切割
split -d -b 100MB FileName.tar FileName.tar_
# 壓縮與切割一起做, 其中 - 不能省略, 那是tar的ouput和split的input的引數
tar cvf - DirName | split -d -b 5m - FileName.tar
# 解壓縮
tar xvf FileName.tar
```

- zip

```bash
# 將 data 目錄下所有檔案壓儲到 file.zip, 指令無須加上 .zip 副檔名, zip 會自動補上
time zip file data/*
# 將 data 目錄下所有檔案及副目錄壓儲到 file.zip
time zip -r file data/*
```

- gzip

```bash
# 壓縮
gzip -k install.sh
# 解壓縮
gzip -d FileName.gz
gzip -dc install.sh.gz | bash
```

## 搜尋檔案

[Unix/Linux 的 find 指令使用教學、技巧與範例整理](https://blog.gtwang.org/linux/unix-linux-find-command-examples/)  
[計算資料夾下的檔案數](https://cynthiachuang.github.io/Count-Number-of-Files-in-Linux/)

- 參數介紹

| 參數 | 輸入 | 說明 |
| --- | --- | --- |
| \-type | d or f | d:目錄 f:一般檔案 |
| \-name | "\*.mp3" | 指定檔名搜尋 |
| \-exec | rm -f {} \\ | 搜尋的檔案執行輸入命令 |
| \-mtime | n | 指定檔案的最後修改時間(天)（modification time） |
| \-mmin | n | 指定檔案的最後修改時間(分) |
| \-atime | n | 指定檔案的最後存取時間(天)（access time） |
| \-amin | n | 指定檔案的最後存取時間(分) |
| \-ctime | n | 指定檔案狀態相關資訊最後修改的時間(天)（status time） |
| \-cmin | n | 指定檔案狀態相關資訊最後修改的時間(分) |

- 搜尋附檔名為mp4的檔案

```bash
find . -name "*.mp3"
```

- 查看檔案數量

```bash
find . -type f | wc -l
```

- 刪除時間超過一天檔案

```bash
find <target_dir> -mtime +1 -type f -exec rm -rf {} \;
```

- 刪除時間超過一天檔案, 不超過三天檔案

```bash
find <target_dir> -mtime +1 -mtime -3 -type f -exec rm -rf {} \;
```

---

## 資料內容操作

## sh執行檔開頭

```bash
#!/bin/bash
```

## 查看log檔

```bash
tail -f /home/nfs/user/davidkylee/PycharmProjects/Inception/log/L6A_PI_Defect_Judge_9class_inc_v4_active_learn_round2_F3T4R0.log
tail -f ../log/L7B_tcai004_SSD_Fine2_2500epoch.log
```

## grep應用

### 查看檔案並修改檔案後排序

```bash
grep ', ' ./bbbb.csv | sed -e 's/, /,/g' | cut -d ' ' -f 3 | sort | uniq -c
grep ', ' ./bbbb.csv | sed -e 's/^\([^ ]* \)\{2\}//' -e 's/\].*/\]/' | sort | uniq -c > ./cccc.csv
grep ', ' ./bbbb.csv | sed -e 's/, /,/g' | awk '{print $3}' | sort | uniq -c
grep ', ' ./bbbb.csv | sed -e 's/, /,/' -e 's/^\([^ ]* \)\{2\}\([^ ]*\).*/\2/' | head
cat ./bbbb.csv | sed -e 's/, /,/g' | cut -d ' ' -f 3 | sort | uniq -c | sed -e 's/\[//' -e 's/\]//'
```

### 複製查詢出來的檔案到指定資料夾

```bash
ls /home/ftp/example_10pertype/png/*/*_1_L0*0* | grep -v OK | xargs -i cp {} NG_test/
```

## xargs應用

### 查詢檔案後對每個檔案執行script

```bash
ls data/video/ML5CB2/train/*.avi | xargs -i python3 tools/to_mp4.py --video_path {}
```

### 刪除final best last以外的weight檔

```bash
ls | grep -v '_final.\|_best.\|_last.' | xargs -i rm
rm !(*_final.*|*_last.*|*_best.*)
```

## 篩選文件列

```bash
grep
```

## 進階功能

```bash
awk
```

## 計算列數

```bash
wc -l count.csv
```

## 查看xml檔

```bash
cat file.xml
```

## 修改xml檔

```bash
time sed -i -e 's/defect_bndbox/bndbox/' -e 's/defect_label/name/g' /home/nfs/user/davidkylee/image/L6A/Object_Detection/PI/20180419/xml/*.xml
time sed -i -e 's#</bndbox>#&\n</object>\n<object>#' /home/nfs/user/davidkylee/image/L6A/Object_Detection/PI/20180419/xml/*.xml
time sed -i -e '{/<object>/N;s#<object>\n</object>##}' /home/nfs/user/davidkylee/image/L6A/Object_Detection/PI/20180419/xml/*.xml
time sed -i -e '{s/defect_bndbox/bndbox/; /<\/bndbox>/N; s#\(</bndbox>.*\n\)\(.*<defect_label\)#\1  </object>\n  <object>\n\2#; s/defect_label/name/g}' /home/nfs/user/davidkylee/image/L6A/Object_Detection/PI/20180419/xml/*.xml
```

## 批次修改xml檔

```bash
time for f in /home/nfs/user/davidkylee/image/L6A/Object_Detection/PI/20180419/xml/*.xml; do (head -n -3 $f; echo '</annotation>') > tmp; cp tmp $f; done
```

## 查看二進制內容

```bash
od -x xxx.pth | head
```

## 解壓縮

[GNU / Linux 各種壓縮與解壓縮指令](http://note.drx.tw/2008/04/command.html)

- tar

```bash
# 打包
tar cvf FileName.tar DirName
tar zcvf FileName.tar.gz DirName
# 解包
tar xvf FileName.tar
tar zxvf FileName.tar.gz
# 查看
tar tvf FileName.tar
```

- gz

```bash
# 打包
gzip -k FileName
# 解包
gzip -d FileName.gz
gzip -dc FileName.gz
```

- zip

```bash
# 打包
zip -r FileName.zip DirName
# 解包
unzip FileName.zip
```

- rar

```bash
# 打包
rar a FileName.rar DirName
# 解包
rar e FileName.rar
```

- 7z

```bash
# 打包
7z a FileName.7z FileNam
# 解包
7z x FileName.7z
```

## 邏輯判斷

[if / else 條件判斷式](https://www.ltsplus.com/linux/shell-script-if-else-elseif)

```bash
#!/bin/sh

# 單純if
if [ "$1" = "123" ]
then
    echo "var is 123"
fi

# if else
if [ "$1" = "123" ]
then
    echo "var is 123"
else
    echo "var is 456"
fi

# if elif else
if [ "$1" = "123" ]
then
    echo "var is 123"
elif [ "$1" = "456" ]
then
    echo "var is 456"
else
    echo "var is 789"
fi
```

## 迴圈範例

[\[Linux 文章收集\] Bash For Loop Examples](http://puremonkey2010.blogspot.com/2012/01/linux-bash-for-loop-examples.html)

- 範例1

```bash
#!/bin/bash
VIDEO_DIR=/home/nfs/dataset/auo_cctv/dorm/0701
WEIGHTS=/home/nfs/user/davidkylee/PycharmProjects/auomaskdetection/yolov5maskdetection/weights/bestsadd.pt
PROJECT=runs/detect

for s in ${VIDEO_DIR}/*.mp4
do
python3 -u detect.py --source $s --weights ${WEIGHTS} --project ${PROJECT} --name \`basename $s\` &> log/\`basename $s\`.log
done
```

- 範例2

```bash
#!/bin/ksh
# Tested with ksh version JM 93t+ 2010-03-05
for i in {1..100}
do
 # your-unix-command-here
 echo $i
done
```

- 範例3

```bash
#!/bin/bash

for i in -0.2 -0.1 0 0.1 0.2
do
echo $i
done
```

- 範例4: 迴圈跑轉檔且將副檔名變更為mp4  
	[\[Linux\] 使用 shell 截取檔案的檔名與副檔名](https://ephrain.net/linux-%E4%BD%BF%E7%94%A8-shell-%E6%88%AA%E5%8F%96%E6%AA%94%E6%A1%88%E7%9A%84%E6%AA%94%E5%90%8D%E8%88%87%E5%89%AF%E6%AA%94%E5%90%8D/)

```bash
for s in *; do ffmpeg -i $s -vcodec libx264 ${s%.*}.mp4; done
```

## 查看現有時間

- 範例1

```bash
\`date +%Y%m%d%H%M%S\`
```

- 範例2

```bash
$(date +%Y%m%d%H%M%S) #需加$
```

## 刪除程序範例

- 範例1，使用awk方式較穩

```bash
#!/bin/bash
kill \`ps uxf | grep 'http.server' | grep -v grep | awk '{print $2}'\`
ps uxw | grep http.server | grep -v grep | awk '{print $2}' | xargs kill -15 2>&1
```

- 範例2，使用cut可能欄位會取錯

```bash
#!/bin/bash
kill \`ps uxf | grep 'http.server' | grep -v grep | cut -d ' ' -f 2\`
```

## curl使用

[Linux Curl Command 指令與基本操作入門教學](https://blog.techbridge.cc/2019/02/01/linux-curl-command-tutorial/)

```bash
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "api_key: ff83c5bb97dd8073e2f58b54685cec71" -d '{}' "http://estone.corpnet.auo.com:8080/EstoneApi/API/APITask/UxAddTaskExt"
```

選擇 Repo

目錄

[Linux](#Linux) [搜尋系統套件](#搜尋系統套件) [debian](#debian) [關機](#關機) [切換使用者](#切換使用者) [切換root](#切換root) [環境變數](#環境變數) [呼叫變數](#呼叫變數) [查看環境變數](#查看環境變數) [查看PYTHONPATH](#查看PYTHONPATH) [指定PYTHONPATH](#指定PYTHONPATH) [各種引號功能介紹](#各種引號功能介紹) [指定cuda GPU](#指定cuda-GPU) [自動增長GPU記憶體用量](#自動增長GPU記憶體用量) [cuda版本](#cuda版本) [cudnn版本](#cudnn版本) [串接指令](#串接指令) [IDE](#IDE) [開pycharm](#開pycharm) [開jupyter notebook](#開jupyter-notebook) [virtualenv](#virtualenv) [查看虛擬環境](#查看虛擬環境) [virtualenvwrapper](#virtualenvwrapper) [conda](#conda) [建立虛擬環境](#建立虛擬環境) [venv](#venv) [virtualenvwrapper](#virtualenvwrapper27) [conda](#conda28) [啟動虛擬環境](#啟動虛擬環境) [venv](#venv30) [virtualenvwrapper](#virtualenvwrapper31) [conda](#conda32) [退出虛擬環境](#退出虛擬環境) [venv or virtualenvwrapper](#venv-or-virtualenvwrapper) [conda](#conda35) [刪除虛擬環境或package](#刪除虛擬環境或package) [venv](#venv37) [virtualenvwrapper](#virtualenvwrapper38) [conda](#conda39) [查看系統](#查看系統) [查看GPU指令](#查看GPU指令) [查看目前process](#查看目前process) [查詢系統上各process所開啟的檔案](#查詢系統上各process所開啟的檔案) [查看ip](#查看ip) [linux](#linux) [window](#window) [排程設定](#排程設定) [資料夾/檔操作](#資料夾檔操作) [查看資料夾](#查看資料夾) [查看資料夾大小](#查看資料夾大小) [移動資料](#移動資料) [修改權限](#修改權限) [更名](#更名) [壓縮檔案](#壓縮檔案) [搜尋檔案](#搜尋檔案) [資料內容操作](#資料內容操作) [sh執行檔開頭](#sh執行檔開頭) [查看log檔](#查看log檔) [grep應用](#grep應用) [查看檔案並修改檔案後排序](#查看檔案並修改檔案後排序) [複製查詢出來的檔案到指定資料夾](#複製查詢出來的檔案到指定資料夾) [xargs應用](#xargs應用) [查詢檔案後對每個檔案執行script](#查詢檔案後對每個檔案執行script) [刪除final best last以外的weight檔](#刪除final-best-last以外的weight檔) [篩選文件列](#篩選文件列) [進階功能](#進階功能) [計算列數](#計算列數) [查看xml檔](#查看xml檔) [修改xml檔](#修改xml檔) [批次修改xml檔](#批次修改xml檔) [查看二進制內容](#查看二進制內容) [解壓縮](#解壓縮) [邏輯判斷](#邏輯判斷) [迴圈範例](#迴圈範例) [查看現有時間](#查看現有時間) [刪除程序範例](#刪除程序範例) [curl使用](#curl使用)