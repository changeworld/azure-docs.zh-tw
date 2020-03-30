---
title: 訓練深度學習 PyTorch 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習的 PyTorch 估算器類在企業規模上運行 PyTorch 培訓腳本。  示例腳本對雞肉和火雞圖像進行分類，以基於 PyTorch 的轉移學習教程構建深度學習神經網路。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834868"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>使用 Azure 機器學習大規模訓練 Pytorch 深度學習模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，瞭解如何使用 Azure 機器學習的[PyTorch 估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)類在企業規模運行[PyTorch](https://pytorch.org/)培訓腳本。  

本文中的示例腳本用於對雞和火雞圖像進行分類，以構建基於 PyTorch 的轉移[學習教程](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)的深層學習神經網路。 

無論您是從地面培訓深度學習 PyTorch 模型，還是將現有模型引入雲中，都可以使用 Azure 機器學習來擴展開源培訓作業，使用彈性雲計算資源。 您可以使用 Azure 機器學習構建、部署、版本和監視生產級模型。 

瞭解有關[深度學習與機器學習的更多詳細資訊](concept-deep-learning-vs-machine-learning.md)。

## <a name="prerequisites"></a>Prerequisites

在以下任一環境中運行此代碼：

- Azure 機器學習計算實例 - 無需下載或安裝

    - 完成[教程：設置環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以創建預載入 SDK 和示例存儲庫的專用筆記本伺服器。
    - 在筆記本伺服器上的示例深度學習資料夾中，通過導航到此目錄找到已完成和展開的筆記本：**如何使用-azureml >培訓-與深度學習>訓練-超參數-調諧-部署-使用 pytorch**資料夾。 
 
 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [創建工作區設定檔](how-to-configure-environment.md#workspace)。
    - [下載示例指令檔](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    您還可以在 GitHub 示例頁面上找到本指南的已完成[的 Jupyter 筆記本版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb)。 該筆記本包括涵蓋智慧超參數調優、模型部署和筆記本小部件的擴展部分。

## <a name="set-up-the-experiment"></a>設置實驗

本節通過載入所需的 python 包、初始化工作區、創建實驗以及上載訓練資料和培訓腳本來設置訓練實驗。

### <a name="import-packages"></a>匯入套件

首先，導入必要的 Python 庫。

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure 機器學習工作區](concept-workspace.md)是服務的頂級資源。 它為您提供了一個集中的位置，用於處理您創建的所有專案。 在 Python SDK 中，您可以通過創建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)物件來訪問工作區專案。

從`config.json`[先決條件部分](#prerequisites)中創建的檔創建工作區物件。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>創建深度學習實驗

創建一個實驗和一個資料夾來保存你的訓練腳本。 在此示例中，創建一個稱為"火焰鳥"的實驗。

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>取得資料

資料集由約 120 個火雞和雞的訓練圖像組成，每個類有 100 個驗證圖像。 我們將下載並提取資料集作為培訓腳本`pytorch_train.py`的一部分。 圖像是[開放圖像 v5 資料集](https://storage.googleapis.com/openimages/web/index.html)的子集。

### <a name="prepare-training-scripts"></a>準備培訓腳本

在本教程中，已提供培訓腳本`pytorch_train.py`。 實際上，您可以採用任何自訂訓練腳本（如現在一樣），然後使用 Azure 機器學習運行它。

上傳 Pytorch 訓練腳本`pytorch_train.py`。

```Python
shutil.copy('pytorch_train.py', project_folder)
```

但是，如果要使用 Azure 機器學習跟蹤和指標功能，則必須在訓練腳本中添加少量代碼。 指標跟蹤示例可在 中找到`pytorch_train.py`。

## <a name="create-a-compute-target"></a>建立計算目標

為 PyTorch 作業創建要運行的計算目標。 在此示例中，創建啟用 GPU 的 Azure 機器學習計算群集。

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

有關計算目標的詳細資訊，請參閱[什麼是計算目標](concept-compute-target.md)文章。

## <a name="create-a-pytorch-estimator"></a>創建 PyTorch 估計器

[PyTorch 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)提供了一種在計算目標上啟動 PyTorch 培訓作業的簡單方法。

PyTorch 估計器通過泛型[`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類實現，該類可用於支援任何框架。 有關使用通用估計器的訓練模型的詳細資訊，請參閱[使用估計器使用 Azure 機器學習訓練模型](how-to-train-ml-models.md)

如果訓練腳本需要額外的 pip 或 conda 包來運行，則可以通過 和`pip_packages``conda_packages`參數傳遞它們的名稱，從而在生成的 docker 映射上安裝包。

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

有關自訂 Python 環境的詳細資訊，請參閱[創建和管理用於培訓和部署的環境](how-to-use-environments.md)。

## <a name="submit-a-run"></a>提交運行

[Run 物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作業運行時和完成作業後提供執行歷程記錄的介面。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

執行運行時，它將經歷以下階段：

- **準備**： 根據 PyTorch 估計器創建 docker 映射。 圖像將上載到工作區的容器註冊表，並緩存以以後運行。 日誌也會資料流到執行歷程記錄，並可以查看以監視進度。

- **縮放**：如果 Batch AI 群集需要比當前可用的節點更多的節點來執行運行，群集將嘗試向上擴展。

- **正在運行**：腳本資料夾中的所有腳本都將上載到計算目標，將裝載或複製資料存儲，並執行entry_script。 來自 stdout 和 ./logs 資料夾的輸出將資料流到執行歷程記錄，並可用於監視運行。

- **後處理**：運行的 ./輸出檔案夾將複製到執行歷程記錄。

## <a name="register-or-download-a-model"></a>註冊或下載模型

訓練模型後，即可將其註冊到工作區。 模型註冊允許您在工作區中存儲和版本模型，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> 您剛剛註冊的模型的部署方式與 Azure 機器學習中的任何其他註冊模型完全相同，而與用於培訓的估算器無關。 部署方式包含有關註冊模型的部分，但您可以直接跳到創建用於部署[的計算目標](how-to-deploy-and-where.md#choose-a-compute-target)，因為您已經具有已註冊的模型。

您還可以使用 Run 物件下載模型的本機複本。 在訓練腳本`pytorch_train.py`中，PyTorch 保存物件將模型保存到本地資料夾（計算目標的本地）。 您可以使用 Run 物件下載副本。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>分散式定型

估算[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)器還支援跨 CPU 和 GPU 群集的分散式訓練。 您可以輕鬆地運行分散式 PyTorch 作業，Azure 機器學習將為您管理業務流程。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod)是一個開源的，所有減少由 Uber 開發的分散式培訓框架。 它提供了一條通往分散式 GPU PyTorch 作業的簡單路徑。

要使用 Horovod，請在[`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py)PyTorch`distributed_training`建構函式中為參數指定物件。 此參數可確保安裝 Horovod 庫，供您在訓練腳本中使用。


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
將為您安裝 Horovod 及其依賴項，因此您可以將其導入培訓腳本`train.py`中，如下所示：

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>匯出到 ONNX

要使用[ONNX 運行時](concept-onnx.md)優化推理，請將經過訓練的 PyTorch 模型轉換為 ONNX 格式。 推理或模型評分是部署模型用於預測的階段，最常見的是生產資料。 有關示例，請參閱[本教程](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)。

## <a name="next-steps"></a>後續步驟

在本文中，您使用 PyTorch 在 Azure 機器學習上訓練並註冊了深度學習神經網路。 要瞭解如何部署模型，請繼續執行模型部署文章。

> [!div class="nextstepaction"]
> [如何以及在哪裡部署模型](how-to-deploy-and-where.md)
* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中分散式深度學習培訓的參考體系結構](/azure/architecture/reference-architectures/ai/training-deep-learning)

