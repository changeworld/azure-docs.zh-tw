---
title: 訓練 scikit-learn-瞭解機器學習模型
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 上執行 scikit-learn-學習訓練腳本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d023fa5786375f8fb4dbcdfe01e32da0400088f8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849381"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>組建 scikit-learn-以 Azure Machine Learning 大規模學習模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何使用 Azure Machine Learning 來執行 scikit-learn 學習訓練腳本。

本文中的範例腳本是用來分類鳶尾花的花卉影像，以根據 scikit-learn 學習的[鳶尾花資料集](https://archive.ics.uci.edu/ml/datasets/iris)來建立機器學習模型。

無論您是從基礎開始訓練機器學習服務 scikit-learn-學習模型，或將現有的模型帶入雲端，您都可以使用 Azure Machine Learning，使用彈性雲端計算資源來相應放大開放原始碼訓練作業。 您可以使用 Azure Machine Learning 來建立、部署、版本及監視生產等級模型。

## <a name="prerequisites"></a>必要條件

在下列任一環境中執行此程式碼：
 - Azure Machine Learning 計算執行個體 - 不需要下載或安裝

    - 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
    - 在筆記本伺服器的 [範例訓練] 資料夾中，流覽至此目錄以尋找已完成和已展開的筆記本：**使用方法 > ml 架構 > scikit-learn-瞭解 > 訓練 > 訓練-超參數-sklearn**資料夾。

 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。

## <a name="set-up-the-experiment"></a>設定實驗

本節會藉由載入所需的 python 套件、將工作區初始化、建立實驗，以及上傳定型資料和定型腳本，來設定定型實驗。

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure Machine Learning 工作區](concept-workspace.md)是服務的最上層資源。 其可提供集中式位置以處理您建立的所有成品。 在 Python SDK 中，您可以藉由建立物件來存取工作區構件 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 。

從 `config.json` [必要條件一節](#prerequisites)中建立的檔案建立工作區物件。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>準備腳本

在本教學課程中[，您已](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)在此為您提供訓練腳本**train_iris .py** 。 在實務上，您應該能夠採用任何自訂訓練腳本，並使用 Azure ML 加以執行，而不需要修改您的程式碼。

注意：
- 提供的定型腳本會示範如何使用腳本中的物件，將一些計量記錄到您的 Azure ML 執行 `Run` 。
- 提供的定型腳本會使用來自函數的範例資料 `iris = datasets.load_iris()` 。  針對您自己的資料，您可能需要使用[上傳資料集和腳本](how-to-train-keras.md#data-upload)等步驟，讓資料可在定型期間使用。

### <a name="define-your-environment"></a>定義您的環境。

#### <a name="create-a-custom-environment"></a>建立自訂環境。

撰寫您的 conda 環境 (sklearn-env. yml) 。
若要從筆記本撰寫 conda 環境，您可以在資料 ```%%writefile sklearn-env.yml``` 格的頂端新增這一行。

```yaml
name: sklearn-training-env
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

從這個 Conda 環境規格建立 Azure ML 環境。 環境將會在執行時間封裝成 docker 容器。
```python
from azureml.core import Environment

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
```

#### <a name="use-a-curated-environment"></a>使用策劃環境
如果您不想要建立自己的映射，Azure ML 會提供預先建的策劃容器環境。 如需詳細資訊，請參閱[這裡](resource-curated-environments.md)。
如果您想要使用策劃環境，您可以改為執行下列命令：

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>建立 ScriptRunConfig

此 ScriptRunConfig 會提交您的作業，以便在本機計算目標上執行。

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
src.run_config.environment = myenv
```

如果您想要針對遠端叢集進行提交，您可以將 run_config 變更為所需的計算目標。

### <a name="submit-your-run"></a>提交您的執行
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure Machine Learning 會藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的敏感性資料，請使用[. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)檔案，或不要將它包含在來原始目錄中。 相反地，請[使用資料存放](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)區來存取您的資料。

如需自訂 Python 環境的詳細資訊，請參閱[建立和管理用於訓練和部署的環境](how-to-use-environments.md)。 

## <a name="what-happens-during-run-execution"></a>執行期間所發生的狀況
執行時，它會經歷下列階段：

- **準備**： docker 映射是根據 TensorFlow 估計工具建立的。 映射會上傳至工作區的容器登錄，並快取以供稍後執行。 記錄也會串流處理至執行歷程記錄，並可加以查看以監視進度。

- **調整**：如果 Batch AI 叢集需要執行比目前可用的節點更多的節點，叢集會嘗試相應增加。

- **執行：腳本**資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區，以及執行 entry_script。 Stdout 和./logs 資料夾的輸出會串流處理至執行歷程記錄，並可用來監視執行。

- **後置處理**：執行的./outputs 資料夾會複製到執行歷程記錄。

## <a name="save-and-register-the-model"></a>儲存並註冊模型

將模型定型之後，您可以將其儲存並註冊到您的工作區。 註冊模型可讓您在工作區中儲存模型並設定其版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

將下列程式碼加入您的定型腳本中，train_iris .py，以儲存模型。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

使用下列程式碼，將模型註冊到您的工作區。 藉由指定 `model_framework` 、和參數， `model_framework_version` `resource_configuration` 就可以使用無程式碼模型部署。 無程式碼模型部署可讓您直接從已註冊的模型將您的模型部署為 web 服務，而物件則會 [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) 定義 web 服務的計算資源。

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

您剛註冊的模型可以使用與 Azure Machine Learning 中任何其他已註冊的模型完全相同的方式來部署，不論您用於定型的估計工具為何。 部署如何包含註冊模型的區段，但您可以直接跳到建立部署的[計算目標](how-to-deploy-and-where.md#choose-a-compute-target)，因為您已經有已註冊的模型。

### <a name="preview-no-code-model-deployment"></a> (預覽) 無程式碼模型部署

除了傳統部署路由以外，您也可以使用無程式碼部署功能 (preview) 進行 scikit-learn-學習。 所有內建的 scikit-learn-學習模型類型都不支援任何程式碼模型部署。 藉由使用、和參數來註冊您的模型（如上所示 `model_framework` `model_framework_version` ）， `resource_configuration` 您可以直接使用靜態函式 [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 來部署您的模型。

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

[完整的](how-to-deploy-and-where.md)作法涵蓋 Azure Machine Learning 中的部署更深入。


## <a name="next-steps"></a>後續步驟

在本文中，您已訓練並註冊 scikit-learn 學習模型，並瞭解部署選項。 若要深入瞭解 Azure Machine Learning，請參閱這些其他文章。

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [Azure 中分散式深度學習訓練的參考架構](/azure/architecture/reference-architectures/ai/training-deep-learning)
