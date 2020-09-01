---
title: 將訓練回合提交至計算目標
titleSuffix: Azure Machine Learning
description: 在各種定型環境上定型您的機器學習模型， (計算目標) 。 您可以輕鬆地在定型環境之間切換。 在本機開始定型作業。 如果您需要擴增，請切換至雲端式計算目標。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: ca76ec5bef1d908ca3cea6ce0f58b1205c1676ca
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144085"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>將訓練回合提交至計算目標

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何使用各種定型環境 ([計算目標](concept-compute-target.md)) 來定型您的機器學習模型。

定型時，通常在本機電腦上啟動，然後在不同的計算目標上執行該定型指令碼。 有了 Azure Machine Learning，您就可以在各種計算目標上執行腳本，而不需要變更定型腳本。

您只需要在 **腳本執行**設定內定義每個計算目標的環境。  然後，當您想要在不同的計算目標上執行定型實驗時，為該計算指定回合組態。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)
* [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine Learning 工作區](how-to-manage-workspace.md)、`ws`
* 計算目標 `my_compute_target` 。  建立具有下列內容的計算目標：
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>什麼是腳本執行設定？

您可以使用 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 物件提交訓練實驗。  此物件包含：

* **source_directory**：包含定型指令碼的來源目錄
* **指令碼**：識別定型指令碼
* **run_config**：回合 [設定，它](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)接著會定義定型的發生位置。 在中， `run_config` 您會指定執行定型腳本時要使用的計算目標和環境。  

## <a name="whats-an-environment"></a>什麼是環境？

Azure Machine Learning [環境](concept-environments.md) 是您機器學習訓練發生所在環境的封裝。 他們會指定您訓練和評分腳本周圍的 Python 套件、環境變數和軟體設定。 它們也會指定執行時間 (Python、Spark 或 Docker) 。  

環境是在中的  `run_config` 物件所指定 `ScriptRunConfig` 。

## <a name="train-your-model"></a><a id="submit"></a>定型模型

提交定型回合的程式碼模式對於所有類型的計算目標皆相同：

1. 建立要執行的實驗
1. 建立將執行腳本的環境
1. 建立參考計算目標和環境的腳本執行設定
1. 提交執行
1. 等待執行完成

或者您可以：

* 使用 `Estimator` 物件提交實驗，如[使用估算程式將 ML 模型定型](how-to-train-ml-models.md)中所示。
* 提交用於[超參數微調](how-to-tune-hyperparameters.md)的 HyperDrive 回合。
* 透過 [VS Code 擴充功能](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)來提交實驗。

## <a name="create-an-experiment"></a>建立實驗

在您的工作區中建立實驗。

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>建立環境

策劃環境包含 Python 套件的集合，預設可在您的工作區中使用。 這些環境是由快取的 Docker 映射所支援，可減少執行準備成本。 針對遠端計算目標，您可以使用下列其中一個常用的策劃環境來開始：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

如需有關環境的詳細資訊和詳細資訊，請參閱 [Azure Machine Learning 中的建立 & 使用軟體環境](how-to-use-environments.md)。
  
### <a name="local-compute-target"></a>本機計算目標

如果您的計算目標是您的 **本機電腦**，您必須負責確保執行腳本的 Python 環境中有所有必要的套件可供使用。  使用您 `python.user_managed_dependencies` 目前的 python 環境 (或您指定) 路徑上的 python。

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>建立腳本執行設定

現在您已有 (`compute_target`) 和環境 () 的計算目標 `my_environment` ，請建立執行定型腳本的腳本回合設定， (`train.py` 位於您目錄中的) `project_folder` ：

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

您也可能想要設定執行的架構。

* 針對 HDI 叢集：
    ```python
    src.run_config.framework = "pyspark"
    ```

* 遠端虛擬機器：
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>提交實驗

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> 當您提交定型回合時，系統會對包含定型指令碼的目錄建立快照集，並傳送到計算目標。 其也會儲存為工作區中實驗的一部分。 如果您變更檔案並再次提交回合，則只會上傳已變更的檔案。
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> 如需快照集的詳細資訊，請參閱 [快照](concept-azure-machine-learning-architecture.md#snapshots)集。


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git 追蹤與整合

當您啟動來源目錄是本機 Git 存放庫的定型回合時，該存放庫的相關資訊會儲存在回合歷程記錄中。 如需詳細資訊，請參閱 [Azure Machine Learning 的 Git 整合](concept-train-model-git-integration.md)。

## <a name="notebook-examples"></a>筆記本範例

如需具有各種計算目標的定型範例，請參閱下列筆記：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* [教學課程：定型模型](tutorial-train-models-with-aml.md)會使用受控計算目標來定型模型。
* 了解如何[有效率地微調超參數](how-to-tune-hyperparameters.md)以便建置更好的模型。
* 擁有定型的模型之後，請了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* 檢視 [RunConfiguration 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK 參考。
* [搭配使用 Azure Machine Learning 與 Azure 虛擬網路](how-to-enable-virtual-network.md)
