---
title: 記錄 ML 實驗和計量
titleSuffix: Azure Machine Learning
description: 監視您的 Azure ML 實驗並監視執行計量，以強化模型建立程序。 使用 run. log、Run. start_logging 或 ScriptRunConfig，將記錄新增至訓練腳本。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f49a66fc6e233658b228b7ad9bacdaa23917afef
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648729"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>在 Azure ML 定型回合中啟用記錄
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning Python SDK 可讓您使用預設的 Python 記錄套件和 SDK 特有的功能來記錄即時資訊。 您可以在本機登入，並將記錄傳送至您在入口網站中的工作區。

記錄可協助您診斷錯誤和警告，或追蹤效能度量，例如參數和模型效能。 在本文中，您將瞭解如何在下列案例中啟用記錄：

> [!div class="checklist"]
> * 互動式訓練課程
> * 使用 ScriptRunConfig 提交定型作業
> * Python 原生 `logging` 設定
> * 來自其他來源的記錄


> [!TIP]
> 本文說明如何監視模型定型流程。 如果您想要從 Azure Machine learning 監視資源使用量和事件（例如配額、已完成的定型回合或完成的模型部署），請參閱 [監視 Azure Machine Learning](monitor-azure-machine-learning.md)。

## <a name="data-types"></a>資料類型

您可以記錄多個資料類型，包括純量值、清單、資料表、影像、目錄等等。 如需詳細資訊，以及適用于不同資料類型的 Python 程式碼範例，請參閱 [執行類別參考頁面](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)。

## <a name="interactive-logging-session"></a>互動式記錄會話

互動式記錄會話通常用於筆記本環境。 方法 [實驗。 start_logging ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) 會啟動互動式記錄會話。 在會話期間記錄的任何計量都會新增至實驗中的執行記錄。 方法 [執行。完成 ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) 結束會話，並將執行標示為已完成。

## <a name="scriptrunconfig-logs"></a>ScriptRunConfig 記錄

在本節中，您將瞭解如何在 ScriptConfig 執行內新增記錄程式碼。 您可以使用 [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 類別來封裝可重複執行的腳本和環境。 您也可以使用此選項來顯示用於監視的 visual Jupyter 筆記本 widget。

此範例會對 Alpha 值執行參數比對，並使用 [ ( # B1 方法的 run. log ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) 來捕捉結果。

1. 建立包含記錄邏輯的定型腳本 `train.py` 。

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. 提交 ```train.py``` 腳本以在使用者管理的環境中執行。 提交整個腳本資料夾以進行定型。

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    `show_output`參數開啟詳細資訊記錄，可讓您查看定型程式的詳細資料，以及任何遠端資源或計算目標的相關資訊。 當您提交實驗時，請使用下列程式碼來開啟詳細資訊記錄。

```python
run = exp.submit(src, show_output=True)
```

您也可以針對產生結果的執行，在 `wait_for_completion` 函式中使用相同參數。

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>原生 Python 記錄

SDK 中的某些記錄可能會包含錯誤，指示您將記錄層級設定為 DEBUG。 若要設定記錄層級，請將下列程式碼加入到您的指令碼中。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>其他記錄來源

Azure Machine Learning 也可以在定型期間記錄來自其他來源的資訊，例如自動化機器學習執行，或執行作業的 Docker 容器。 未記載這些記錄檔，但如果您遇到問題並聯絡 Microsoft 支援服務，他們可能可以在進行疑難排解時使用這些記錄。

如需 Azure Machine Learning 設計工具中記錄計量的詳細資訊 (預覽) ，請參閱 [如何在設計工具中記錄計量 (預覽) ](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Notebook 範例
下列筆記本示範本文中的概念：
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 Azure Machine Learning，請參閱下列文章：

* 瞭解如何 [在 Azure Machine Learning 設計工具中記錄計量 (preview) ](how-to-track-designer-experiments.md)。

* 關於如何註冊最佳模型，並在教學課程中加以部署的範例，請參閱[使用 Azure Machine Learning 定型映像分類模型](tutorial-train-models-with-aml.md)。
