---
title: ONNX：高效能、跨平臺的推斷
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Open Neural Network Exchange (ONNX) 有助於將機器學習模型的推斷優化。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: 5a1ed6c43e4d3a96e0e19d7be50e1e16bcab780e
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071233"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX 和 Azure Machine Learning：建立及加速 ML 模型

瞭解如何使用 [Open Neural Network Exchange](https://onnx.ai) (ONNX) 有助於將機器學習模型的推斷優化。 推斷或模型計分是部署的模型用於預測的階段，最常見的是生產資料。 

因為您需要微調模型和推斷程式庫，以充分利用硬體功能，所以很難將用於推斷 (或模型計分) 的機器學習模型優化。 如果您想要在不同類型的平臺上取得最佳效能， (雲端/邊緣、CPU/GPU 等 ) ，則問題會變得非常困難，因為每個平臺都有不同的功能和特性。 如果您有需要在各種平臺上執行之各種架構的模型，則會增加複雜性。 將所有不同的架構和硬體組合優化相當耗時。 一種解決方案，可在您慣用的架構中訓練一次，並在雲端或邊緣上的任何位置執行。 這就是 ONNX 的來源。

Microsoft 和合作夥伴的一群 ONNX 建立為代表機器學習模型的開放標準。 [許多](https://onnx.ai/supported-tools)架構（包括 TensorFlow、PyTorch、Scikit-learn、學習、Keras、Chainer、MXNET、MATLAB 和 SparkML）的模型都可以匯出或轉換成標準 ONNX 格式。 一旦模型採用 ONNX 格式，就可以在各種不同的平台和裝置上執行。

[ONNX 運行](https://onnxruntime.ai) 時間是一個高效能的推斷引擎，可將 ONNX 模型部署到生產環境。 它已針對雲端和邊緣優化，並可在 Linux、Windows 和 Mac 上運作。 它以 c + + 撰寫，也有 C、Python、c #、JAVA 和 JAVAscript ( # A0) Api，可用於各種環境中的使用方式。 ONNX Runtime 支援 DNN 和傳統 ML 模型，並與不同硬體上的加速器整合，例如 NVidia Gpu 上的 TensorRT、Intel 處理器上的 OpenVINO、Windows 上的 DirectML 等等。 藉由使用 ONNX 執行時間，您可以受益于大量的生產等級優化、測試和持續改進。

ONNX Runtime 用於大規模的 Microsoft 服務，例如 Bing、Office 和 Azure 認知服務。 效能提升取決於許多因素，但這些 Microsoft 服務已看到 __CPU 的平均2倍效能提升__。 除了 Azure Machine Learning 服務之外，ONNX Runtime 也會在支援 Machine Learning 工作負載的其他產品中執行，包括：
+ Windows：執行時間內建于 windows 中，做為 [windows Machine Learning](/windows/ai/windows-ml/) 的一部分，並在數百個裝置上執行。 
+ Azure SQL 產品系列：對 [AZURE Sql Edge](../azure-sql-edge/onnx-overview.md) 和 [azure sql 受控執行個體](../azure-sql/managed-instance/machine-learning-services-overview.md)中的資料執行原生評分。
+ ML.NET： [在 ML.NET 中執行 ONNX 模型](/dotnet/machine-learning/tutorials/object-detection-onnx)。


[![顯示定型、轉換器和部署的 ONNX 流程圖](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>取得 ONNX 模型

您可以透過數種方式來取得 ONNX 模型：
+ 在 Azure Machine Learning 中訓練新的 ONNX 模型 (請參閱本文底部的範例) 或使用自動化的 [Machine Learning 功能](concept-automated-ml.md#automl--onnx)
+ 將現有模型從另一個格式轉換成 ONNX (請參閱 [教學](https://github.com/onnx/tutorials) 課程)  
+ 從 [ONNX Model Zoo](https://github.com/onnx/models) 取得預先定型的 ONNX 模型
+ 從 [Azure 自訂視覺服務](../cognitive-services/custom-vision-service/index.yml)產生自訂 ONNX 模型 

許多模型（包括影像分類、物件偵測和文字處理）可以表示為 ONNX 模型。 如果您遇到無法成功轉換之模型的問題，請在您所使用之個別轉換器的 GitHub 中提出問題。 您可以繼續使用現有的格式模型，直到問題解決為止。

## <a name="deploy-onnx-models-in-azure"></a>在 Azure 中部署 ONNX 模型

您可以使用 Azure Machine Learning 部署、管理和監視 ONNX 模型。 使用標準[部署工作流程](concept-model-management-and-deployment.md)和 ONNX Runtime，您可以建立裝載於雲端中的 REST 端點。 請參閱本文結尾處的範例 Jupyter 筆記本，親自試試看。 

### <a name="install-and-use-onnx-runtime-with-python"></a>使用 Python 安裝和使用 ONNX 執行時間

適用于 ONNX 執行時間的 Python 套件可在 [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime)、 [GPU](https://pypi.org/project/onnxruntime-gpu)) 上取得。 請先閱讀 [系統需求](https://github.com/Microsoft/onnxruntime#system-requirements) 再進行安裝。 

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
請參閱 [如何使用 azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) 來建立和部署 onnx 模型的範例 Python 筆記本。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

您可以在 [ONNX Runtime GitHub](https://github.com/microsoft/onnxruntime/tree/master/samples)中找到其他語言的用法範例。

## <a name="more-info"></a>其他資訊

深入瞭解 **ONNX** 或參與專案：
+ [ONNX 專案網站](https://onnx.ai)
+ [GitHub 上的 ONNX 程式碼](https://github.com/onnx/onnx)

深入瞭解 **ONNX 運行** 時間或參與專案：
+ [ONNX 執行時間專案網站](https://onnxruntime.ai)
+ [ONNX Runtime GitHub 存放庫](https://github.com/Microsoft/onnxruntime)