---
title: "Python基礎"
source: "https://hackmd.io/iYiRsV_LTmOTuKrl-d7OOQ"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
###### tags: 基礎

---

[Python-100-Days, a very popular github project](https://github.com/jackfrued/Python-100-Days)

## python版本資訊

| OS / image | python3 | python3.6 | python3.7 | python3.8 | python3.9 | python3.10 |
| --- | --- | --- | --- | --- | --- | --- |
| tcai005 | **3.6.9** | **3.6.9** | 3.7.5 |  |  |  |
| Jetpack 4.4~4.6 | **3.6.9** |  |  |  |  |  |
| nvidia/l4t-base:r32.6.1 | **3.6.9** |  |  |  |  |  |
| Ubuntu 18.04 (bionic) | 3.6.7 | **3.6.9** | 3.7.5 | 3.8.0 |  |  |
| Ubuntu 20.04 (focal) | 3.8.2 | NA | NA | **3.8.10** | 3.9.5 |  |
| Ubuntu 21.10 (impish) | 3.9.4 | NA | NA | NA | 3.9.7 | 3.10.0 |
| Ubuntu 22.04 (jammy) | 3.10.4 | NA | NA | NA | NA | 3.10.4 |
| nvidia/l4t-tensorrt:r8.0.1-runtime | 3.8.0 / **3.6.9** |  |  |  |  |  |
| nvidia/l4t-tensorrt:r8.4.0-runtime | **3.8.10** |  |  |  |  |  |
| nodered/node-red:2.1.3-14 (Alpine 3.11) | **3.8.10** |  |  |  |  |  |
| gcp-airdp01 (AI365) | 3.6.5 / 3.7.9 |  |  |  |  |  |
| maz-openaivap01 | **3.6.9** | **3.6.9** | 3.7.5 | 3.8.0 |  |  |

## Jetpack version matrix

| Jetpack | Device | L4T | CUDA | cuDNN | TensorRT | Ubuntu | Python3 | Torch |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 5.1 | NX、Orin Nano | 35.2.1 | 11.4.19 | 8.6.0 | 8.5.2 | 20.04 | 3.8.10 | 1.14以上 |
| 5.0.2 | NX、Orin Nano | 35.1 | 11.4.14 | 8.4.1 | 8.4.1 | 20.04 | 3.8.10 | 1.11~1.13 |
| 5.0.1 | NX、Orin Nano | 34.1.1 | 11.4.6 | 8.3.2 | 8.4.0 | 20.04 | 3.8.10 | 1.11~1.12 |
| 5 | NX、Orin Nano | 34.1 | 11.4.6 | 8.3.2 | 8.4.0 | 20.04 | 3.8.10 | 1.11~1.12 |
| 4.6.2 | NX、Orin Nano | 32.7.2 | 10.2.300 | 8.2.1 | 8.2.1 | 18.04 | 3.6.9 |  |
| 4.6.1 | NX、Nano | 32.7.1 | 10.2.300 | 8.2.1 | 8.2.1 | 18.04 | 3.6.9 | 1.9~1.10 |
| 4.6 | NX、Nano | 32.6.1 | 10.2.300 | 8.2.1.32 | 8.0.1.6 | 18.04 | 3.6.9 | 1.8~1.9 |
| 4.5.1 | NX、Nano | 32.5.1 | 10.2.89 | 8.0.0.180 | 7.1.3.0 | 18.04 | 3.6.9 |  |
| 4.5 | NX、Nano | 32.5.0 | 10.2.89 | 8.0.0.180 | 7.1.3.0 | 18.04 | 3.6.9 | 1.6~1.7 |
| 4.4 | NX、Nano | 32.4.3 | 10.2.89 | 8.0.0.180 | 7.1.3.0 | 18.04 | 3.6.9 | 1.6 |
| 4.3 | NX、Nano | 32.3.1 | 10.0.326 | 7.6.3 | 6.0.1.10 | 18.04 | 3.6.7 |  |

## pip

## 查看pip版本

```bash
pip --version
which pip
```

## linux下載package

- pip安裝in maz-neo4j01

```bash
pip install xxxxx --proxy http://10.248.15.7:3128
```

- 設定proxy

```bash
export https_proxy=http://proxy.mydomain.com:<port>
export http_proxy=http://proxy.mydomain.com:<port>
```

- 參數介紹

| 參數 | 輸入 | 說明 |
| --- | --- | --- |
| \-r | ./requirements.txt | 安裝requirements.txt內package |
| –no-index |  | offline安裝 |
| \-f | ./ | whl檔路徑 |
| \-d | ./pip\_package/ | 下載目的資料夾 |
| –user |  | 安裝到個人package中 |
| –proxy | AUO\\帳號:密碼@auhqwsg.corpnet.auo.com:8080 | proxy設定 |
| –cert | ./TMGCert.cer | 認證檔 |
| –python-version | 37 | 指定python版本 |
| –only-binary | :all: | 僅下載binary檔 |
| –platform | manylinux2014\_aarch64 | 指定platform |
| –prefix | ./venv/venv\_36\_openpifpaf | 安裝路徑, 通常放虛擬環境路徑 |

- 安裝: pip install

```bash
# 正常安裝
pip install xxxx
# 使用offline安裝, 檔案路徑再./
pip install xxxx.whl --no-index --find-link=.
# 使用offline安裝, 檔案路徑再./, 安裝到個人環境
pip install xxxx.whl --user --no-index --find-link=.
# 設定proxy與cert檔
pip install grep --user --cert TMGCert.cer --proxy=AUO\\帳號:密碼@auhqwsg.corpnet.auo.com:8080
# 設定proxy與cert檔, 並安裝package到指定環境中
pip install grep --cert TMGCert.cer --proxy=AUO\\帳號:密碼@auhqwsg.corpnet.auo.com:8080 --prefix /home/nfs/user/davidkylee/PycharmProjects/HRNet-Bottom-Up-Pose-Estimation-master/venv1/
# 使用requirements.txt安裝套件
pip install -r requirements.txt
```

- 下載: pip download

```bash
# 設定proxy與cert檔
pip download grep --cert TMGCert.cer --proxy=AUO\\帳號:密碼@auhqwsg.corpnet.auo.com:8080
# 設定proxy與cert檔, 下載requirements.txt內所有package至pip_package資料夾
pip download -d ./pip_package/ -r requirements.txt --cert TMGCert.cer --proxy=AUO\\davidkylee:1234qweR@auhqwsg.corpnet.auo.com:8080
# 設定proxy與cert檔, 指定python版本, 並僅下載whl檔
pip download xxxxxxx --python-version 37 --only-binary=:all: --proxy=AUO\\帳號:密碼@auhqwsg.corpnet.auo.com:8080 --cert TMGCert.cer
# 指定python版本, 僅下載whl檔, 指定platform, platform請參考檔名
pip download xxxxxxx --platform manylinux2014_aarch64 --python-version 37 --only-binary=:all:
```

- 記錄版本: pip freeze

```bash
pip freeze > requirements.txt
```

自動下載失敗時，請用本機電腦下載whl檔，再放在server上  
如果本機下載whl檔失敗時(有時會被檔)，請用個人裝置下載後email回公司或下載tar.gz檔，whl檔檔案太大必要時需要使用usb傳輸

## window下載package

- 公司內OA

```bash
pip install pandas --proxy=auhqwsg.corpnet.auo.com:8080 --user --cert TMGCert.cer #要設定proxy
conda install packageName
```

- maz-apcdev01

```bash
pip install -i https://pypi.org/simple --proxy http://proxy-sslbypass.corpnet.auo.com:3128 {套件名稱}
```

- maz-apcdev03

```bash
pip install jupyter -i https://pypi.org/simple --proxy http://proxy-sslbypass.corpnet.auo.com:3128 --trusted-host pypi.org --trusted-host files.pythonhosted.org {套件名稱}
```

## build whl檔

[pip wheel](https://pip.pypa.io/en/stable/cli/pip_wheel/)

- Build wheels for a requirement (and all its dependencies), and then install

```bash
pip wheel --wheel-dir=/tmp/wheelhouse SomePackage
pip install --no-index --find-links=/tmp/wheelhouse SomePackage
```

- Build a wheel for a package from source

```bash
export BUILD_VERSION=x.x.x
pip wheel --no-binary SomePackage SomePackage
pip wheel --no-index --no-deps ./torchvision
```

- build h5py  
	[Cannot install h5py on Jetson Xavier NX](https://forums.developer.nvidia.com/t/cannot-install-h5py-on-jetson-xavier-nx/129261)

```bash
apt-get update
apt-get install -y cython libhdf5-serial-dev hdf5-tools libhdf5-dev
pip wheel ./h5py
```

## os常用路徑語法

[os常用路徑語法](https://pixnashpython.pixnet.net/blog/post/29444563-%E3%80%90os%E3%80%91os%E5%B8%B8%E7%94%A8%E5%8F%96%E5%BE%97%E6%AA%94%E6%A1%88%E8%B7%AF%E5%BE%91%E6%96%B9%E6%B3%95%EF%BC%9A%E4%B8%8A%E4%B8%80%E5%B1%A4%E7%9B%AE%E9%8C%84-)  
[Python os.path.getmtime()用法及代碼示例](https://vimsky.com/zh-tw/examples/usage/python-os-path-getmtime-method.html)

```python
import os

# 取得當目錄
os.getcwd()

# 取得同目錄下檔案位置
os.path.join(os.getcwd(), 'YourFileName.csv')

# 取得上一層目錄
os.path.abspath(os.path.join(os.getcwd(), os.path.pardir)) # 上層目錄

# 取得指定路徑下所有檔案
path = os.getcwd()
os.listdir(path) # ['main.py'] 有一個py檔叫main.py

# 檢查路徑有無存在
path_data = os.path.join(os.getcwd(), 'data')
os.path.exists(path_data) # False 沒有data資料夾

# 創建路徑
path_temp = os.path.join(os.getcwd(), 'model', 'temp')
os.makedirs(path_temp) # 在檔前工作目錄下model資料夾下創建temp資料夾

# 檢查路徑沒有就創立
path_icon = os.path.join(path, 'img', 'icon')
if os.path.exists(path_icon) == False: # 檢驗有無存檔資料夾
    os.makedirs(path_icon) # 沒有就建立

# 檢查下全部檔案大小
os.path.getsize(path) # 目錄檔案大小 4096 位元組

# 查看修改時間
os.path.getmtime(path)
```

---

## 隨機抽樣使用random

[Random sampling from a list in Python (random.choice, sample, choices)](https://note.nkmk.me/en/python-random-choice-sample-choices/)

```python
import random

l = [0, 1, 2, 3, 4]
print(random.sample(l, 3))
# [1, 3, 2]
print(type(random.sample(l, 3)))
# <class 'list'>
```

---

## 指定PYTHONPATH

```python
import sys
sys.path.append('...')
sys.path.insert(0, '...')
```

---

## import絕對路徑與相對路徑

[\[Python\] 解決 import 相對路徑執行問題](https://medium.com/bryanyang0528/python-%E8%A7%A3%E6%B1%BA%E7%9B%B8%E5%B0%8D%E8%B7%AF%E5%BE%91%E5%9F%B7%E8%A1%8C%E5%95%8F%E9%A1%8C-5cb157d22ab0)  
[Python的绝对引用 vs 相对引用全面讲解](https://blog.csdn.net/ning13481193737/article/details/107165586)

- 資料夾結構

```bash
| ---- a
|       | ---- main1.py
|       | --- b
|                |  --- c
|                         | --- main2.py
|                         | --- test.py
```

- [main1.py](http://main1.py/)

```python
import sys
print(sys.path)
from .b.c.test import getcwd
getcwd()
```

- [main2.py](http://main2.py/)

```python
import sys
print(sys.path)
from .test import getcwd
getcwd()
```

- [test.py](http://test.py/)

```python
import os
import sys
print('test',sys.path)
def getcwd():
    print('getcwd',os.getcwd())
```

- 執行狀況

```bash
/$ python3 a/main1.py       #報錯，將main1.py中的.移除即不報錯
/a$ python3 main1.py        #報錯，將main1.py中的.移除即不報錯
/$ python3 -m a.main1       #正確，可執行
/a$ python3 -m main1        #報錯，至少需有一個.才可執行
/$ python3 a/b/c/main2.py   #報錯，將main2.py中的.移除即不報錯
/a/b/c$ python3 main2.py    #報錯，將main2.py中的.移除即不報錯
/$ python3 -m a.b.c.main2   #正確，可執行
/a$ python3 -m b.c.main2    #正確，可執行
/a/b$ python3 -m c.main2    #正確，可執行
/a/b/c$ python3 -m main2    #報錯，至少需有一個.才可執行
```

- 心得
	1. 直接執行.py檔，則此.py檔不可使用相對路徑，但此.py檔import的module可使用相對路徑
		2. 直接執行.py檔，檔案位置為PYTHONPATH，以此為import絕對路徑的起點
		3. 如需使用相對路徑，必須使用-m方式呼叫且呼叫的module不可為第一層
		4. 不需直接執行的.py檔，或者稱module檔，使用相對路徑沒有問題
		5. 不直接執行的.py檔，可使用相對路徑或絕對路徑，絕對路徑為PYTHONPATH
		6. 直接執行的.py檔，必須使用絕對路徑，但如果此py檔不在根目錄，也無法直接執行，需把根目錄指定為PYTHONPATH
		7. 直接執行的.py檔，使用根目錄為起始點，如此py檔不在根目錄，只需把根目錄指定為PYTHONPATH即可

---

## Python的super繼承

[python的super繼承](https://ithelp.ithome.com.tw/articles/10222948)

```python
class student:
    def __init__(self,name=None,gender=None,dep=None,ID=None):
        self.name=name
        self.gender=gender
        self.dep=dep
        self.ID=ID
    
    def get_gender(self):
        return self.gender

student_a = student(gender='male')

class ABC:
    def __init__(self,gender):
        self.gender = gender

abc = ABC(student_a.get_gender())

class Person(student):
    def __init__(self, name, gender, dep, ID, pro_qua):
        # 如果父類別的__init__有參數時, 需使用super()繼承
        # super().__init__會去呼叫父類別的initializer__init__
        super().__init__(name, gender, dep, ID) 
        self.pro_qua=pro_qua
        
    def Profess(self):
        General_Programming={'C','C++','C#','JAVA'}
        Statics_Programming={'Python','R'}
        Engineering_Programming={'Matlab','Fortran'}
        
        if self.pro_qua in General_Programming:
            return'General_Programming'
        elif self.pro_qua in  Statics_Programming:
            return'Statics_Programming'
        elif self.pro_qua in  Engineering_Programming:
            return 'Engineering_Programming'
        else:
            return 'Offices'
            
if __name__=='__main__':
    A=Person('Eason','Male','Civil Engineering','9487943','Python')
    print('Name:{}  Department:{}  ID:{}'.format(A.name,A.dep,A.ID)) 
    print('Skill:{} ({})'.format(A.Profess(),A.pro_qua))

#output:
#Name:Eason     Department:Civil Engineering     ID:9487943
#Skill:Statics_Programming (Python)
```

---

## tqdm基礎用法

[tqdm](https://tqdm.github.io/docs/tqdm/)

- for loop

```python
from tqdm import tqdm
for i in tqdm(range(10000)):
    ...
```

- while loop

```python
pbar = tqdm()
time=0
while time<100:
    sleep(0.1)
    time += 1
    pbar.update(1)
pbar.close()
```

---

## 迭代器

[python中yield的用法详解——最简单，最清晰的解释](https://blog.csdn.net/mieleizhi0522/article/details/82142856)

- 單次使用: next

```python
def foo(num):
    print('starting...')
    while num<=3:
        num+=1
        yield num
a = foo(0)
print(next(a)) # 1
print(next(a)) # 2
print(next(a)) # 3
print(next(a)) # error
print(next(a),None) # None
```

- 迴圈使用

```python
a = foo(0)
for i in a:
    print(i) # 1,2,3
```

## @fun 裝飾器

[Python @函数装饰器及用法（超级详细）](http://c.biancheng.net/view/2270.html)  
裝飾器案例

```python
def print_fun_time(func):
    def new_func(*args, **args2):
        t0 = time.time()
        e_print("@%s, {%s} start" % (time.strftime("%X", time.localtime()), func.__name__))
        back = func(*args, **args2)
        e_print("@%s, {%s} end" % (time.strftime("%X", time.localtime()), func.__name__))
        e_print("@%.3fs taken for {%s}" % (time.time() - t0, func.__name__))
        return back
    return new_func
```

函數裝飾器的工作原理是怎樣的呢？假設用 funA() 函數裝飾器去裝飾 funB() 函數，如下所示：

```python
#funA 作為裝飾器函數
def funA(fn):
    #...
    fn() # 執行傳入的fn參數
    #...
    return '...'
@funA
def funB():
    #...
```

實際上，上面程序完全等價於下面的程序：

```python
def funA(fn):
    #...
    fn() # 執行傳入的fn參數
    #...
    return '...'
def funB():
    #...
funB = funA(funB)
```

上面示例中，都是使用一個裝飾器的情況，但實際上，Python 也支持多個裝飾器，比如：

```python
@funA
@funB
@funC
def fun():
    #...
```

上面程序的執行順序是裡到外，所以它等效於下面這行代碼：

```python
fun = funA(funB(funC(fun)))
```

## pycharm debug mode

[新手必会，pycharm的调试功能(史上最详篇)](https://zhuanlan.zhihu.com/p/62610785)

- Edit Configurations
	- **Script path**: 選擇python主程式
		- **Parameters**: 設定主程式後的參數, ex: –help
		- **Working directory**: 設定執行主程式時的pwd, 預設是放主程式的資料夾
- 功能介紹
	- **step over（F8快捷鍵）** ：在單步執行時，在函數內遇到子函數時不會進入子函數內單步執行，而是將子函數整個執行完再停止，也就是把子函數整個作為一步。在不存在子函數的情況下是和step into效果一樣的。簡單的說就是，程序代碼越過子函數，但子函數會執行，且不進入。
		- **step into（F7快捷鍵）** ：在單步執行時，遇到子函數就進入並且繼續單步執行，有的會跳到源代碼裡面去執行。
		- **step into my code（Alt+Shift+F7快捷鍵）** ：在單步執行時，遇到子函數就進入並且繼續單步執行，不會進入到源碼中。
		- **step out（Shift+F8快捷鍵）** ：假如進入了一個函數體中，你看了兩行代碼，不想看了，跳出當前函數體內，返回到調用此函數的地方，即使用此功能即可。
		- **Resume program(F9快捷鍵)** ：繼續恢復程序，直接運行到下一斷點處。
- 以上四個功能，就是最常用的功能，一般操作步驟
	- **設置好斷點，debug運行**
		- **然後 F8 單步調試，遇到想進入的函數 F7 進去**
		- **想出來在 shift + F8，跳過不想看的地方**
		- **直接設置下一個斷點，然後 F9 過去。**

## jupyter notebook refactor with jupytext

[使用Jupyter Notebooks（使用Jupytext和Papermill）自动生成报告](https://segmentfault.com/a/1190000040036521)

1. 安裝jupytext

```bash
pip install jupytext
conda install jupytext -c conda-forge
```

2. 將雜亂的jupyter notebook檔案轉成py檔

```bash
jupytext xxxxx.ipynb --to py
# 會在同一目錄下產生一個xxxxx.py檔
```

3. 使用pycharm開啟py檔並執行refactor，此時的py檔已排除output內容，很適合版本控管
4. 再將已執行refactor的py檔轉回ipynb檔

```bash
jupytext xxxxx.py --to ipynb
# 會在同一目錄下覆蓋原有xxxxx.ipynb
```

5. 使用jupyter notebook開啟ipynb檔，看到的就是可讀性高的檔案，需注意這邊轉回的ipynb檔已沒有output內容
6. 因為已把重要method萃取出來，也可提供method給後續script.py使用

## cProfile 使用教學

[cProfile Python性能分析工具使用详解](http://yx-pc.net/article/371764.html)  
[python性能分析之cProfile模块](https://cloud.tencent.com/developer/article/1581156)  
[Python性能分析工具Profile使用实例](https://www.jb51.net/article/174589.htm)  
[爱上python系列–––python性能(二):line\_profiler性能分析](https://blog.csdn.net/zhou_438/article/details/109183237)  
[使用line\_profiler对python代码性能进行评估优化](https://www.cnblogs.com/dechinphy/p/line-profiler.html)

- 執行主程式並記錄profile檔

```bash
python3 -m cProfile -o proj_flask_server.out proj_flask_server.py
python3 -m cProfile -o proj_flask_client.out proj_flask_client.py
python3 -m cProfile -s cumulative -o proj_flask_server.out proj_flask_server.py
python3 -m cProfile -s cumulative -o proj_flask_client.out proj_flask_client.py
```

- 彙整profile檔並依花費時間排序，使用cumulative排序看高階function花費時間

```bash
python3 -c "import pstats; p=pstats.Stats('proj_flask_client.out'); p.sort_stats('cumulative').print_stats()"
python3 -c "import pstats; p=pstats.Stats('proj_flask_client.profile'); p.sort_stats('cumulative').print_stats()" | grep proj
python3 -c "import pstats; p=pstats.Stats('proj_flask_client.out'); p.sort_stats('time').print_stats()"
```

## 程式lock機制

[用排隊上廁所來比喻Python Thread的Lock機制！](https://ithelp.ithome.com.tw/articles/10254439)  
Lock機制通常會使用於，當有多個線程要使用同一個代碼資源，且對同一個全域(共享)變數進行修改的時候

- 舉例: 上廁所排隊

```python
import threading
import time

# 宣告鑰匙物件
lock = threading.Lock()  # 廁所門的鑰匙(lock)
toilet = []  # 放屎的馬桶(list)

# 上廁所(function)，如果只是flask server，僅需在method內開頭執行lock.acquire()，結尾執行lock.release()
def WC():
    with lock # 使用鑰匙將廁所門上鎖，將廁所門解鎖, 並把鑰匙放在旁邊等下一個人來拿
        toilet.append(f"{threading.current_thread().name}: 拉了第1坨屎")  # 將當前的人(線程)所拉的第一屎放進馬桶(list)中
        time.sleep(0.1)
        toilet.append(f"{threading.current_thread().name}: 拉了第2坨屎")  # 將當前的人(線程)所拉的第二屎放進馬桶(list)中

# 呼叫function時，使用多執行序做法呼叫
# 如為flask client，此段可省略
# 產生3位排隊大號的人
for i in range(3):
    wc_thread = threading.Thread(target=WC)
    wc_thread.start()  # 第 i 個人開始進廁所大號

time.sleep(1)  # 等待一秒確保三個人都上完廁所, 且馬桶內都有他們排放的屎了
print(toilet)  # 將馬桶內的屎打印出來看排序
```

## jupyter

## style.bar

[Table Visualization](https://pandas.pydata.org/docs/user_guide/style.html)

```python
import pandas as pd
df = pd.read_csv('xxx.csv')
df[['sk_pts', '以上累積percent', '以上累積counts']].style.bar(vmin=0.0, vmax=100.0, subset=['以上累積percent', ], color='#d65f5f')
```

## 型態註解

[使用 Python typing 模組對你的同事好一點](https://myapollo.com.tw/blog/python-typing-module/)  
[用Python Typing提升程式碼的可維護性: 從基本標註到泛型標註](https://medium.com/ai-blog-tw/python-typing-guide-1d44f40790a)

- function

```python
def get_value(json: str) -> dict:
     return parse(json)
```

- variable

```python
price: float = 19.99
name: str = "Alice"
```

選擇 Repo