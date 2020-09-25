---
title: 訓練 scikit-learn-瞭解機器學習模型
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 上執行 scikit-learn 學習訓練腳本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e2f1eb50f6d878eecb4b5c448e683a3024e8c396
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250839"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>組建 scikit-learn-利用 Azure Machine Learning 瞭解大規模模型


在本文中，您將瞭解如何使用 Azure Machine Learning 來執行 scikit-learn 學習訓練腳本。

本文中的範例腳本可用來將鳶尾花花卉影像分類，以根據 scikit-learn 學習的 [鳶尾花資料集](https://archive.ics.uci.edu/ml/datasets/iris)來建立機器學習模型。

無論您是從頭開始訓練 machine learning scikit-learn-學習模型，或是將現有的模型帶入雲端，您都可以使用 Azure Machine Learning，利用彈性的雲端計算資源來擴充開放原始碼訓練作業。 您可以使用 Azure Machine Learning 來建立、部署、版本和監視生產等級的模型。

## <a name="prerequisites"></a>Prerequisites

在下列任一環境中執行此程式碼：
 - Azure Machine Learning 計算執行個體 - 不需要下載或安裝

    - 完成 [教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)  ，以建立使用 SDK 預先載入的專用筆記本伺服器和範例存放庫。
    - 在筆記本伺服器的 [範例訓練] 資料夾中，流覽至下列目錄以找出已完成和展開的筆記本： **如何使用 azureml > ml-架構 > scikit-learn-瞭解 > 訓練 > 超參數-sklearn** 資料夾。

 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。

## <a name="set-up-the-experiment"></a>設定實驗

本節藉由載入所需的 python 套件、初始化工作區、建立實驗，以及上傳定型資料和定型腳本，來設定定型實驗。

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure Machine Learning 工作區](concept-workspace.md)是服務的最上層資源。 其可提供集中式位置以處理您建立的所有成品。 在 Python SDK 中，您可以藉由建立物件來存取工作區成品 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) 。

從「 `config.json` [必要條件」一節](#prerequisites)中建立的檔案建立工作區物件。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>準備腳本

在本教學課程中，[我們](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)已在此為您提供**train_iris .py**的訓練腳本。 在實務上，您應該能夠採用任何自訂定型腳本，並使用 Azure ML 執行它，而不需要修改您的程式碼。

注意：
- 提供的定型腳本會示範如何使用腳本中的物件，將某些計量記錄到您的 Azure ML 執行 `Run` 。
- 提供的定型腳本會使用來自函數的範例資料  `iris = datasets.load_iris()` 。  針對您自己的資料，您可能需要使用 [上傳資料集和腳本](how-to-train-keras.md#data-upload) 等步驟，讓資料在定型期間可供使用。

### <a name="define-your-environment"></a>定義您的環境。

#### <a name="create-a-custom-environment"></a>建立自訂環境。

撰寫您的 conda 環境 (sklearn-env yml) 。
若要從筆記本寫入 conda 環境，您可以 ```%%writefile sklearn-env.yml``` 在儲存格頂端新增這一行。

```yaml
name: sklearn-training-env
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

從這個 Conda 環境規格建立 Azure ML 環境。 環境會在執行時間封裝至 docker 容器。
```python
from azureml.core import Environment

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
myenv.docker.enabled = True
```

#### <a name="use-a-curated-environment"></a>使用策劃環境
如果您不想要建立自己的映射，Azure ML 會提供預先建立的策劃容器環境。 如需詳細資訊，請參閱 [這裡](resource-curated-environments.md)。
如果您想要使用策劃環境，您可以改為執行下列命令：

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>建立 ScriptRunConfig

此 ScriptRunConfig 會提交您的作業，以便在本機計算目標上執行。

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
sklearnconfig.run_config.environment = myenv
```

如果您想要針對遠端叢集進行提交，可以將 run_config 變更為所需的計算目標。

### <a name="submit-your-run"></a>提交您的執行
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure Machine Learning 藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的機密資料，請使用 [. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 檔案，或不要將它包含在來原始目錄中。 相反地， [使用資料存放](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)區存取您的資料。

如需自訂 Python 環境的詳細資訊，請參閱[建立和管理用於訓練和部署的環境](how-to-use-environments.md)。 

## <a name="what-happens-during-run-execution"></a>執行期間發生的情況
執行執行時，它會經歷下列階段：

- **準備**：根據 TensorFlow 估算器建立 docker 映射。 映射上傳至工作區的容器登錄，並快取以供稍後執行。 記錄也會串流至執行歷程記錄，並可加以查看以監視進度。

- **調整**：如果 Batch AI 叢集需要更多節點來執行執行比目前可用的節點，則叢集會嘗試擴大規模。

- **執行中：腳本**資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區，並執行 entry_script。 Stdout 和./logs 資料夾的輸出會串流處理至執行歷程記錄，並可用來監視執行。

- **後續處理**：執行的./outputs 資料夾會複製到執行歷程記錄。

## <a name="save-and-register-the-model"></a>儲存並註冊模型

將模型定型之後，您可以將其儲存並註冊到您的工作區。 註冊模型可讓您在工作區中儲存模型並設定其版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

將下列程式碼新增至您的定型腳本 train_iris .py，以儲存模型。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

使用下列程式碼，將模型註冊到您的工作區。 藉由指定參數 `model_framework` 、 `model_framework_version` 和 `resource_configuration` ，將無法使用任何程式碼模型部署。 無程式碼模型部署可讓您直接將模型從已註冊的模型部署為 web 服務，而 [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py&preserve-view=true) 物件會定義 web 服務的計算資源。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>部署

您剛剛註冊的模型可以與 Azure Machine Learning 中的任何其他已註冊模型完全相同，無論您使用何種估算器來進行定型。 部署如何包含註冊模型的區段，但您可以直接跳到建立部署的 [計算目標](how-to-deploy-and-where.md#choose-a-compute-target) ，因為您已經有已註冊的模型。

### <a name="preview-no-code-model-deployment"></a> (Preview) 無程式碼模型部署

除了傳統的部署路由之外，您也可以使用無程式碼部署功能 (preview) 進行 scikit-learn-學習。 所有內建 scikit-learn 學習模型類型都支援無程式碼模型部署。 藉由使用、和參數來註冊您的模型（如上所示 `model_framework` `model_framework_version` `resource_configuration` ），您可以直接使用靜態函式 [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 來部署模型。

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

注意：這些相依性包含在預先建立的 scikit-learn 學習推斷容器中。

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

[完整的作法涵蓋了 Azure Machine Learning](how-to-deploy-and-where.md)更深入的部署。


## <a name="next-steps"></a>後續步驟

在本文中，您已訓練並註冊 scikit-learn 學習模型，並瞭解部署選項。 若要深入瞭解 Azure Machine Learning，請參閱這些其他文章。

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [Azure 中分散式深度學習訓練的參考架構](/azure/architecture/reference-architectures/ai/training-deep-learning)
