---
title: 訓練 scikit-learn-瞭解機器學習模型
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 如何讓您使用彈性雲端計算資源來擴充 scikit-learn 學習訓練作業。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e80f33e6c36e1525eff954376d17c8a8b76204cb
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796018"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>訓練 scikit-learn-利用 Azure Machine Learning 瞭解大規模模型

在本文中，您將瞭解如何使用 Azure Machine Learning 來執行 scikit-learn 學習訓練腳本。

本文中的範例腳本可用來將鳶尾花花卉影像分類，以根據 scikit-learn 學習的 [鳶尾花資料集](https://archive.ics.uci.edu/ml/datasets/iris)來建立機器學習模型。

無論您是從頭開始訓練 machine learning scikit-learn-學習模型，或是將現有的模型帶入雲端，您都可以使用 Azure Machine Learning，利用彈性的雲端計算資源來擴充開放原始碼訓練作業。 您可以使用 Azure Machine Learning 來建立、部署、版本和監視生產等級的模型。

## <a name="prerequisites"></a>必要條件

在下列任一環境中執行此程式碼：
 - Azure Machine Learning 計算執行個體 - 不需要下載或安裝

    - 完成 [教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)  ，以建立使用 SDK 預先載入的專用筆記本伺服器和範例存放庫。
    - 在筆記本伺服器的 [範例訓練] 資料夾中，流覽至下列目錄以找出已完成和展開的筆記本： **如何使用 azureml > ml-架構 > scikit-learn-瞭解 > 訓練-超參數-sklearn** 資料夾。

 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) ( # B0 = 1.13.0) 。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。

## <a name="set-up-the-experiment"></a>設定實驗

本節會透過載入所需的 Python 套件、將工作區初始化、定義定型環境，以及準備定型腳本，來設定定型實驗。

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure Machine Learning 工作區](concept-workspace.md)是服務的最上層資源。 其可提供集中式位置以處理您建立的所有成品。 在 Python SDK 中，您可以藉由建立物件來存取工作區成品 [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) 。

從「 `config.json` [必要條件」一節](#prerequisites)中建立的檔案建立工作區物件。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>準備腳本

在本教學課程中，[我們](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)已在此為您提供 **train_iris .py** 的訓練腳本。 在實務上，您應該能夠採用任何自訂定型腳本，並使用 Azure ML 執行它，而不需要修改您的程式碼。

附註：
- 提供的定型腳本會示範如何使用腳本中的物件，將某些計量記錄到您的 Azure ML 執行 `Run` 。
- 提供的定型腳本會使用來自函數的範例資料  `iris = datasets.load_iris()` 。  若要使用及存取您自己的資料，請參閱 [如何使用資料集進行定型](how-to-train-with-datasets.md) ，以在定型期間提供資料。

### <a name="define-your-environment"></a>定義您的環境

若要定義 Azure ML [環境](concept-environments.md) 以封裝定型腳本的相依性，您可以定義自訂環境，或使用和 Azure ml 策劃環境。

#### <a name="use-a-curated-environment"></a>使用策劃環境
如果您不想要定義自己的環境，Azure ML 可選擇性地提供預建的策劃環境。 如需詳細資訊，請參閱 [這裡](resource-curated-environments.md)。
如果您想要使用策劃環境，您可以改為執行下列命令：

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>建立自訂環境

您也可以建立自己的自訂環境。 在 YAML 檔案中定義您的 conda 相依性;在此範例中，會將檔案命名為 `conda_dependencies.yml` 。

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

從這個 Conda 環境規格建立 Azure ML 環境。 環境會在執行時間封裝至 Docker 容器。
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

如需有關建立和使用環境的詳細資訊，請參閱 [在 Azure Machine Learning 中建立和使用軟體環境](how-to-use-environments.md)。

## <a name="configure-and-submit-your-training-run"></a>設定並提交定型回合

### <a name="create-a-scriptrunconfig"></a>建立 ScriptRunConfig
建立 ScriptRunConfig 物件，以指定定型作業的組態詳細資料，包括您的定型指令碼、要使用的環境，以及要在其上執行的計算目標。
如果您在參數中指定，將會透過命令列傳遞定型腳本的任何引數 `arguments` 。

下列程式碼會設定 ScriptRunConfig 物件，以提交您的作業以便在本機電腦上執行。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

如果您想要改為在遠端叢集上執行作業，您可以將所需的計算目標指定為 `compute_target` ScriptRunConfig 的參數。

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>提交您的執行
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning 藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的機密資料，請使用 [. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 檔案，或不要將它包含在來原始目錄中。 相反地，請使用 Azure ML [資料集](how-to-train-with-datasets.md)來存取您的資料。

### <a name="what-happens-during-run-execution"></a>執行期間發生的情況
執行執行時，它會經歷下列階段：

- **準備**：根據定義的環境建立 docker 映射。 映射上傳至工作區的容器登錄，並快取以供稍後執行。 記錄也會串流至執行歷程記錄，並可加以查看以監視進度。 如果改為指定策劃環境，則會使用支援該策劃環境的快取映射。

- **調整**：如果 Batch AI 叢集需要更多節點來執行執行比目前可用的節點，則叢集會嘗試擴大規模。

- 執行 **中：腳本** 資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區，並 `script` 執行。 Stdout 和 **./logs** 資料夾的輸出會串流處理至執行歷程記錄，並可用來監視執行。

- **後續處理**：執行的 **./outputs** 資料夾會複製到執行歷程記錄。

## <a name="save-and-register-the-model"></a>儲存並註冊模型

將模型定型之後，您可以將其儲存並註冊到您的工作區。 註冊模型可讓您在工作區中儲存模型並設定其版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

將下列程式碼新增至您的定型腳本 train_iris .py，以儲存模型。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

使用下列程式碼，將模型註冊到您的工作區。 藉由指定參數 `model_framework` 、 `model_framework_version` 和 `resource_configuration` ，將無法使用任何程式碼模型部署。 無程式碼模型部署可讓您直接將模型從已註冊的模型部署為 web 服務，而 [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?preserve-view=true&view=azure-ml-py) 物件會定義 web 服務的計算資源。

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

您剛剛註冊的模型可以像 Azure ML 中的任何其他已註冊模型一樣地部署。 部署如何包含註冊模型的區段，但您可以直接跳到建立部署的 [計算目標](how-to-deploy-and-where.md#choose-a-compute-target) ，因為您已經有已註冊的模型。

### <a name="preview-no-code-model-deployment"></a> (Preview) 無程式碼模型部署

除了傳統的部署路由之外，您也可以使用無程式碼部署功能 (preview) 進行 scikit-learn-學習。 所有內建 scikit-learn 學習模型類型都支援無程式碼模型部署。 藉由使用、和參數來註冊您的模型（如上所示 `model_framework` `model_framework_version` `resource_configuration` ），您可以直接使用靜態函式 [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 來部署模型。

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
