---
title: 記錄 ML 實驗和計量
titleSuffix: Azure Machine Learning
description: 監視您的 Azure ML 實驗並監視執行計量，以強化模型建立程序。 使用 run.log、Run.start_logging 或 ScriptRunConfig 將記錄新增至定型指令碼。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7eaa2fbe6033f801a252f6f2c7afa5eb726bce2f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318240"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>在 Azure ML 定型回合中啟用記錄


Azure Machine Learning Python SDK 可讓您使用預設的 Python 記錄套件和 SDK 特有的功能來記錄即時資訊。 您可以登入本機，並將記錄傳送至入口網站中的工作區。

記錄可協助您診斷錯誤和警告，或追蹤參數和模型效能等效能計量。 在本文中，您將了解如何在下列案例中啟用記錄：

> [!div class="checklist"]
> * 互動式定型工作階段
> * 使用 ScriptRunConfig 提交定型作業
> * Python 原生 `logging` 設定
> * 從其他來源記錄


> [!TIP]
> 本文說明如何監視模型定型程序。 如果您想要從 Azure Machine Learning 監視資源使用量和事件 (例如配額、已完成的定型回合或已完成的模型部署)，請參閱[監視 Azure Machine Learning](monitor-azure-machine-learning.md)。

## <a name="data-types"></a>資料類型

您可以記錄多個資料類型，包括純量值、清單、資料表、影像、目錄等等。 如需詳細資訊和不同資料類型的 Python 程式碼範例，請參閱[回合類別參考頁面](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true)。

## <a name="interactive-logging-session"></a>互動式記錄工作階段

互動式記錄工作階段通常用於筆記本環境。 [Experiment.start_logging()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truestart-logging--args----kwargs-) 方法會啟動互動式記錄工作階段。 工作階段期間記錄的所有計量都會加入實驗的回合記錄中。 [run.complete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truecomplete--set-status-true-) 方法會結束工作階段，並將該回合標示為已完成。

## <a name="scriptrun-logs"></a>ScriptRun 記錄

在本節中，您將了解在使用 ScriptRunConfig 進行設定時所建立的執行程序內，新增記錄程式碼的方法。 您可以使用 [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) 類別來封裝可重複回合的指令碼和環境。 您也可以使用此選項來顯示用於監視的視覺型 Jupyter Notebook 小工具。

這個範例會對 Alpha 值執行參數整理，並使用 [run.log()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truelog-name--value--description----) 方法來擷取結果。

1. 建立包含記錄邏輯 `train.py` 的定型指令碼。

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. 提交要在使用者管理的環境中執行的 ```train.py``` 指令碼。 會提交整個指令碼資料夾以進行定型。

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    `show_output` 參數會開啟詳細資訊記錄，這可讓您查看定型過程的詳細資料，以及任何遠端資源或計算目標的相關資訊。 當您提交實驗時，請使用下列程式碼來開啟詳細資訊記錄。

```python
run = exp.submit(src, show_output=True)
```

您也可以針對產生結果的執行，在 `wait_for_completion` 函式中使用相同參數。

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>原生 Python 記錄

SDK 中的某些記錄可能包含錯誤，其指示您將記錄層級設為 DEBUG。 若要設定記錄層級，請將下列程式碼加入到您的指令碼中。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>其他記錄來源

Azure Machine Learning 也可以在定型期間記錄來自其他來源的資訊，例如自動化機器學習回合，或執行作業的 Docker 容器。 這些記錄不會記載下來，但如果您遇到問題並連絡 Microsoft 支援服務，他們在進行疑難排解時可能會使用這些記錄。

如需 Azure Machine Learning 設計工具中的記錄計量相關資訊，請參閱[如何在設計工具中記錄計量](how-to-track-designer-experiments.md)。

## <a name="example-notebooks"></a>Notebook 範例

下列 Notebook 示範了此文章中說明的概念：
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 Azure Machine Learning，請參閱下列文章：

* 了解如何[在 Azure Machine Learning 設計工具中記錄計量](how-to-track-designer-experiments.md)。

* 關於如何註冊最佳模型，並在教學課程中加以部署的範例，請參閱[使用 Azure Machine Learning 定型映像分類模型](tutorial-train-models-with-aml.md)。
