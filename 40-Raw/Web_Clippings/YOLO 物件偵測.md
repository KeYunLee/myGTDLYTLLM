---
title: "YOLO 物件偵測"
source: "https://hackmd.io/Yf6jQ3aPQIm5BCXNPMLbRw"
author:
published:
created: 2026-09-04
description:
tags:
  - "clippings"
---
[Ultralytics YOLO11](https://docs.ultralytics.com/zh/models/yolo11/)  
[Ultralytics 文档：将 YOLO11 与 SAHI 结合使用于切片推理](https://docs.ultralytics.com/zh/guides/sahi-tiled-inference/)  
[SAHI: Slicing Aided Hyper Inference](https://github.com/obss/sahi)

## SAHI能做甚麼

SAHI (Slicing Aided Hyper Inference) 是一個專門為了解決「高解析度圖像中小物件偵測」問題而設計的輕量級視覺庫。  
針對您的問題，SAHI 主要支援的\*\*任務類型（Task Types）\*\*如下：

1. 核心支援的模型類型  
	SAHI 的核心設計邏輯是「切片（Slicing）-> 推論（Inference）-> 合併（Merging/NMS）」，這種邏輯主要適用於以下兩類模型：  
	• 物件偵測模型 (Object Detection Models)：  
	o 這是 SAHI 最主要的用途。  
	o 支援程度： 極高 (Primary Support)。  
	o 功能： 它將大圖切成小圖進行偵測，然後將偵測到的 Bounding Boxes (邊界框) 映射回原圖座標並進行非極大值抑制 (NMS) 合併。  
	o 適用情境： 空拍圖偵測車輛、監控畫面偵測行人、PCB 瑕疵檢測。  
	• 實例分割模型 (Instance Segmentation Models)：  
	o SAHI 也完全支援實例分割。  
	o 支援程度： 高 (Full Support)。  
	o 功能： 除了處理邊界框外，它也能處理分割遮罩 (Masks)。當多個切片中的遮罩重疊時，它會進行合併處理。  
	o 適用情境： 農業病害葉片分割、衛星影像建築物分割。
2. 不支援或非主要設計的類型  
	• 圖像分類模型 (Image Classification Models)：  
	o 不支援 (Not Supported)。  
	o 原因： SAHI 的機制是「找出物體在哪裡（座標）」，而分類模型是回答「這整張圖是什麼」。SAHI 依賴模型的輸出包含座標資訊 (Box/Mask) 來進行合併，純分類模型沒有這些資訊。  
	• 語義分割模型 (Semantic Segmentation Models)：  
	o 有限/非原生支援 (Limited)。  
	o 雖然概念上可行（切片後拼接），但 SAHI 目前的 API 和後處理邏輯主要針對 Object Detection 和 Instance Segmentation (基於物件的)。如果您需要做大圖的語義分割，通常會直接使用滑動視窗 (Sliding Window) 推論，而非 SAHI 的 NMS 合併邏輯。
3. 具體支援的框架與模型庫 (Frameworks)  
	SAHI 本身不是一個模型，而是一個包裝器 (Wrapper)，它支援掛載多種流行的深度學習框架。只要您的模型屬於上述的「物件偵測」或「實例分割」，且來自以下庫，通常都能直接使用：  
	框架/庫 支援模型範例 備註  
	Ultralytics YOLOv8, YOLO11, YOLOv5 目前最常用的組合，支援偵測與實例分割。  
	MMDetection Faster R-CNN, Mask R-CNN, RetinaNet, Cascade R-CNN 支援 MMDetection 庫中的大部分模型。  
	Hugging Face Transformers (DETR, OwlViT 等) 支援透過 Hugging Face transformers 加載的模型。  
	TorchVision Faster R-CNN, Mask R-CNN, RetinaNet PyTorch 官方視覺庫的模型。  
	Detectron2 Mask R-CNN, Faster R-CNN Facebook (Meta) 的視覺庫。  
	YOLO-NAS YOLO-NAS SuperGradients 的模型。  
	總結  
	如果您主要進行 物件偵測 (Object Detection) 或 實例分割 (Instance Segmentation)，特別是針對高解析度圖片中的微小物件，SAHI 是非常適合的工具。但它不適用於圖像分類任務。

## 圖像分類任務有類似SAHI套件能用嗎?

針對圖像分類模型（Image Classification Models），目前市面上並沒有像 SAHI 那樣「一站式」（切片+推論+結果合併）的知名專用套件。  
這主要是因為分類模型的輸出邏輯比較簡單（輸出機率值），不需要處理複雜的邊界框座標合併（NMS）。  
不過，您完全可以借用現有的切片工具來達成「切片分類（Patch-based Classification）」的流程。以下是幾種最適合的方案：

1. 借用 SAHI 的切片功能 (最推薦，既然您已經在看 SAHI)  
	雖然 SAHI 的推論引擎是給物件偵測用的，但它的切片工具 (slicing) 是獨立的模組，非常好用。您可以用它來幫您切圖，然後自己寫一個簡單的迴圈丟進分類模型。  
	• 優點：不用自己寫繁瑣的 Sliding Window 邏輯，SAHI 幫您處理好重疊率 (overlap) 和邊緣填充。  
	• 作法：
	1. 使用 sahi.slicing.slice\_image 切割圖片。
		2. 取得 slice\_image\_result.images (這是切好的圖檔列表)。
		3. 將這些小圖丟進您的分類模型預測。
		4. 紀錄哪些切片被分類為「目標類別」，即可反推目標在原圖的大致區域（熱區圖概念）。
2. 使用 Patchify (專為切片設計的輕量套件)  
	如果您不想要安裝 SAHI 這麼大的庫，patchify 是目前 Python 社群處理圖像切塊最常用的輕量工具。  
	• 套件名稱：patchify  
	• 優點：極度輕量，專注於將 Numpy array (圖片) 切成 Grid，支援 2D (圖像) 和 3D (Volumetric)。  
	• 適用情境：將大圖切成固定大小（例如 224x224）並保留重疊 (Step size)。
3. 實作邏輯：滑動視窗 (Sliding Window) 分類  
	對於分類模型，通常的「切片推論」流程如下，您可以根據這個邏輯尋找或撰寫工具：
	1. 切片 (Slicing)：將 5000x5000 的大圖，切成數百張 224x224 的小圖（Patch）。
		2. 批次推論 (Batch Inference)：將小圖組成 Batch 丟入分類模型 (ResNet, EfficientNet, ViT 等)。
		3. 過濾 (Filtering)：設定一個閾值 (Threshold)，例如「信心度 > 0.8 才算有」。
		4. 映射 (Mapping)：  
		o 如果第 5 行第 3 列的小圖被分類為「瑕疵」，則原圖對應位置就是瑕疵區域。  
		o 最終結果通常會呈現為一張 Heatmap (熱力圖) 或 Grid Map，而不是精準的 Bounding Box。

技術比較總結  
方案 工具名稱 推薦原因 實作難度  
借用 SAHI sahi.slicing 您已經在研究 SAHI，且它的切圖 API 設計得很好（包含存檔、轉換）。 低  
輕量級專用 patchify 專門做切矩陣的事，比起 SAHI 更單純，適合搭配 Numpy/OpenCV。 低  
深度學習原生 torch.nn.functional.unfold 如果您熟悉 PyTorch tensor 操作，這是最快的方法 (GPU 上直接切)。 高 (需懂 Tensor維度)

專家建議  
如果您做切片分類的目的是為了\*\*「在大圖中找到物體在哪裡」 **：  
• 如果物體特徵明顯且背景單純：切片分類法 (Patch Classification) 是可行的。  
• 如果需要精準定位：這其實就是「物件偵測」的任務。與其用分類模型硬做，不如考慮將資料標註改為 Bounding Box，訓練一個 YOLO 或 Faster-RCNN，然後搭配 SAHI，效果會好非常多。  
我可以為您做的下一步： 您想看一段** 「使用 SAHI 的切片工具 (slice\_image) 來切割圖片，並準備給分類模型使用」\*\*的 Python 程式碼範例嗎？

選擇 Repo