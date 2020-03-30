---
title: 模型培訓方法
titleSuffix: Azure Machine Learning
description: 瞭解可用於使用 Azure 機器學習訓練模型的不同方法。 估算器提供了一種使用常用框架的簡便方法，如 Scikit 學習、TensorFlow、Keras、PyTorch 和鏈子。 機器學習管道便於安排無人值守的運行、使用異構計算環境以及重用工作流的某些部分。 回合組態提供對訓練過程運行的計算目標的精細控制。
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 68ad9cc47d68f7bc3ae952f7e458781cdc1c4ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129762"
---
# <a name="train-models-with-azure-machine-learning"></a>使用 Azure Machine Learning 將模型定型

Azure 機器學習提供了幾種培訓模型的方法，從使用 SDK 的代碼優先解決方案到低代碼解決方案（如自動機器學習和視覺化設計器）。 使用以下清單確定哪種培訓方法適合您：

+ [用於 Python](#python-sdk)的 Azure 機器學習 SDK ： Python SDK 提供了多種方法來訓練模型，每種方法都具有不同的功能。

    | 培訓方法 | 描述 |
    | ----- | ----- |
    | [回合組態](#run-configuration) | **訓練模型的一般方法是**使用訓練腳本並回合組態。 回合組態提供配置用於訓練模型的訓練環境所需的資訊。 您可以採用回合組態、訓練腳本和計算目標（培訓環境）並運行培訓作業。 |
    | [自動機器學習](#automated-machine-learning) | 自動化機器學習允許您**在沒有廣泛的資料科學或程式設計知識的情況下培訓模型**。 對於具有資料科學和程式設計背景的人，它提供了一種通過自動化演算法選擇和超參數調優來節省時間和資源的方法。 使用自動機器學習時，您不必擔心定義回合組態。 |
    | [估計](#estimators) | 估算器類**使得基於流行的機器學習框架訓練模型變得容易**。 有**估計類的Scikit學習****，PyTorch，TensorFlow**和**鏈子**。 **TensorFlow** 還有一個通用估計器，可用於尚未具有專用估計器類的框架。 使用估計器時，不必擔心定義回合組態。 |
    | [機器學習管道](#machine-learning-pipeline) | 管道不是一種不同的訓練方法，而是**使用模組化、可重用步驟定義工作流的方法**，這些步驟可以包括作為工作流一部分的培訓。 機器學習管道支援使用自動機器學習、估計器和回合組態來訓練模型。 由於管道沒有專門側重于培訓，因此使用管道的原因比其他培訓方法更多樣化。 通常，在：<br>• 您希望**安排無人值守的進程**，如長時間運行的培訓作業或資料準備。<br>• 使用跨異構計算資源和存儲位置協調的**多個步驟**。<br>• 將管道用作特定方案（如重新訓練或批次處理評分）的**可重用範本**。<br>* **跟蹤和版本工作流的資料來源、輸入和輸出**。<br>• 您的工作流**由獨立處理特定步驟的不同團隊實現**。 然後，可以在管道中聯接步驟以實現工作流。 |

+ [用於 Python](#r-sdk)的 Azure 機器學習 SDK ：SDK 使用隔離包綁定到 Azure 機器學習的 Python SDK。 這允許您從任何 R 環境訪問在 Python SDK 中實現的核心物件和方法。

+ **設計器**：Azure 機器學習設計器（預覽版）為構建概念證明或編碼經驗不足的使用者提供了一個輕鬆進入機器學習的入口。 它允許您使用基於 Web 的拖放 UI訓練模型。 您可以將 Python 代碼用作設計的一部分，也可以在不編寫任何代碼的情況下訓練模型。

+ **CLI**：機器學習 CLI 為 Azure 機器學習的常見任務提供了命令，通常用於**編寫腳本和自動化任務**。 例如，創建訓練腳本或管道後，可以使用 CLI 按計劃或更新用於培訓的資料檔案時啟動培訓運行。 對於培訓模型，它提供了提交訓練作業的命令。 它可以使用回合組態或管道提交作業。

每種培訓方法都可以使用不同類型的計算資源進行培訓。 這些資源統稱為[__計算目標__](concept-azure-machine-learning-architecture.md#compute-targets)。 計算目標可以是本地電腦或雲資源，如 Azure 機器學習計算、Azure HDInsight 或遠端虛擬機器。

## <a name="python-sdk"></a>Python SDK

適用于 Python 的 Azure 機器學習 SDK 允許您使用 Azure 機器學習構建和運行機器學習工作流。 您可以通過互動式 Python 會話、猶太筆記本、視覺化工作室代碼或其他 IDE 與服務進行交互。

* [什麼是適用于 Python 的 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [安裝/更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [設定 Azure Machine Learning 的開發環境](how-to-configure-environment.md)

### <a name="run-configuration"></a>回合組態

可以使用[Run 配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)定義 Azure 機器學習的通用培訓作業。 然後，使用回合組態以及訓練腳本在計算目標上訓練模型。

您可以從本地電腦的回合組態開始，然後根據需要切換到基於雲的計算目標配置。 更改計算目標時，您只更改使用的回合組態。 運行還會記錄有關訓練作業的資訊，如輸入、輸出和日誌。

* [什麼是回合組態？](concept-azure-machine-learning-architecture.md#run-configurations)
* [教程：培訓您的第一個 ML 模型](tutorial-1st-experiment-sdk-train.md)
* [示例：訓練模型的 Jupyter 筆記本示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [如何：設置和使用模型培訓的計算目標](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>自動化 Machine Learning

定義反覆運算、超參數設置、壯舉和其他設置。 在培訓期間，Azure 機器學習並行嘗試不同的演算法和參數。 培訓在達到您定義的允出準則後停止。 使用估計器時，不必擔心定義回合組態。

> [!TIP]
> 除了 Python SDK 之外，您還可以通過[Azure 機器學習工作室](https://ml.azure.com)使用自動 ML。

* [什麼是自動化機器學習？](concept-automated-ml.md)
* [教程：使用自動機器學習創建第一個分類模型](tutorial-first-experiment-automated-ml.md)
* [教程：使用自動機器學習預測出租車票價](tutorial-auto-train-models.md)
* [示例：用於自動機器學習的 Jupyter 筆記本示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [如何：在 Python 中配置自動 ML 實驗](how-to-configure-auto-train.md)
* [如何：自動訓練時間序列預測模型](how-to-auto-train-forecast.md)
* [如何：使用 Azure 機器學習工作室創建、探索和部署自動機器學習實驗](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>估計

估算器便於使用流行的 ML 框架訓練模型。 如果你使用的是**Scikit學習****，PyTorch，TensorFlow，** 或**鏈子**，你應該考慮使用估計器進行訓練。 **TensorFlow** 還有一個通用估計器，可用於尚未具有專用估計器類的框架。 使用估計器時，不必擔心定義回合組態。

* [什麼是估算器？](concept-azure-machine-learning-architecture.md#estimators)
* [教程：使用 Azure 機器學習使用 MNIST 資料和學習技術對圖像分類模型進行培訓](tutorial-train-models-with-aml.md)
* [示例：使用估計器的 Jupyter 筆記本示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [如何：在培訓中創建評估器](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>機器學習管道

機器學習管道可以使用前面提到的培訓方法（回合組態、估計器和自動機器學習）。 管道更多的是創建工作流，因此它們不僅僅包括模型培訓。 在管道中，可以使用自動機器學習、估計器或回合組態來訓練模型。

* [Azure 機器學習中的 ML 管道是什麼？](concept-ml-pipelines.md)
* [使用 Azure 機器學習 SDK 創建和運行機器學習管道](how-to-create-your-first-pipeline.md)
* [教程：使用 Azure 機器學習管道進行批次處理評分](tutorial-pipeline-batch-scoring-classification.md)
* [示例：機器學習管道的 Jupyter 筆記本示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [示例：具有自動機器學習的管道](https://aka.ms/pl-automl)
* [示例：帶估計器的管道](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

R SDK 使您能夠將 R 語言與 Azure 機器學習一起使用。 SDK 使用隔離包綁定到 Azure 機器學習的 Python SDK。 這允許您從任何 R 環境訪問在 Python SDK 中實現的核心物件和方法。

如需詳細資訊，請參閱下列文章：

* [教程：創建邏輯回歸模型](tutorial-1st-r-experiment.md)
* [用於 R 參考的 Azure 機器學習 SDK](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 設計工具

設計器允許您在 Web 瀏覽器中使用拖放介面訓練模型。

+ [什麼是設計師？](concept-designer.md)
+ [教程 ： 預測汽車價格](tutorial-designer-automobile-price-train-score.md)
+ [回歸：預測價格](how-to-designer-sample-regression-automobile-price-basic.md)
+ [分類：預測收入](how-to-designer-sample-classification-predict-income.md)
+ [分類：預測流失、動宜和追加銷售](how-to-designer-sample-classification-churn.md)
+ [使用自訂 R 腳本分類：預測航班延誤](how-to-designer-sample-classification-flight-delay.md)
+ [文本分類：維琪百科SP 500資料集](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

機器學習 CLI 是 Azure CLI 的擴展。 它提供了用於使用 Azure 機器學習的跨平臺 CLI 命令。 通常，使用 CLI 自動執行任務，例如訓練機器學習模型。

* [將 CLI 擴展用於 Azure 機器學習](reference-azure-machine-learning-cli.md)
* [Azure 上的 MOP](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>後續步驟

瞭解如何[設置培訓環境](how-to-set-up-training-targets.md)。
