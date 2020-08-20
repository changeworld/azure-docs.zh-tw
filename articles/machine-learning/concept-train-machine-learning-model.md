---
title: 建置模型並加以定型
titleSuffix: Azure Machine Learning
description: 了解各種可讓您使用 Azure Machine Learning 來將模型定型的方法。 估算器可讓您輕鬆地使用 Scikit-learn、TensorFlow、Keras、PyTorch 和 Chainer 等熱門架構。 Machine Learning 管線可讓您輕鬆地排程自動執行、使用異質計算環境，以及重複使用工作流程的各個部分。 至於回合組態，則可讓您精細地控制定型程序執行所在的計算目標。
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 186839425e6ab2fb5430a82650615425bb93d51a
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651755"
---
# <a name="train-models-with-azure-machine-learning"></a>使用 Azure Machine Learning 將模型定型

Azure Machine Learning 提供數種方式供您將模型定型，從使用 SDK 的 Code First 解決方案，到自動化機器學習和視覺化設計工具等低程式碼解決方案均囊括在內。 請使用下列清單來判斷適合您使用的定型方法：

+ [適用於 Python 的 Azure Machine Learning SDK](#python-sdk)：Python SDK 提供數種可將模型定型的方法，這些方法各有不同的功能。

    | 定型方法 | 描述 |
    | ----- | ----- |
    | [回合組態](#run-configuration) | **一般會用來將模型定型的方法**是使用定型指令碼和回合組態。 回合組態會提供所需資訊，以供您設定用來將模型定型的定型環境。 您可以取得回合組態、定型指令碼和計算目標 (定型環境)，然後執行定型作業。 |
    | [自動化機器學習](#automated-machine-learning) | 自動化機器學習可讓您**在沒有廣泛的資料科學或程式設計知識時將模型定型**。 對於有資料科學和程式設計背景的人員，自動化機器學習可讓其透過將演算法的選取和超參數的微調自動化而節省時間和資源。 使用自動化機器學習時，您不必擔心要如何定義回合組態。 |
    | [估算器](#estimators) | 估算器類別**可讓您輕鬆地根據熱門機器學習架構來將模型定型**。 **Scikit-learn**、**PyTorch**、**TensorFlow**、**Chainer** 和 **Ray RLlib** 均有估算器類別。 另外還有一種泛型估算器，這種估算器可與尚未擁有專用估算器類別的架構搭配運作。 使用估算器時，您不必擔心要如何定義回合組態。 |
    | [機器學習管線](#machine-learning-pipeline) | 管線並非不同的定型方法，而是**使用模組化、可重複使用的步驟來定義工作流程的一種方法**，其可將定型作業納入作為工作流程的一部分。 機器學習管線可支援使用自動化機器學習、估算器和回合組態來將模型定型。 由於管線並非特別聚焦在定型上，因此會使用管線的理由比其他定型方法更為多樣。 一般而言，您可能會在下列情況使用管線：<br>* 您想要**排程自動程序**，例如長時間執行的定型作業或資料準備。<br>* 使用**多個步驟**，這些步驟會跨異質計算資源和儲存位置彼此協調。<br>* 針對重新定型或批次評分等特定案例，使用管線作為**可重複使用的範本**。<br>針對工作流程來* **追蹤資料來源、輸入和輸出並設定其版本**。<br>* 您的工作流程是**由不同小組實作的，而這些小組會獨立處理特定步驟**。 然後，這些步驟可在管線中聯結在一起以實作工作流程。 |

+ [適用于 r 的 AZURE MACHINE LEARNING sdk](#r-sdk)：適用于 r 的 sdk 會使用 reticulate 套件系結至 Azure Machine Learning 的 Python SDK。 這可讓您從任何 R 環境存取在 Python SDK 中實作的核心物件和方法。

+ **設計工具**：Azure Machine Learning 設計工具 (預覽) 可讓您輕鬆地進入機器學習領域來建置概念證明，或讓幾乎沒有程式碼撰寫經驗的使用者進入機器學習領域。 其可讓您使用拖放式的 Web 型 UI 來將模型定型。 您可以將 Python 程式碼作為設計的一部分，或在不撰寫任何程式碼的情況下將模型定型。

+ **CLI**：機器學習 CLI 會提供常見 Azure Machine Learning 工作的命令，而且通常會用於**指令碼編寫和自動化工作**上。 例如，當您建立定型指令碼或管線之後，便可使用 CLI 來按照排程啟動定型回合，或在用於定型的資料檔案有所更新時啟動。 針對將模型定型，其會提供可提交定型作業的命令。 其可使用回合組態或管線來提交作業。

這些定型方法各自可以使用不同類型的計算資源來進行定型。 這些資源統稱為[__計算目標__](concept-azure-machine-learning-architecture.md#compute-targets)。 計算目標可以是本機電腦或雲端資源，例如 Azure Machine Learning Compute、Azure HDInsight 或遠端虛擬機器。

## <a name="python-sdk"></a>Python SDK

適用於 Python 的 Azure Machine Learning SDK 可讓您使用 Azure Machine Learning 來建置及執行機器學習工作流程。 您可以從互動式 Python 工作階段、Jupyter Notebook、Visual Studio Code 或其他 IDE 與服務互動。

* [什麼是適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [安裝/更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [設定 Azure Machine Learning 的開發環境](how-to-configure-environment.md)

### <a name="run-configuration"></a>回合組態

您可以使用 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 來定義使用 Azure Machine Learning 的一般定型作業。 然後再使用回合組態以及用來在計算目標上將模型定型的定型指令碼。

您可以從本機電腦的回合組態開始，然後視需要切換至雲端式計算目標的回合組態。 變更計算目標時只會變更所使用的回合組態。 回合也會記錄定型作業的相關資訊，例如輸入、輸出和記錄。

* [什麼是回合組態？](concept-azure-machine-learning-architecture.md#run-configurations)
* [教學課程：將您的第一個 ML 模型定型](tutorial-1st-experiment-sdk-train.md)
* [範例：將模型定型的 Jupyter Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [操作說明：設定及使用計算目標來將模型定型](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>自動化 Machine Learning

請定義反覆項目、超參數設定、特徵化及其他設定。 在定型期間，Azure Machine Learning 會以平行方式嘗試不同的演算法和參數。 定型作業會在達到您定義的允出準則後停止。 使用估算器時，您不必擔心要如何定義回合組態。

> [!TIP]
> 除了 Python SDK 以外，您也可以透過 [Azure Machine Learning Studio](https://ml.azure.com) 來使用自動化 ML。

* [什麼是自動化機器學習？](concept-automated-ml.md)
* [教學課程：使用自動化機器學習建立第一個分類模型](tutorial-first-experiment-automated-ml.md)
* [教學課程：使用自動化機器學習預測計程車車資](tutorial-auto-train-models.md)
* [範例：自動化機器學習的 Jupyter Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [操作說明：在 Python 中設定自動化 ML 實驗](how-to-configure-auto-train.md)
* [操作說明：將時間序列預測模型自動定型](how-to-auto-train-forecast.md)
* [操作說明：使用 Azure Machine Learning Studio 建立、探索及部署自動化機器學習實驗](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>估算器

估算器可讓您輕鬆地使用熱門的 ML 架構來將模型定型。 如果您使用 **Scikit-learn**、**PyTorch**、**TensorFlow**、**Chainer** 或 **Ray RLlib**，請考慮使用估算器來進行定型。 另外還有一種泛型估算器，這種估算器可與尚未擁有專用估算器類別的架構搭配運作。 使用估算器時，您不必擔心要如何定義回合組態。

* [什麼是估算器？](concept-azure-machine-learning-architecture.md#estimators)
* [教學課程：使用 Azure Machine Learning，搭配 MNIST 資料和 scikit-learn 定型映像分類模型](tutorial-train-models-with-aml.md)
* [範例：使用估算器的 Jupyter Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [操作說明：在定型中建立估算器](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>機器學習管線

機器學習管線可以使用先前提到的定型方法 (回合組態、估算器和自動化機器學習)。 管線更常用來建立工作流程，因此其所包含的功能不只是將模型定型而已。 在管線中，您可以使用自動化機器學習、估算器或回合組態來將模型定型。

* [Azure Machine Learning 中的 ML 管線是什麼？](concept-ml-pipelines.md)
* [使用 Azure Machine Learning SDK 來建立及執行機器學習管線](how-to-create-your-first-pipeline.md)
* [教學課程：使用 Azure Machine Learning 管線來進行批次評分](tutorial-pipeline-batch-scoring-classification.md)
* [範例：機器學習管線的 Jupyter Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [範例：管線與自動化機器學習](https://aka.ms/pl-automl)
* [範例：管線與估算器](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>瞭解當您提交定型作業時會發生什麼事

Azure 訓練生命週期是由下列各項所組成：

1. 壓縮專案資料夾中的檔案，並忽略_amlignore_或 _. .gitignore_中所指定的檔案。
1. 擴充您的計算叢集 
1. 建立或下載 dockerfile 至計算節點 
    1. 系統會計算下列雜湊： 
        - 基底映射 
        - 自訂 docker 步驟 (參閱 [使用自訂 docker 基底映射部署模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image)) 
        - Conda 定義 YAML (參閱 [在 Azure Machine Learning 中建立 & 使用軟體環境](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments)) 
    1. 系統會使用此雜湊作為 (ACR Azure Container Registry 工作區查閱中的索引鍵) 
    1. 如果找不到，則會在全域 ACR 中尋找相符的
    1. 如果找不到，系統會建立新的映射， (將會快取並向工作區 ACR 註冊) 
1. 將壓縮的專案檔案下載至計算節點上的暫存儲存體
1. 解壓縮專案檔
1. 執行中的計算節點 `python <entry script> <arguments>`
1. 將記錄、模型檔案和其他寫入的檔案儲存到 `./outputs` 與工作區相關聯的儲存體帳戶
1. 縮小計算範圍，包括移除暫存儲存體 

如果您選擇在本機電腦上定型 ( 「設定為本機執行」 ) ，則不需要使用 Docker。 如果您選擇 (請參閱 [設定 ML 管線](https://docs.microsoft.com/azure/machine-learning/how-to-debug-pipelines#configure-ml-pipeline ) 以取得範例) 一節，您可以在本機使用 Docker。

## <a name="r-sdk"></a>R SDK

R SDK 可讓您搭配使用 R 語言與 Azure Machine Learning。 SDK 會使用網狀套件來繫結至 Azure Machine Learning 的 Python SDK。 這可讓您從任何 R 環境存取 Python SDK 中所執行的核心物件和方法。

如需詳細資訊，請參閱下列文章：

* [教學課程：建立羅吉斯迴歸模型](tutorial-1st-r-experiment.md)
* [適用於 R 的 Azure Machine Learning SDK 參考](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 設計工具

設計工具可讓您在網頁瀏覽器中使用拖放介面來定型模型。

+ [什麼是設計工具？](concept-designer.md)
+ [教學課程：預測汽車價格](tutorial-designer-automobile-price-train-score.md)
+ [迴歸：預測價格](how-to-designer-sample-regression-automobile-price-basic.md)
+ [分類：預測收入](how-to-designer-sample-classification-predict-income.md)
+ [分類：預測流失、購買意願和向上銷售](how-to-designer-sample-classification-churn.md)
+ [使用自訂 R 指令碼進行分類：預測航班誤點](how-to-designer-sample-classification-flight-delay.md)
+ [文字分類：Wikipedia SP 500 資料集](how-to-designer-sample-text-classification.md)

## <a name="many-models-solution-accelerator"></a>多種模型解決方案加速器

[多種模型解決方案加速器](https://aka.ms/many-models) (預覽) 建置在 Azure Machine Learning 中，可讓您定型、操作及管理上百個或甚至數千個機器學習模型。

例如，在下列情況中，__針對每個執行個體或個人__建置模型可以改善結果：

* 預測個別商店的銷售額
* 對數百個油井進行預測性維護
* 為個別使用者量身打造體驗。

如需詳細資訊，請參閱 GitHub 上的 [Many Models Solution Accelerator](https://aka.ms/many-models) \(英文\)。

## <a name="cli"></a>CLI

機器學習 CLI 是 Azure CLI 的擴充功能。 其提供跨平台 CLI 命令供您使用 Azure Machine Learning。 一般而言，您會使用 CLI 來將工作自動化，例如將機器學習模型定型。

* [使用適用於 Azure Machine Learning 的 CLI 擴充功能](reference-azure-machine-learning-cli.md)
* [Azure 上的 MLOps](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS 程式碼

您可以使用 VS Code 擴充功能來執行和管理定型作業。 若要深入瞭解，請參閱 [VS Code 資源管理操作指南](how-to-manage-resources-vscode.md#experiments) 。

## <a name="next-steps"></a>後續步驟

了解如何[設定定型環境](how-to-set-up-training-targets.md)。
