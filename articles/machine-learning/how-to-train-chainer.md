---
title: 培養深度學習鏈條模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習鏈器估計器評估器類在企業規模上運行 PyTorch 培訓腳本。  示例腳本對手寫數位影像進行分類，以便使用運行在 numpy 之上的鏈家 Python 庫構建深度學習神經網路。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536612"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>使用 Azure 機器學習大規模培訓和註冊鏈家模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，瞭解如何使用 Azure 機器學習[鏈器估計器評估器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)類在企業規模上運行[鏈家](https://chainer.org/)培訓腳本。 本文中的示例訓練腳本使用流行的[MNIST 資料集](http://yann.lecun.com/exdb/mnist/)使用使用運行在[numpy](https://www.numpy.org/)之上的鏈家 Python 庫構建的深層神經網路 （DNN） 對手寫數位進行分類。

無論您是從開始培訓深度學習鏈家模型，還是將現有模型引入雲中，都可以使用 Azure 機器學習來擴展開源培訓作業，使用彈性雲計算資源。 您可以使用 Azure 機器學習構建、部署、版本和監視生產級模型。 

瞭解有關[深度學習與機器學習的更多詳細資訊](concept-deep-learning-vs-machine-learning.md)。

如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>Prerequisites

在以下任一環境中運行此代碼：

- Azure 機器學習計算實例 - 無需下載或安裝

    - 完成[教程：設置環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以創建預載入 SDK 和示例存儲庫的專用筆記本伺服器。
    - 在筆記本伺服器上的示例深度學習資料夾中，在 **"使用操作"> ml 框架>鏈家>部署>訓練-超參數-調諧-部署-鏈式儲存體**資料夾中查找已完成的筆記本和檔。  該筆記本包括涵蓋智慧超參數調優、模型部署和筆記本小部件的擴展部分。

- 您自己的 Jupyter Notebook 伺服器

    - [安裝 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [創建工作區設定檔](how-to-configure-environment.md#workspace)。
    - 下載示例指令檔[chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer)。
     - 您還可以在 GitHub 示例頁面上找到本指南的已完成[的 Jupyter 筆記本版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)。 該筆記本包括涵蓋智慧超參數調優、模型部署和筆記本小部件的擴展部分。

## <a name="set-up-the-experiment"></a>設置實驗

本節通過載入所需的 python 包、初始化工作區、創建實驗以及上載訓練資料和培訓腳本來設置訓練實驗。

### <a name="import-packages"></a>匯入套件

首先，導入 azureml.core Python 庫並顯示版本號。

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure 機器學習工作區](concept-workspace.md)是服務的頂級資源。 它為您提供了一個集中的位置，用於處理您創建的所有專案。 在 Python SDK 中，您可以通過創建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)物件來訪問工作區專案。

通過讀取先決條件`config.json`[部分](#prerequisites)中創建的檔，創建工作區物件：

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>創建專案目錄
創建一個目錄，其中包含從本地電腦訪問遠端資源所需的所有代碼。 這包括訓練腳本以及訓練腳本所依賴的任何其他檔。

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>準備培訓腳本

在本教程中，已為您提供**培訓腳本chainer_mnist.py。** 實際上，您應該能夠像現在一樣獲取任何自訂訓練腳本，並在 Azure ML 上運行它，而無需修改代碼。

要使用 Azure ML 跟蹤和指標功能，請在訓練腳本中添加少量 Azure ML 代碼。  訓練腳本**chainer_mnist.py**演示如何使用腳本中`Run`的物件將某些指標記錄到 Azure ML 運行。

提供的訓練腳本使用來自鏈條器`datasets.mnist.get_mnist`函數的示例資料。  對於您自己的資料，您可能需要使用["上載資料集"和"腳本](how-to-train-keras.md#data-upload)"等步驟在培訓期間提供資料。

將訓練腳本**chainer_mnist.py**複製到專案目錄中。

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>創建深度學習實驗

建立實驗。 在此示例中，創建一個稱為"鏈子-mnist"的實驗。

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>創建或獲取計算目標

您需要一個[計算目標](concept-compute-target.md)來訓練模型。 在此示例中，對於遠端訓練計算資源，請使用 Azure ML 託管計算 （Aml 計算）。

**創建 Aml 計算大約需要 5 分鐘**。 如果具有該名稱的 AmlCompute 已在工作區中，則此代碼將跳過創建過程。  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

有關計算目標的詳細資訊，請參閱[什麼是計算目標](concept-compute-target.md)文章。

## <a name="create-a-chainer-estimator"></a>創建鏈條估計器

[鏈式估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)提供了一種在計算目標上啟動鏈子訓練作業的簡單方法。

鏈式估計器通過泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類實現，該類可用於支援任何框架。 有關使用通用估計器的訓練模型的詳細資訊，請參閱[使用估計器使用 Azure 機器學習訓練模型](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>提交運行

[Run 物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作業運行時和完成作業後提供執行歷程記錄的介面。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

執行運行時，它將經歷以下階段：

- **準備**： 根據鏈子估計器創建 docker 映射。 圖像將上載到工作區的容器註冊表，並緩存以以後運行。 日誌也會資料流到執行歷程記錄，並可以查看以監視進度。

- **縮放**：如果 Batch AI 群集需要比當前可用的節點更多的節點來執行運行，群集將嘗試向上擴展。

- **正在運行**：腳本資料夾中的所有腳本都將上載到計算目標，將裝載或複製資料存儲，並執行entry_script。 來自 stdout 和 ./logs 資料夾的輸出將資料流到執行歷程記錄，並可用於監視運行。

- **後處理**：運行的 ./輸出檔案夾將複製到執行歷程記錄。

## <a name="save-and-register-the-model"></a>保存並註冊模型

訓練模型後，可以將其保存並註冊到工作區。 模型註冊允許您在工作區中存儲和版本模型，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。


模型培訓完成後，使用以下代碼將模型註冊到工作區。  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> 您剛剛註冊的模型的部署方式與 Azure 機器學習中的任何其他註冊模型完全相同，而與用於培訓的估算器無關。 部署方式包含有關註冊模型的部分，但您可以直接跳到創建用於部署[的計算目標](how-to-deploy-and-where.md#choose-a-compute-target)，因為您已經具有已註冊的模型。

您還可以下載模型的本機複本。 這對於在本地執行其他模型驗證工作非常有用。 在訓練腳本中，`chainer_mnist.py`保存程式物件將模型保存到本地資料夾（計算目標的本地）。 您可以使用 Run 物件從資料存儲下載副本。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>後續步驟

在本文中，您使用 Azure 機器學習上的鏈式程式訓練和註冊了深度學習神經網路。 要瞭解如何部署模型，請繼續執行[模型部署](how-to-deploy-and-where.md)文章。

* [調整超參數](how-to-tune-hyperparameters.md)

* [追蹤定型期間的執行計量](how-to-track-experiments.md)

* [查看我們在 Azure 中分散式深度學習培訓的參考體系結構](/azure/architecture/reference-architectures/ai/training-deep-learning)
