---
title: ONNX：高效能、跨平臺的推斷
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Open Neural Network Exchange （ONNX）來協助優化機器學習模型的推斷。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270169"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX 和 Azure Machine Learning：建立並加速 ML 模型

瞭解如何使用[Open Neural Network Exchange](https://onnx.ai) （ONNX）來協助優化機器學習模型的推斷。 推斷（或模型計分）是已部署的模型用於預測的階段，最常見的是生產資料。 

針對推斷（或模型評分）優化機器學習模型很棘手，因為您需要微調模型和推斷程式庫，才能充分利用硬體功能。 如果您想要在不同類型的平臺（雲端/邊緣、CPU/GPU 等）上取得最佳效能，問題就會變得非常困難，因為每個平臺都有不同的功能和特性。 如果您有各種架構需要在各種平臺上執行的模型，則複雜度會增加。 優化所有不同的架構和硬體組合非常耗時。 一種解決方案，可在您慣用的架構中訓練一次，並在雲端或邊緣上的任何位置執行。 這就是 ONNX 的來源。

Microsoft 和合作夥伴所建立的 ONNX，是代表機器學習模型的開放標準。 [許多](https://onnx.ai/supported-tools)架構（包括 TensorFlow、PyTorch、Scikit-learn、Keras、Chainer、MXNET 和 MATLAB）的模型都可以匯出或轉換成標準 ONNX 格式。 一旦模型採用 ONNX 格式，就可以在各種不同的平臺和裝置上執行。

[ONNX Runtime](https://github.com/Microsoft/onnxruntime)是高效能推斷引擎，可將 ONNX 模型部署至生產環境。 它已針對雲端和邊緣進行優化，並可在 Linux、Windows 和 Mac 上運作。 以C++撰寫，它也具有 C、Python 和C# api。 ONNX Runtime 提供所有 ONNX ML 規格的支援，也會與不同硬體上的加速器整合，例如 NVidia Gpu 上的 TensorRT。

ONNX 執行時間用於高級別的 Microsoft 服務，例如 Bing、Office 和認知服務。 效能提升取決於數個因素，但這些 Microsoft 服務已看到 CPU 的__平均2倍效能提升__。 ONNX 執行時間也用來做為數百萬部裝置上 Windows ML 的一部分。 您可以使用執行時間搭配 Azure Machine Learning。 藉由使用 ONNX 執行時間，您可以從大量的生產等級優化、測試和持續改進中獲益。

[顯示訓練、轉換器和部署 ![ONNX 流程圖](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>取得 ONNX 模型

您可以透過數種方式來取得 ONNX 模型：
+ 在 Azure Machine Learning 中訓練新的 ONNX 模型（請參閱本文底部的範例）
+ 將現有的模型從另一種格式轉換為 ONNX （請參閱[教學](https://github.com/onnx/tutorials)課程） 
+ 從[ONNX Model Zoo](https://github.com/onnx/models)取得預先定型的 ONNX 模型（請參閱本文底部的範例）
+ 從 [Azure 自訂視覺服務](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)產生自訂 ONNX 模型 

許多模型（包括影像分類、物件偵測和文字處理）都可以表示為 ONNX 模型。 不過，某些模型可能無法成功轉換。 如果您遇到這種情況，請在您所使用之個別轉換器的 GitHub 中提出問題。 您可以繼續使用現有的格式模型，直到問題解決為止。

## <a name="deploy-onnx-models-in-azure"></a>在 Azure 中部署 ONNX 模型

有了 Azure Machine Learning，您就可以部署、管理和監視 ONNX 模型。 使用標準[部署工作流程](concept-model-management-and-deployment.md)和 ONNX Runtime，您可以建立裝載於雲端中的 REST 端點。 請參閱本文結尾的範例 Jupyter 筆記本，親自試試看。 

### <a name="install-and-use-onnx-runtime-with-python"></a>以 Python 安裝和使用 ONNX 執行時間

ONNX 執行時間的 Python 套件可在[PyPi.org](https://pypi.org) （[CPU](https://pypi.org/project/onnxruntime)、 [GPU](https://pypi.org/project/onnxruntime-gpu)）上取得。 請先閱讀[系統需求](https://github.com/Microsoft/onnxruntime#system-requirements)再安裝。 

 若要安裝適用于 Python 的 ONNX 執行時間，請使用下列其中一個命令： 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

若要在您的 Python 指令碼中呼叫 ONNX Runtime，請使用：    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

模型隨附的文件通常會告訴您使用模型所需的輸入和輸出。 您也可以使用視覺效果工具 (例如 [Netron](https://github.com/lutzroeder/Netron)) 來檢視模型。 ONNX Runtime 也可讓您查詢模型中繼資料、輸入和輸出：    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

若要推斷您的模型，請使用 `run`，傳入您想要系統傳回的輸出清單 (如果您想要傳回全部則留空)，以及傳入輸入值的對應。 結果是輸出清單。  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

如需完整的 Python API 參考，請參閱 [ONNX Runtime 參考文件](https://aka.ms/onnxruntime-python) (英文)。    

## <a name="examples"></a>範例

請參閱 [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx)，以取得建立與部署 ONNX 模型的範例筆記本。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>其他資訊

深入了解 ONNX 或參與專案：
+ [ONNX 專案網站](https://onnx.ai)
+ [GitHub 上的 ONNX 程式碼](https://github.com/onnx/onnx)

深入了解 ONNX Runtime 或參與專案：
+ [ONNX Runtime GitHub 存放庫](https://github.com/Microsoft/onnxruntime)


