---
title: ONNX：高滲透，跨平臺推理
titleSuffix: Azure Machine Learning
description: 瞭解使用開放神經網路交換 （ONNX） 如何説明優化機器學習模型的推理。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270169"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX 和 Azure 機器學習：創建並加速 ML 模型

瞭解使用[開放神經網路交換](https://onnx.ai)（ONNX） 如何説明優化機器學習模型的推理。 推理或模型評分是部署模型用於預測的階段，最常見的是生產資料。 

優化機器學習模型進行推理（或模型評分）非常困難，因為您需要調整模型和推理庫以充分利用硬體功能。 如果你想在不同類型的平臺上（雲/邊緣、CPU/GPU等）獲得最佳性能，這個問題就變得極其困難，因為每個平臺都有不同的功能和特性。 如果具有需要在各種平臺上運行的各種框架的模型，則複雜性會增加。 優化框架和硬體的所有不同組合非常耗時。 需要一種解決方案，用於在首選框架中培訓一次，並在雲或邊緣的任何位置運行。 這就是 ONNX 的用處。

Microsoft 和合作夥伴社區創建了 ONNX，作為表示機器學習模型的開放標準。 來自[許多框架](https://onnx.ai/supported-tools)的模型，包括 TensorFlow、PyTorch、SciKit-Learn、Keras、鏈條、MXNet 和 MATLAB，可以匯出或轉換為標準的 ONNX 格式。 模型採用 ONNX 格式後，可以在各種平臺和設備上運行。

[ONNX 運行時](https://github.com/Microsoft/onnxruntime)是一種高性能推理引擎，用於將 ONNX 型號部署到生產。 它針對雲和邊緣進行了優化，適用于 Linux、Windows 和 Mac。 它C++編寫，它還具有 C、Python 和 C# API。 ONNX 運行時支援所有 ONNX-ML 規範，並在不同硬體（如 NVidia GPU 上的 TensorRT）上與加速器集成。

ONNX 運行時用於大規模 Microsoft 服務，如必應、Office 和認知服務。 性能提升取決於許多因素，但這些微軟服務在__CPU 上的平均性能提升__為 2 倍。 ONNX 運行時也用作數以億計設備上的 Windows ML 的一部分。 可以將運行時與 Azure 機器學習一起使用。 通過使用 ONNX 運行時，您可以受益于廣泛的生產級優化、測試和持續改進。

[![ONNX 流程圖，顯示培訓、轉換器和部署](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>取得 ONNX 模型

您可以透過數種方式來取得 ONNX 模型：
+ 在 Azure 機器學習中訓練新的 ONNX 模型（請參閱本文底部的示例）
+ 將現有模型從另一種格式轉換為 ONNX（請參閱[教程](https://github.com/onnx/tutorials)） 
+ 從[ONNX 模型動物園](https://github.com/onnx/models)獲取預先訓練的 ONNX 模型（請參閱本文底部的示例）
+ 從 [Azure 自訂視覺服務](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)產生自訂 ONNX 模型 

許多模型（包括圖像分類、物件檢測和文本處理）都可以表示為 ONNX 模型。 但是，某些模型可能無法成功轉換。 如果您遇到這種情況，請在您使用的相應轉換器的 GitHub 中提交問題。 您可以繼續使用現有格式模型，直到問題得到解決。

## <a name="deploy-onnx-models-in-azure"></a>在 Azure 中部署 ONNX 模型

借助 Azure 機器學習，可以部署、管理和監視 ONNX 模型。 使用標準[部署工作流程](concept-model-management-and-deployment.md)和 ONNX Runtime，您可以建立裝載於雲端中的 REST 端點。 請參閱本文末尾的 Jupyter 筆記本示例，親自試用。 

### <a name="install-and-use-onnx-runtime-with-python"></a>使用 Python 安裝和使用 ONNX 運行時

用於 ONNX 運行時的 Python 包PyPi.org[（CPU](https://pypi.org/project/onnxruntime) [、](https://pypi.org) [GPU](https://pypi.org/project/onnxruntime-gpu)） 上可用。 請在安裝前閱讀[系統要求](https://github.com/Microsoft/onnxruntime#system-requirements)。 

 要安裝 Python 的 ONNX 運行時，請使用以下命令之一： 
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


