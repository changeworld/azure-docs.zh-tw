---
title: 設定定型回合
titleSuffix: Azure Machine Learning
description: 在各種定型環境上定型您的機器學習模型， (計算目標) 。 您可以輕鬆地在定型環境之間切換。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: a5764a9f230540d58edf71e8c00781e86589aa9a
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070162"
---
# <a name="configure-and-submit-training-runs"></a>設定和提交定型執行

在本文中，您將瞭解如何設定和提交 Azure Machine Learning 回合以訓練您的模型。 程式碼程式碼片段說明設定和提交定型腳本的重要部分。  然後使用其中一個 [範例筆記本](#notebooks) 來尋找完整的端對端運作範例。

定型時，通常會在您的本機電腦上啟動，然後再向外擴充到雲端式叢集。 有了 Azure Machine Learning，您就可以在各種計算目標上執行腳本，而不需要變更定型腳本。

您只需要在 **腳本執行** 設定內定義每個計算目標的環境。  然後，當您想要在不同的計算目標上執行定型實驗時，為該計算指定回合組態。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)
* [適用于 Python 的 AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) ( # B0 = 1.13.0) 
* [Azure Machine Learning 工作區](how-to-manage-workspace.md)、`ws`
* 計算目標 `my_compute_target` 。  [建立計算目標](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>什麼是腳本執行設定？
[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)可用來設定提交定型回合作為實驗一部分的必要資訊。

您可以使用 ScriptRunConfig 物件提交訓練實驗。  此物件包含：

* **source_directory**：包含定型指令碼的來源目錄
* **腳本**：要執行的定型腳本
* **compute_target**：要在其上執行的計算目標
* **環境**：執行腳本時要使用的環境
* 還有一些其他可設定的選項 (如需詳細資訊，請參閱 [參考檔](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)) 

## <a name="train-your-model"></a><a id="submit"></a>定型模型

提交定型回合的程式碼模式對於所有類型的計算目標皆相同：

1. 建立要執行的實驗
1. 建立將執行腳本的環境
1. 建立 ScriptRunConfig，以指定計算目標和環境
1. 提交執行
1. 等待執行完成

或者您可以：

* 提交用於[超參數微調](how-to-tune-hyperparameters.md)的 HyperDrive 回合。
* 透過 [VS Code 擴充功能](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)來提交實驗。

## <a name="create-an-experiment"></a>建立實驗

在您的工作區中建立實驗。

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>選取計算目標

選取您的定型腳本將在其上執行的計算目標。 如果 ScriptRunConfig 中未指定任何計算目標，或者 `compute_target='local'` ，AZURE ML 會在本機執行您的腳本。 

本文中的範例程式碼假設您已從「必要條件」一節中建立計算目標 `my_compute_target` 。

## <a name="create-an-environment"></a>建立環境
Azure Machine Learning [環境](concept-environments.md) 是您機器學習訓練發生所在環境的封裝。 他們會指定您訓練和評分腳本周圍的 Python 套件、Docker 映射、環境變數和軟體設定。 它們也會指定 (Python、Spark 或 Docker) 的執行時間。

您可以定義自己的環境，或使用 Azure ML 策劃環境。 [策劃環境](./how-to-use-environments.md#use-a-curated-environment) 是預先定義的環境，在您的工作區中預設為可用。 這些環境是由快取的 Docker 映射所支援，可減少執行準備成本。 如需可用策劃環境的完整清單，請參閱 [Azure Machine Learning 策劃環境](./resource-curated-environments.md) 。

針對遠端計算目標，您可以使用下列其中一個常用的策劃環境來開始：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

如需有關環境的詳細資訊和詳細資訊，請參閱 [Azure Machine Learning 中的建立 & 使用軟體環境](how-to-use-environments.md)。
  
### <a name="local-compute-target"></a><a name="local"></a>本機計算目標

如果您的計算目標是您的 **本機電腦**，您必須負責確保執行腳本的 Python 環境中有所有必要的套件可供使用。  使用您 `python.user_managed_dependencies` 目前的 python 環境 (或您指定) 路徑上的 python。

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>建立腳本執行設定

現在您已有 (`my_compute_target`) 和環境 () 的計算目標 `myenv` ，請建立執行定型腳本的腳本回合設定， (`train.py` 位於您目錄中的) `project_folder` ：

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

如果您未指定環境，將會為您建立預設環境。

如果您有想要傳遞給定型腳本的命令列引數，您可以透過 ScriptRunConfig 函式的參數指定這些引數 **`arguments`** ，例如。 `arguments=['--arg1', arg1_val, '--arg2', arg2_val]`

如果您想要覆寫執行所允許的預設最大時間，可以透過 **`max_run_duration_seconds`** 參數進行。 如果系統所花費的時間超過此值，系統會嘗試自動取消執行。

### <a name="specify-a-distributed-job-configuration"></a>指定分散式作業設定
如果您想要執行分散式訓練作業，請將分散式工作專屬的設定提供給 **`distributed_job_config`** 參數。 支援的設定類型包括 [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py)、 [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py)和 [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py)。 

如需執行分散式 Horovod、TensorFlow 和 PyTorch 作業的詳細資訊和範例，請參閱：

* [將 TensorFlow 模型定型](./how-to-train-tensorflow.md#distributed-training)
* [將 PyTorch 模型定型](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>提交實驗

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> 當您提交定型回合時，系統會對包含定型指令碼的目錄建立快照集，並傳送到計算目標。 其也會儲存為工作區中實驗的一部分。 如果您變更檔案並再次提交回合，則只會上傳已變更的檔案。
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> 如需快照集的詳細資訊，請參閱 [快照](concept-azure-machine-learning-architecture.md#snapshots)集。

> [!IMPORTANT]
> **特殊資料夾** Azure Machine Learning 會特別對待 *outputs* 與 *logs*, 這兩個資料夾。 在定型期間，當您將檔案寫入到相對於根目錄且名為 *outputs* 與 *logs* 的資料夾 (分別是 `./outputs` 與 `./logs`) 時，這些檔案會自動上傳到執行歷程記錄，因此當您的回合完成之後，您就能存取它們。
>
> 若要在定型期間建立成品 (例如模型檔案、檢查點、資料檔案或繪製的影像)，請將這些成品寫入到 `./outputs` 資料夾。
>
> 同樣地，您也可以將來自您定型回合的任何記錄寫入到 `./logs` 資料夾。 若要利用 Azure Machine Learning 的 [TensorBoard 整合](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb)，請務必將您的 TensorBoard 記錄寫入到此資料夾。 當您的回合在執行時，您將能啟動 TensorBoard 並串流這些記錄。  稍後，您也可以從任何先前的回合還原記錄。
>
> 例如，若要在您的遠端定型回合執行完成之後將寫入到 *outputs* 資料夾的檔案下載到您的本機電腦，請執行：`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git 追蹤與整合

當您啟動來源目錄是本機 Git 存放庫的定型回合時，該存放庫的相關資訊會儲存在回合歷程記錄中。 如需詳細資訊，請參閱 [Azure Machine Learning 的 Git 整合](concept-train-model-git-integration.md)。

## <a name="notebook-examples"></a><a name="notebooks"></a>筆記本範例

請參閱這些筆記本，以取得設定各種定型案例執行的範例：
* [在各種計算目標上定型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [使用 ML framework 定型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>疑難排解

 * **ModuleErrors (沒有名稱為) 的模組**：如果您在 Azure ML 中提交實驗時遇到 ModuleErrors，定型腳本預期會安裝套件，但不會新增套件。 提供套件名稱之後，Azure ML 會在用於定型回合的環境中安裝套件。

    如果您使用估算器來提交實驗，您可以 `pip_packages` `conda_packages` 根據您要安裝套件的來源，在估算器中透過或參數指定封裝名稱。 您也可以使用來指定具有所有相依性的 yml 檔案， `conda_dependencies_file` 或使用參數列出 txt 檔案中的所有 pip 需求 `pip_requirements_file` 。 如果您有自己的 Azure ML 環境物件，而您想要覆寫估算器所使用的預設映射，您可以透過估算器函式的參數來指定該環境 `environment` 。
    
    您可以在 [AzureML 容器](https://github.com/Azure/AzureML-Containers)中看到 Azure ML 維護的 docker 映射及其內容。
    架構特有的相依性會列在個別的架構檔中：
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    
    > [!Note]
    > 如果您認為特定套件在 Azure ML 維護的映射和環境中有很大的不足，請在 [AzureML 容器](https://github.com/Azure/AzureML-Containers)中提出 GitHub 問題。 
 
* **未定義 NameError (名稱) ，AttributeError (物件沒有屬性)**：此例外狀況應來自您的定型腳本。 您可以從 Azure 入口網站查看記錄檔，以取得有關未定義特定名稱或屬性錯誤的詳細資訊。 您可以從 SDK 使用 `run.get_details()` 來查看錯誤訊息。 這也會列出針對您的執行所產生的所有記錄檔。 重新提交您的執行之前，請務必先查看您的訓練腳本並修正錯誤。 


* **執行或實驗刪除**：您可以使用 [實驗.](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) 封存方法來封存實驗，或從 Azure Machine Learning Studio 用戶端的實驗索引標籤中，透過 [封存實驗] 按鈕來封存實驗。 此動作會從清單查詢和 views 中隱藏實驗，但不會將其刪除。

    目前不支援永久刪除個別實驗或執行。 如需有關刪除工作區資產的詳細資訊，請參閱 [匯出或刪除您的 Machine Learning 服務工作區資料](how-to-export-delete-data.md)。

* 計量 **檔太大**： Azure Machine Learning 具有可從定型執行一次記錄的計量物件大小的內部限制。 如果在記錄清單值計量時發生「計量文件太大」錯誤，請嘗試將清單分割為較小的區塊，例如：

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    在內部，Azure ML 會將具有相同計量名稱的區塊串連為連續清單。

* **執行失敗， `jwt.exceptions.DecodeError` 並** 出現：確切的錯誤訊息： `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` 。 
    
    請考慮升級至 azureml 核心的最新版本： `pip install -U azureml-core` 。
    
    如果您在本機執行時遇到此問題，請檢查您的環境中所安裝的 PyJWT 版本，以開始執行。 支援的 PyJWT 版本 < 2.0.0。 如果版本是 >= 2.0.0，請從環境卸載 PyJWT。 您可以檢查 PyJWT 版本、卸載並安裝正確的版本，如下所示：
    1. 啟動命令 shell，並啟用已安裝 azureml 核心的 conda 環境。
    2. 輸入 `pip freeze` 並尋找， `PyJWT` 如果找到，則應 < 列出的版本2.0。0
    3. 如果列出的版本不是支援的版本，請 `pip uninstall PyJWT` 在命令 shell 中輸入 y 確認。
    4. 使用 `pip install 'PyJWT<2.0.0'` 進行安裝
    
    如果您要在執行時提交使用者建立的環境，請考慮在該環境中使用最新版本的 azureml 核心。 版本 >= azureml 核心已釘選 PyJWT < 2.0.0 的1.18.0。 如果您需要在您提交的環境中使用 azureml 核心 < 1.18.0，請務必在您的 pip 相依性中指定 PyJWT < 2.0.0。

* **計算目標需要很長的時間才能啟動**：計算目標的 Docker 映射會從 AZURE CONTAINER REGISTRY (ACR) 載入。 根據預設，Azure Machine Learning 會建立使用 *基本* 服務層級的 ACR。 將工作區的 ACR 變更為「標準」或「進階層」，可減少建立和載入映射所需的時間。 如需詳細資訊，請參閱 [Azure Container Registry 服務層級](../container-registry/container-registry-skus.md)。

## <a name="next-steps"></a>後續步驟

* [教學課程：定型模型](tutorial-train-models-with-aml.md)會使用受控計算目標來定型模型。
* 瞭解如何使用特定的 ML 架構（例如 [scikit-learn-](how-to-train-scikit-learn.md)learning、 [TensorFlow](how-to-train-tensorflow.md)和 [PyTorch](how-to-train-pytorch.md)）來定型模型。
* 了解如何[有效率地微調超參數](how-to-tune-hyperparameters.md)以便建置更好的模型。
* 擁有定型的模型之後，請了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* 查看 [ScriptRunConfig class](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) SDK 參考。
* [搭配使用 Azure Machine Learning 與 Azure 虛擬網路](./how-to-network-security-overview.md)
