---
title: 培訓學習機器學習模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習 SK 學習估算器類在企業規模上運行學習課程培訓腳本。 示例腳本對光圈花圖像進行分類，以構建基於 scikit-learn 的虹膜資料集的機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942253"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>使用 Azure 機器學習大規模構建學習模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，瞭解如何使用 Azure 機器學習[SK 學習估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)類在企業規模上運行 scikit 學習培訓腳本。 

本文中的示例腳本用於對虹膜花圖像進行分類，以構建基於 scikit-learn 的[虹膜資料集](https://archive.ics.uci.edu/ml/datasets/iris)的機器學習模型。

無論您是從零開始訓練機器學習學習模型，還是將現有模型引入雲中，都可以使用 Azure 機器學習來擴展開源培訓作業，使用彈性雲計算資源。 您可以使用 Azure 機器學習構建、部署、版本和監視生產級模型。

## <a name="prerequisites"></a>Prerequisites

在以下任一環境中運行此代碼：
 - Azure 機器學習計算實例 - 無需下載或安裝

    - 完成[教程：設置環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以創建預載入 SDK 和示例存儲庫的專用筆記本伺服器。
    - 在筆記本伺服器上的示例訓練資料夾中，通過導航到此目錄查找已完成和展開的筆記本：**如何使用-azureml > ml 框架>學>訓練>訓練-超參數-調諧-部署-sk-learn**資料夾。

 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [創建工作區設定檔](how-to-configure-environment.md#workspace)。
    - 下載資料集和示例指令檔 
        - [虹膜資料集](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - 您還可以在 GitHub 示例頁面上找到本指南的已完成[的 Jupyter 筆記本版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。 該筆記本包括一個擴展部分，涵蓋智慧超參數調優和按主要指標檢索最佳模型。

## <a name="set-up-the-experiment"></a>設置實驗

本節通過載入所需的 python 包、初始化工作區、創建實驗以及上載訓練資料和培訓腳本來設置訓練實驗。

### <a name="import-packages"></a>匯入套件

首先，導入必要的 Python 庫。

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

[Azure 機器學習工作區](concept-workspace.md)是服務的頂級資源。 它為您提供了一個集中的位置，用於處理您創建的所有專案。 在 Python SDK 中，您可以通過創建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)物件來訪問工作區專案。

從`config.json`[先決條件部分](#prerequisites)中創建的檔創建工作區物件。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>建立機器學習實驗

創建一個實驗和一個資料夾來保存你的訓練腳本。 在此示例中，創建一個名為"學習-虹膜"的實驗。

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>準備培訓腳本

在本教程中，培訓腳本**train_iris.py**已經為您提供。 實際上，您應該能夠像現在一樣獲取任何自訂訓練腳本，並在 Azure ML 上運行它，而無需修改代碼。

要使用 Azure ML 跟蹤和指標功能，請在訓練腳本中添加少量 Azure ML 代碼。  訓練腳本**train_iris.py**演示如何使用腳本中`Run`的物件將某些指標記錄到 Azure ML 運行。

提供的培訓腳本使用函數的示例`iris = datasets.load_iris()`資料。  對於您自己的資料，您可能需要使用["上載資料集"和"腳本](how-to-train-keras.md#data-upload)"等步驟在培訓期間提供資料。

將訓練腳本**train_iris.py**複製到專案目錄中。

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>創建或獲取計算目標

為 scikit 學習作業創建要運行的計算目標。 Scikit 學習僅支援單個節點、CPU 計算。

以下代碼為遠端訓練計算資源創建 Azure 機器學習託管計算 （AmlCompute）。 創建 Aml 計算大約需要 5 分鐘。 如果具有該名稱的 AmlCompute 已在工作區中，則此代碼將跳過創建過程。

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

有關計算目標的詳細資訊，請參閱[什麼是計算目標](concept-compute-target.md)文章。

## <a name="create-a-scikit-learn-estimator"></a>創建一個學習的估算器

[學習器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py)提供了一種在計算目標上啟動學習技術訓練作業的簡單方法。 它通過類實現，[`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)可用於支援單節點 CPU 訓練。

如果訓練腳本需要額外的 pip 或 conda 包來運行，則可以通過 和`pip_packages``conda_packages`參數傳遞它們的名稱，從而在生成的 docker 映射上安裝包。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


有關自訂 Python 環境的詳細資訊，請參閱[創建和管理用於培訓和部署的環境](how-to-use-environments.md)。 

## <a name="submit-a-run"></a>提交運行

[Run 物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作業運行時和完成作業後提供執行歷程記錄的介面。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

在執行運行時，它將經歷以下階段：

- **準備**： 根據 TensorFlow 估計器創建 docker 映射。 圖像將上載到工作區的容器註冊表，並緩存以以後運行。 日誌也會資料流到執行歷程記錄，並可以查看以監視進度。

- **縮放**：如果 Batch AI 群集需要比當前可用的節點更多的節點來執行運行，群集將嘗試向上擴展。

- **正在運行**：腳本資料夾中的所有腳本都將上載到計算目標，將裝載或複製資料存儲，並執行entry_script。 來自 stdout 和 ./logs 資料夾的輸出將資料流到執行歷程記錄，並可用於監視運行。

- **後處理**：運行的 ./輸出檔案夾將複製到執行歷程記錄。

## <a name="save-and-register-the-model"></a>保存並註冊模型

訓練模型後，可以將其保存並註冊到工作區。 模型註冊允許您在工作區中存儲和版本模型，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

將以下代碼添加到訓練腳本 train_iris.py 以保存模型。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

使用以下代碼將模型註冊到工作區。 通過指定參數`model_framework`、`model_framework_version`和`resource_configuration`， 無代碼模型部署將變為可用。 這允許您直接從已註冊的模型將模型部署為 Web 服務，[`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py)並且物件定義 Web 服務的計算資源。

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

無論使用哪個估計器用於培訓，您剛剛註冊的模型都可以以與 Azure 機器學習中的任何其他註冊模型完全相同的方式進行部署。 部署方式包含有關註冊模型的部分，但您可以直接跳到創建用於部署[的計算目標](how-to-deploy-and-where.md#choose-a-compute-target)，因為您已經具有已註冊的模型。

### <a name="preview-no-code-model-deployment"></a>（預覽版）無代碼模型部署

您還可以使用無代碼部署功能（預覽）學習，而不是傳統的部署路由。 所有內置的 scikit 學習模型類型都支援無代碼模型部署。 通過將模型與`model_framework`、`model_framework_version`和`resource_configuration`參數註冊，只需使用[`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)靜態函數來部署模型即可。

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

注： 這些依賴項包含在預構建的 scikit 學習推理容器中。

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

完整的["手"功能涵蓋](how-to-deploy-and-where.md)Azure 機器學習中更深入的部署。


## <a name="next-steps"></a>後續步驟

在本文中，您訓練和註冊了一個 scikit 學習模型，並瞭解了部署選項。 請參閱這些其他文章，瞭解有關 Azure 機器學習的更多詳細資訊。

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [Azure 中分散式深度學習培訓的參考體系結構](/azure/architecture/reference-architectures/ai/training-deep-learning)
