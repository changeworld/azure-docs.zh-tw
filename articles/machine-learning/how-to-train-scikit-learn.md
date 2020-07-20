---
title: 訓練 scikit-learn-瞭解機器學習模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning SKlearn 估計工具類別，以企業規模執行您的 scikit-learn-學習訓練腳本。 範例腳本會將鳶尾花花卉影像分類，以根據 scikit-learn 學習的鳶尾花資料集來建立機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 525dd90f37175dc5b2b50bc577a5a4f04649555b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146384"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>組建 scikit-learn-以 Azure Machine Learning 大規模學習模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何使用 Azure Machine Learning [SKlearn 估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)類別，以企業規模執行 scikit-learn 學習訓練腳本。 

本文中的範例腳本是用來分類鳶尾花的花卉影像，以根據 scikit-learn 學習的[鳶尾花資料集](https://archive.ics.uci.edu/ml/datasets/iris)來建立機器學習模型。

無論您是從基礎開始訓練機器學習服務 scikit-learn-學習模型，或將現有的模型帶入雲端，您都可以使用 Azure Machine Learning，使用彈性雲端計算資源來相應放大開放原始碼訓練作業。 您可以使用 Azure Machine Learning 來建立、部署、版本及監視生產層級模型。

## <a name="prerequisites"></a>先決條件

在下列任一環境中執行此程式碼：
 - Azure Machine Learning 計算執行個體 - 不需要下載或安裝

    - 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
    - 在筆記本伺服器的 [範例訓練] 資料夾中，流覽至此目錄以尋找已完成和已展開的筆記本：**使用方法 > ml 架構 > scikit-learn-瞭解 > 訓練 > 訓練-超參數-sklearn**資料夾。

 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。
    - 下載資料集和範例腳本檔案 
        - [鳶尾花資料集](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris。 .py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - 您也可以在 GitHub 範例頁面上找到本指南的完整[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。 此筆記本包含擴充的章節，涵蓋智慧型超參數調整，並透過主要度量來抓取最佳模型。

## <a name="set-up-the-experiment"></a>設定實驗

本節會藉由載入所需的 python 套件、將工作區初始化、建立實驗，以及上傳定型資料和定型腳本，來設定定型實驗。

### <a name="import-packages"></a>匯入套件

首先，匯入必要的 Python 程式庫。

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure Machine Learning 工作區](concept-workspace.md)是服務的最上層資源。 其可提供集中式位置以處理您建立的所有成品。 在 Python SDK 中，您可以藉由建立物件來存取工作區構件 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 。

從 `config.json` [必要條件一節](#prerequisites)中建立的檔案建立工作區物件。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>建立機器學習實驗

建立實驗和資料夾，以保存您的訓練腳本。 在此範例中，建立名為 "sklearn-鳶尾花" 的實驗。

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

experiment = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>準備訓練腳本

在本教學課程中，已為您提供訓練腳本**train_iris .py** 。 在實務上，您應該能夠採用任何自訂訓練腳本，並使用 Azure ML 加以執行，而不需要修改您的程式碼。

若要使用「Azure ML 追蹤」和「計量」功能，請在您的訓練腳本內新增少量的 Azure ML 程式碼。  定型腳本**train_iris。 .py**示範如何使用腳本中的物件，將一些計量記錄到您的 Azure ML 執行 `Run` 。

提供的定型腳本會使用來自函數的範例資料 `iris = datasets.load_iris()` 。  針對您自己的資料，您可能需要使用[上傳資料集和腳本](how-to-train-keras.md#data-upload)等步驟，讓資料可在定型期間使用。

將訓練腳本**train_iris .py**複製到您的專案目錄。

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>建立或取得計算目標

建立要在其上執行之 scikit-learn 學習作業的計算目標。 Scikit-learn-瞭解僅支援單一節點，CPU 運算。

下列程式碼會為您的遠端訓練計算資源建立 Azure Machine Learning 受控計算 (AmlCompute) 。 建立 AmlCompute 大約需要5分鐘的時間。 如果具有該名稱的 AmlCompute 已經在您的工作區中，此程式碼將會略過建立進程。

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

如需計算目標的詳細資訊，請參閱[什麼是計算目標一](concept-compute-target.md)文。

## <a name="create-a-scikit-learn-estimator"></a>建立 scikit-learn-學習估計工具

[Scikit-learn-學習估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py)提供簡單的方法，讓您在計算目標上啟動 scikit-learn 學習訓練作業。 它是透過 [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 類別來執行，它可以用來支援單一節點的 CPU 定型。

如果您的訓練腳本需要額外的 pip 或 conda 封裝來執行，您可以藉由透過 `pip_packages` 和引數傳遞它們的名稱，將套件安裝在產生的 docker 映射上 `conda_packages` 。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py',
                    pip_packages=['joblib']
                   )
```

> [!WARNING]
> Azure Machine Learning 會藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的敏感性資料，請使用[. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)檔案，或不要將它包含在來原始目錄中。 相反地，請[使用資料存放](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)區來存取您的資料。

如需自訂 Python 環境的詳細資訊，請參閱[建立和管理用於訓練和部署的環境](how-to-use-environments.md)。 

## <a name="submit-a-run"></a>提交執行

[執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)會在作業執行時和完成後，提供介面給執行歷程記錄。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

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

使用下列程式碼，將模型註冊到您的工作區。 藉由指定 `model_framework` 、和參數， `model_framework_version` `resource_configuration` 就可以使用無程式碼模型部署。 這可讓您直接從已註冊的模型將您的模型部署為 web 服務，而物件則會 [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) 定義 web 服務的計算資源。

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
