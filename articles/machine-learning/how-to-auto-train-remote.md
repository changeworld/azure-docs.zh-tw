---
title: 自動化 ML 遠端計算目標
titleSuffix: Azure Machine Learning
description: 瞭解如何在具有 Azure Machine Learning 的 Azure Machine Learning 遠端計算目標上，使用自動化機器學習來建立模型
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 9e499d609a3f78dc5f422b9ed90df09be30f2e7c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79080406"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>使用雲端中的自動化機器學習來將模型定型

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在 Azure Machine Learning 中，您可以在所管理的不同類型計算資源上將模型定型。 計算目標可以是本機電腦或雲端中的資源。

您可以藉由新增額外的計算目標（例如 Azure Machine Learning 計算（AmlCompute），輕鬆地相應增加或相應放大您的機器學習實驗。 AmlCompute 是一種受控的計算基礎結構，可讓您輕鬆建立單一或多重節點計算。

在本文中，您將瞭解如何使用自動化 ML 搭配 AmlCompute 來建立模型。

## <a name="how-does-remote-differ-from-local"></a>遠端與本機有何不同？

「[使用自動化機器學習來訓練分類模型](tutorial-auto-train-models.md)」教學課程會教您如何使用本機電腦，透過自動化 ML 來訓練模型。 在本機訓練時的工作流程也適用於遠端目標。 不過，使用遠端計算時，自動化 ML 實驗反覆項目會以非同步方式執行。 此功能可讓您取消特定的反覆項目、監看執行狀態，或繼續處理 Jupyter Notebook 中的其他資料格。 若要從遠端進行定型，請先建立遠端計算目標，例如 AmlCompute。 接著，設定遠端資源，並在該處提交您的程式碼。

本文說明在遠端 AmlCompute 目標上執行自動化 ML 實驗所需的額外步驟。 以下程式碼會使用來自教學課程的工作區物件 `ws`。

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>建立資源

在您[`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py)的工作區中建立`ws`目標（）（如果尚未存在）。

**估計時間**：建立 AmlCompute 目標大約需要5分鐘。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

您現在可以使用 `compute_target` 物件作為遠端計算目標。

叢集名稱限制包括：
+ 必須少於 64 個字元。
+ 不得包含下列任一字元：`\` ~ ! @ # $% ^ & * （） = + _ [] {} \\ \\ |;： \' \\"、 < > /？。`

## <a name="access-data-using-tabulardataset-function"></a>使用 TabularDataset 函數存取資料

定義 training_data 做[`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)為和標籤，並將其傳遞至中的[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)自動化 ML。 根據`TabularDataset`預設`from_delimited_files`，方法會將設定`infer_column_types`為 true，這將會自動推斷資料行類型。 

如果您想要手動設定資料行類型，您可以設定`set_column_types`引數以手動設定每個資料行的類型。 在下列程式碼範例中，資料來自 sklearn 套件。

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>設定實驗
指定 `AutoMLConfig` 的設定。  (請參閱[完整參數清單](how-to-configure-auto-train.md#configure-experiment)及其可能值。)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>提交定型實驗

現在提交設定以自動選取演算法、超參數，並訓練模型。

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

您將會看到類似於下列範例的輸出：

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>瀏覽結果

您可以使用[訓練教學課程](tutorial-auto-train-models.md#explore-the-results)中所示的相同[Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)來查看結果的圖表和資料表。

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

以下是小工具的靜態影像。  在筆記本中，您可以按一下資料表中的任一行以查看執行的屬性，以及該次執行的輸出記錄。   您也可以使用圖表上方的下拉式清單，以針對每個反覆項目檢視每個可用度量的圖表。

![小工具資料表](./media/how-to-auto-train-remote/table.png)
![小工具繪圖](./media/how-to-auto-train-remote/plot.png)

小工具會顯示 URL，您可以使用它來查看並瀏覽個別執行的詳細資料。  

如果您不在 Jupyter 筆記本中，可以從執行本身顯示 URL：

```
remote_run.get_portal_url()
```

您的工作區中會提供相同的資訊。  若要深入瞭解這些結果，請參閱[瞭解自動化機器學習結果](how-to-understand-automated-ml.md)。

## <a name="example"></a>範例

下列[筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb)會示範本文中的概念。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* 了解[如何設定自動訓練的設定](how-to-configure-auto-train.md)。
* 請參閱自動化 ML 實驗中啟用模型 interpretability 功能的操作[說明](how-to-machine-learning-interpretability-automl.md)。
