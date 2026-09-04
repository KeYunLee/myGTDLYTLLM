---
title: "TensorRT tutorial"
source: "https://hackmd.io/L7uoraI7SqG3yFzchWae3Q"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
###### tags: 基礎

[torch2trt github](https://github.com/NVIDIA-AI-IOT/torch2trt)  
[torch2trt home](https://nvidia-ai-iot.github.io/torch2trt/master/index.html)  
[NVIDIA TensorRT home](https://docs.nvidia.com/deeplearning/tensorrt/index.html)  
[NVIDIA container release note](https://docs.nvidia.com/deeplearning/tensorrt/container-release-notes/running.html)  
[GPU Compute Capability](https://developer.nvidia.com/cuda-gpus)  
[TensorRT 介紹與安裝教學](https://medium.com/ching-i/tensorrt-%E4%BB%8B%E7%B4%B9%E8%88%87%E5%AE%89%E8%A3%9D%E6%95%99%E5%AD%B8-45e44f73b25e)  
[TensorRT 快速指南](https://zhuanlan.zhihu.com/p/359838019)  
[如何使用TensorRT对训练好的PyTorch模型进行加速?](https://zhuanlan.zhihu.com/p/88318324)  
[深入理解 TensorRT (1) TensorRT Python API 详解](https://www.codetd.com/article/13130133#15_IExecutionContext_89)

## TensorRT安裝需特別注意版本…，使用nvidia官方docker image絕不會有版本問題

- TensorRT 8.2.0 EA has been tested with the following:
	- cuDNN 8.2.1
		- TensorFlow 1.15.5
		- PyTorch 1.9.0
		- ONNX 1.9.0
		- This TensorRT release supports CUDA:
		- 11.4 update 2
				- 11.3 update 1
				- 11.2 update 2
				- 11.1 update 1
				- 11.0 update 1
				- 10.2
- 系統套件需求

```bash
apt-get update
apt-get install -y libegl1
```

## TensorRT流程圖

- (目前作法)方法一: 標準做法，需要以下資訊
	- Config
		- 給定image size: (w,h)
				- 給定input batch size: 1
				- 給定input graph name: name from graph
				- 給定output grahpe name: name from graph
		- 實做model inference wrapper: 盡量設計與原介面相同，Ex: model.predict()

```
graph TD
subgraph Config
config
end
subgraph Framework weight
torch.weight
tensorflow.weight
keras.weight
end
subgraph Convert weight to ONNX
torch2onnx
tensorflow2onnx
keras2onnx
end
subgraph Convert to TensorRT
onnx2trt
end
subgraph Visualize
visualize[network visualize]
end
subgraph Manual define model wrapper
inference_by_trt
end
torch.weight-->torch2onnx-->.onnx-->onnx2trt-->.trt-->inference_by_trt
tensorflow.weight-->tensorflow2onnx-->.onnx
keras.weight-->keras2onnx-->.onnx
config-->torch2onnx
config-->tensorflow2onnx
config-->keras2onnx
.onnx-->visualize
```

- (不推薦)方法二: pytorch快速做法，用torch2trt轉trt，也只能用torch2trt做後續inference

```
graph TD
torch.weight-->torch2trt-->.trt-->inference_by_torch2trt
```

## 版本相容整理(已跑過的)

| Framework | Framework版本 | 轉ONNX套件版本 | ONNX版本 | TRT版本 |
| --- | --- | --- | --- | --- |
| Tensorflow | 1.12-1.15, 2.1-2.7 | tf2onnx==1.9.3 | opset==11 | 8.0.1.6 |
| Torch | git環境版本 | Torch內建export | opset==11 | 8.0.1.6, 7.2.1 |
| Caffe | openpose==1.5.1 | caffe-onnx | opset==9 | 7.2.1 |

## 已有環境

因Framework在不同專案通常為不同版本

- Tensorflow: 使用docker image轉ONNX/TRT
- Torch: 使用docker image轉TRT，轉ONNX的部分請在git環境下使用torch內建export轉

統一使用TRT 8.0.1.6版本轉TRT

| docker image(tcai005) | Framework版本 | 轉ONNX套件版本 | TRT版本 | 環境需求 |
| --- | --- | --- | --- | --- |
| [nvcr.io/nvidia/tensorrt:21.07-py3-ReID\_AdaptiveL2](http://nvcr.io/nvidia/tensorrt:21.07-py3-ReID_AdaptiveL2) | Tensorflow==2.7.0 | tf2onnx==1.9.3 | 8.0.1.6 | NVIDIA Driver 470+, CUDA compute capability 3.5 |
| [nvcr.io/nvidia/tensorrt:21.07-py3](http://nvcr.io/nvidia/tensorrt:21.07-py3) | NA | NA | 8.0.1.6 | NVIDIA Driver 470+, CUDA compute capability 3.5 |
| [nvcr.io/nvidia/tensorrt:20.11-py3-forHD](http://nvcr.io/nvidia/tensorrt:20.11-py3-forHD) | pytorch==1.9.0+cu111 | pytorch==1.9.0+cu111 | 7.2.1 | NVIDIA Driver 455+, CUDA compute capability 3.5 |

## torch2onnx(可自動化，需開發界面)

[TORCH.ONNX](https://pytorch.org/docs/stable/onnx.html)  
[TORCH SAVING AND LOADING MODELS](https://pytorch.org/tutorials/beginner/saving_loading_models.html)  
[什么是ONNX opset](https://www.codenong.com/48935582e4370fdea9bd/)  
[ONNX动态输入尺寸的问题【多输出/多输入】【pytorch/onnx/onnxruntime】](https://blog.csdn.net/weixin_38443388/article/details/108677003)

### 光torch.load的output就可以只含模型或你客製化的字典(其中某個你自己命名的key為模型)，要自動化此部分需考慮情況多，暫時不考慮此段自動化

- 轉onnx時，需指定image size，inference batch size可選擇動態或靜態
- 需注意opset版本，目前測試opset 11版正常，opset 13版失敗
- openpifpaf

```bash
python3 -m openpifpaf.export_onnx --checkpoint shufflenetv2k30 --outfile openpifpaf-shufflenetv2k30.onnx --input-width 640 --input-height 480
```

- torch2onnx: 需注意model.load方式需與你儲存模型時的格式匹配

```python
input_w = 640
input_h = 480
dummy_input = torch.randn(1, 3, input_h, input_w)

model = torch.load(xxxx.pb)
model = torch.load(xxxx.pkl)['model'] # if 'model' is a key
model = torch.load(xxxx.pkl)['mymodel'] # if 'mymodel' is a key
model = ...(a torch model)

outfile = xxxx.onnx
torch.onnx.export(
    model, dummy_input, outfile, verbose=True,
    # 需指定輸入與輸出的graph name
    input_names=['input_batch'], output_names=['cif', 'caf'],
    # 指定onnx版本
    opset_version=11,
    do_constant_folding=True,
    # 可指定哪一維度要使用dynamic動態推論，通常是batch size使用動態
    # 下面為第0維度使用動態，第0維度這裡代表batch size
    dynamic_axes={
        'input_batch': {0: 'dynamic'},
        'cif': {0: 'dynamic'},
        'caf': {0: 'dynamic'},
    }
)
```

- 不同deep learning framework有各自輸出onnx的指令，需依自己

## keras2onnx: 目前統一使用tf2onnx即可

[keras2onnx](https://github.com/onnx/keras-onnx)  
[keras2onnx example](https://github.com/onnx/keras-onnx/blob/master/tutorial/TensorFlow_Keras_MNIST.ipynb)  
[将keras的h5模型转化为onnx](https://blog.csdn.net/weixin_43826596/article/details/101080633)

## tf2onnx: 可轉TensorFlow/Keras/Tensorflow.js/Tflite to ONNX

[tf2onnx](https://github.com/onnx/tensorflow-onnx)  
[tf2onnx example](https://github.com/onnx/tensorflow-onnx/blob/master/tutorials/keras-resnet50.ipynb)  
[tensorflow版本最好為2.0以上，才不會報錯](https://github.com/onnx/tensorflow-onnx/issues/1210)  
[深度学习模型转换与部署那些事(含ONNX格式详细分析)](https://bindog.github.io/blog/2020/03/13/deep-learning-model-convert-and-depoly/)  
[部署环境之：tf2onnx - Convert TensorFlow models to ONNX](https://zhuanlan.zhihu.com/p/157477465)

### 安裝install

```bash
pip3 install tf2onnx
```

### pb檔轉onnx: 穩定，解決自定義layer問題，盡量先把tf的model存成pb檔

- 存tf模型範例  
	[tf.saved\_model 命名空間下的函式](https://ithelp.ithome.com.tw/articles/10247336)  
	[TensorFlow 2 教學：SavedModel 儲存模型](https://weikaiwei.com/tf/tensorflow-2-savedmodel/)

```python
inference_model = ... # a tf model
tf.saved_model.save(inference_model, "./weight/reid/tmp_model")
```

- 轉onnx，注意tf模型的input通常是nhwc，需加入 –inputs-as-nchw，轉onnx時input會順便做transpose

```bash
python3 -m tf2onnx.convert --saved-model tmp_model/ --output training_model_Market1501_DukeMTMC_nchw.onnx --opset 11 --inputs-as-nchw input_6
```

### h5檔轉onnx: 自定義的layer在model載入時可能會報錯，在當初產生h5檔時，就要驗證自定義載入問題

- h5模型載入錯誤

```python
inference_model = ... # a keras model
inference_model.save('./weight/reid/training_model_Market1501_DukeMTMC.h5')

from tensorflow.keras.models import load_model
model = load_model('./weight/reid/training_model_Market1501_DukeMTMC.h5')
# 報錯 ValueError: Unknown regularizer: AdaptiveL1L2
```

- h5轉onnx錯誤

```bash
python3 -m tf2onnx.convert --keras training_model_Market1501_DukeMTMC.h5 --output training_model_Market1501_DukeMTMC.onnx --opset 11 --inputs-as-nchw input_6
# 報錯 ValueError: Unknown regularizer: AdaptiveL1L2
```

## Caffe2onnx

[caffe-onnx github](https://github.com/htshinichi/caffe-onnx)

- 更改caffe.prototxt檔中，指定image size

```bash
vim ../auoismartliving/release/weight/openpose/models/pose/body_25/pose_deploy.prototx
input_dim: 240 # H: image height. This value will be defined at runtime
input_dim: 320 # W: image weight. This value will be defined at runtime
```

- 執行轉檔

```bash
python3 convert2onnx.py ../auoismartliving/release/weight/openpose/models/pose/body_25/pose_deploy.prototxt ../auoismartliving/release/weight/openpose/models/pose/body_25/pose_iter_584000.caffemodel openpose_body25_640x480 ../auoismartliving/release/weight/openpose/models/pose/body_25
```

## onnx visualize

[netron github](https://github.com/lutzroeder/netron)  
[pytorch模型轉換為onnx，並使用netron視覺化](https://www.gushiciku.cn/pl/gnop/zh-tw)

- install package

```bash
pip install netron
```

- visualize on browser

```python
import netron
onnx_path = '/home/nfs/user/davidkylee/PycharmProjects/auoactionai/onnx/shufflenetv2k30-210409-024202-cocokp-o10s-f4fb0807.onnx'
host = '10.96.41.41'
port = 8080
netron.start(onnx_path,browse=False,address=(host, port))
```

## onnx2trt

[TensorRT Command-Line Wrapper: trtexec](https://www.ccoderun.ca/programming/doxygen/tensorrt/md_TensorRT_samples_opensource_trtexec_README.html)  
[TensorRT trtexec的用法说明](https://blog.csdn.net/qq_29007291/article/details/116135737)  
[where is trtexec?](https://forums.developer.nvidia.com/t/where-is-trtexec/73514/2)  
[【tensorrt】——trtexec动态batch支持与batch推理耗时评测](https://python.iitter.com/other/45612.html)  
[onnx-tensorrt github](https://github.com/onnx/onnx-tensorrt)  
[Network has dynamic or shape inputs, but no optimization profile has been defined](https://github.com/onnx/onnx-tensorrt/issues/518)  
[ONNX Model Int64 Weights](https://forums.developer.nvidia.com/t/onnx-model-int64-weights/124248)  
[Assertion failed: tensors.count(input\_name) error when converting onnx to tensorrt](https://github.com/onnx/onnx-tensorrt/issues/302)  
[Assertion failed: ctx->tensors().count(inputName)](https://github.com/onnx/onnx-tensorrt/issues/414)  
[conversion to onnx then trt](https://github.com/openpifpaf/openpifpaf/issues/431)  
[pytorch/mxnet模型tensorrt部署-踩坑記錄](https://blog.csdn.net/u011605951/article/details/108441935)  
[【TensorRT系列】3.一个例子：PyTorch->ONNX->TensorRT](https://zhuanlan.zhihu.com/p/395590559)  
[用TensorRT优化Mobilenetv2踩坑记录](https://zhuanlan.zhihu.com/p/359686680)

## Run in docker container

[Nvidia TensorRT container](https://ngc.nvidia.com/catalog/containers/nvidia:tensorrt)  
[Docker 筆記 Part 2 ｜指令操作](https://visonli.medium.com/docker-%E5%85%A5%E9%96%80-%E7%AD%86%E8%A8%98-part-2-91e4dfa2b365)

- go to docker container env, tcai013

```bash
docker image list
docker run --gpus all -it --rm -v /home/nfs:/home/nfs -v /home/davidkylee/.cache:/root/.cache nvcr.io/nvidia/tensorrt:20.11-py3-iHealthcare2-ffmpeg
```

- check trtexec

```bash
which trtexec
trtexec --help
pip show tensorrt
pip show pycuda
```

- install python package  
	[docker环境里安装opencv ImportError: libGL.so.1: cannot open shared object file: No such file or directory](https://blog.csdn.net/Max_ZhangJF/article/details/108920050)

```bash
pip install opencv-python-headless
pip install openpifpaf
pip install torch
pip install torchvision
```

- commit image

```bash
docker container list
docker commit -m "add package for run openpifpaf" -a "davidkylee" [somecontainerid] nvcr.io/nvidia/tensorrt:20.11-py3-openpifpaf
```

## onnx2trt by trtexec(可自動化，需開發界面)

- 開啟docker container: go to docker container env tcai013

```bash
docker run --gpus all -it --rm -v /home/nfs:/home/nfs -v /home/davidkylee/.cache:/root/.cache nvcr.io/nvidia/tensorrt:20.11-py3-iHealthcare2-ffmpeg
```

- 轉trt時，如果onnx當時為動態batch size，需指定batch size

```bash
/usr/src/tensorrt/bin/trtexec --onnx=./onnx/openpifpaf_shufflenetv2k30_641_369.onnx --fp16 --saveEngine=./onnx/openpifpaf_shufflenetv2k30_641_369.trt --batch=1 --workspace=25
```

- (暫不使用)或指定最小/最大/預設的batch size

```bash
/usr/src/tensorrt/bin/trtexec --onnx=shufflenetv2k30-210409-024202-cocokp-o10s-f4fb0807.onnx --fp16 --saveEngine=shufflenetv2k30-210409-024202-cocokp-o10s-f4fb0807.trt --verbose --minShapes=input_batch:1x3x480x640 --optShapes=input_batch:1x3x480x640 --maxShapes=input_batch:1x3x480x640
```

| 參數 | 輸入 | 說明 |
| --- | --- | --- |
| –onnx | input.onnx | 路徑 |
| –best |  | 最快速度，準度超差 |
| –int8 |  | int8，準度差 |
| –fp16 |  | float16，準度正常(常用) |
| –saveEngine | output.trt | 路徑 |
| –batch | int | batch size |
| –workspace | int | 工作記憶體 |
| –minShapes | input\_batch:1x3x480x640 | graph輸入的名稱:shape |
| –optShapes | input\_batch:1x3x480x640 | graph輸入的名稱:shape |
| –maxShapes | input\_batch:1x3x480x640 | graph輸入的名稱:shape |

## inference(有條件自動化，需定義規則)

[TensorRT 多线程报错](https://blog.csdn.net/yiyayi1/article/details/111314520)  
[Python多进程部署TensorRT的问题及解决](https://www.jianshu.com/p/ece9708fb261)  
[使用TensorRT加速PyTorch模型](https://blog.yuxinzhao.top/speed-up-pytorch-model-using-tensorrt)  
[【TensorRT】execute\_async VS execute\_async\_v2](https://blog.csdn.net/ChuiGeDaQiQiu/article/details/119674722)

- 定義介面，盡量設計與原介面相同以減少開發時間，Ex: model.predict()

```python
class MyModel(TrtPredict):
    def __init__(self, trt_path, input_size):
        super().__init__(trt_path, input_size)
    # 定義predict method，與model.predict同介面
    def predict(self, image):
        input_name = 'input'
        input_dict = {input_name:image}
        output_dict = self.trt_inference(input_dict)
        output_name = 'output'
        output = output_dict[output_name]
        # do some transfor process
        final_output = np.argmax(output,axis=1)
        return final_output
```

- 需特別注意cuda.init方式

```python
#### 方法一: 手動init
import pycuda.driver as cuda

cuda.init()
cuda_ctx = cuda.Device(gpu_id).make_context()

cuda_ctx.push()
... tensorrt inference
cuda_ctx.pop()

#### 方法二: 自動init
import pycuda.autoinit  # This is needed for initializing CUDA driver

pycuda.autoinit.context.push()
... tensorrt inference
pycuda.autoinit.context.pop()
```

- 父類別TrtPredict用法

```python
# 給定trt路徑與對應之照片長寬
trt_path = 'xxxx.trt'
input_size = (640,480)
# 建立predict wrapper
predict = TrtPredict(trt_path, input_size)
# 定義輸入字典
input_dict = {'input_batch': np_img_batch}
# 執行推論，得到輸出字典
outputs_dict = predict.trt_inference(input_dict)
```

- 父類別說明

```python
class TrtPredict:
    '''
    general predict wrapper for tensorrt
    :param trt_path: a str, trt path.
    :param input_size: a tuple (w,h), ex:(640,480)
    '''

    def __init__(self, trt_path, input_size):
        self.trt_path = trt_path
        self.model_input_size = input_size
        self.w, self.h = input_size
        self.trt_logger = trt.Logger(trt.Logger.INFO)
        self._init_cuda_stuff()
    # 載入tensorrt engine
    def _load_engine(self):
        assert os.path.exists(self.trt_path), 'trt_path is not exist ' + self.trt_path
        with open(self.trt_path, 'rb') as f, trt.Runtime(self.trt_logger) as runtime:
            return runtime.deserialize_cuda_engine(f.read())
    # wrapper初始化
    def _init_cuda_stuff(self):
        cuda.init()
        self.device = cuda.Device(0)  # enter your Gpu id here
        self.cuda_context = self.device.make_context()
        self.engine = self._load_engine()

        if self.cuda_context:
            self.cuda_context.push()

        try:
            self.engine_context = self.engine.create_execution_context()
            # 取得buffer記憶體以及engine相關資訊
            bindings, host_inputs, cuda_inputs, name_inputs, shape_inputs, host_outputs, cuda_outputs, name_outputs, shape_outputs, stream = allocate_buffers(
                self.engine)
            self.bindings = bindings
            self.host_inputs = host_inputs
            self.host_outputs = host_outputs
            self.cuda_inputs = cuda_inputs
            self.cuda_outputs = cuda_outputs
            self.name_inputs = name_inputs
            self.name_outputs = name_outputs
            self.shape_inputs = shape_inputs
            self.shape_outputs = shape_outputs
            self.stream = stream
        except Exception as e:
            raise RuntimeError('fail to allocate CUDA resources') from e

        finally:
            if self.cuda_context:
                self.cuda_context.pop()
                
    def __del__(self):
        """ Free CUDA memory. """
        if self.cuda_context:
            self.cuda_context.pop()
            del self.cuda_context
        del self.engine_context
        del self.engine
    
    # 推論介面
    def trt_inference(self, input_dict):
        '''
        tensorrt inference method
        :param input_dict: a dictory, key is input name, value is a batch np.array, ex: {'input_batch': a np.array}, shape is (1,3,480,640) and include batch dim
        :return: a dict, key is output graph name, value is a batch np.array
        '''
        bindings = self.bindings
        host_inputs = self.host_inputs
        host_outputs = self.host_outputs
        cuda_inputs = self.cuda_inputs
        cuda_outputs = self.cuda_outputs
        name_inputs = self.name_inputs
        name_outputs = self.name_outputs
        shape_inputs = self.shape_inputs
        shape_outputs = self.shape_outputs
        stream = self.stream
        # t_begin = time.perf_counter()
        if self.cuda_context:
            self.cuda_context.push()
        # 將輸入寫入記憶體中
        for i in range(len(host_inputs)):
            name = name_inputs[i]
            shape = shape_inputs[i]
            input_np_batch = input_dict[name]
            assert input_np_batch.shape == shape, 'input format need ' + str(shape) + ', but data is ' + str(
                input_np_batch.shape)
            host_inputs[i] = np.ravel(np.zeros_like(input_np_batch))
            np.copyto(host_inputs[i], np.ravel(input_np_batch))
            cuda.memcpy_htod_async(cuda_inputs[i], host_inputs[i], stream)
        # 執行推論
        self.engine_context.execute_async(
            batch_size=1,
            bindings=bindings,
            stream_handle=stream.handle)
        # 將輸出取出
        for i in range(len(host_outputs)):
            cuda.memcpy_dtoh_async(host_outputs[i], cuda_outputs[i], stream)
        stream.synchronize()
        if self.cuda_context:
            self.cuda_context.pop()
        # 輸出reshape後即為模型輸出
        outputs_dict = {}
        for i in range(len(host_outputs)):
            shape = shape_outputs[i]
            name = name_outputs[i]
            total_shape = np.prod(shape)
            output = host_outputs[i][0: total_shape]
            output = np.reshape(output, tuple(shape))
            outputs_dict[name] = output

        return outputs_dict
```

- 使用python執行inference時，需安裝python呼叫cuda的Python package API，docker image有包含Python API

```bash
pip install pycuda
```

- (已Fix)目前inference仍會版本問題報錯，error due to TensorRT 7.0.0 need cuDNN 7.6.3 not 7.4.2，使用docker container後正常

```bash
[TensorRT] WARNING: TensorRT was linked against cuDNN 7.6.3 but loaded cuDNN 7.4.2
[TensorRT] ERROR: Parameter check failed at: engine.cpp::resolveSlots::1092, condition: allInputDimensionsSpecified(routine)
```

## multi threads

[multiple\_threads github](https://github.com/inducer/pycuda/blob/72aae2da98d034203ebb243ec621247ab8a60341/examples/multiple_threads.py)  
[Frequently Asked Questions about PyCUDA](https://wiki.tiker.net/PyCuda/FrequentlyAskedQuestions/#How_does_PyCUDA_handle_threading.3F)

選擇 Repo

目錄

[TensorRT tutorial](#TensorRT-tutorial) [TensorRT安裝需特別注意版本…，使用nvidia官方docker image絕不會有版本問題](#TensorRT安裝需特別注意版本…，使用nvidia官方docker-image絕不會有版本問題) [TensorRT流程圖](#TensorRT流程圖) [版本相容整理(已跑過的)](#版本相容整理已跑過的) [已有環境](#已有環境) [torch2onnx(可自動化，需開發界面)](#torch2onnx可自動化，需開發界面) [光torch.load的output就可以只含模型或你客製化的字典(其中某個你自己命名的key為模型)，要自動化此部分需考慮情況多，暫時不考慮此段自動化](#光torchload的output就可以只含模型或你客製化的字典其中某個你自己命名的key為模型，要自動化此部分需考慮情況多，暫時不考慮此段自動化) [keras2onnx: 目前統一使用tf2onnx即可](#keras2onnx--目前統一使用tf2onnx即可) [tf2onnx: 可轉TensorFlow/Keras/Tensorflow.js/Tflite to ONNX](#tf2onnx--可轉TensorFlowKerasTensorflowjsTflite-to-ONNX) [安裝install](#安裝install) [pb檔轉onnx: 穩定，解決自定義layer問題，盡量先把tf的model存成pb檔](#pb檔轉onnx-穩定，解決自定義layer問題，盡量先把tf的model存成pb檔) [h5檔轉onnx: 自定義的layer在model載入時可能會報錯，在當初產生h5檔時，就要驗證自定義載入問題](#h5檔轉onnx-自定義的layer在model載入時可能會報錯，在當初產生h5檔時，就要驗證自定義載入問題) [Caffe2onnx](#Caffe2onnx) [onnx visualize](#onnx-visualize) [onnx2trt](#onnx2trt) [Run in docker container](#Run-in-docker-container) [onnx2trt by trtexec(可自動化，需開發界面)](#onnx2trt-by-trtexec可自動化，需開發界面) [inference(有條件自動化，需定義規則)](#inference有條件自動化，需定義規則) [multi threads](#multi-threads)