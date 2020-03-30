---
title: 訓練深度學習喀斯模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習在 TensorFlow 上運行的 Keras 深神經網路分類模型進行訓練和註冊。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269961"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>使用 Azure 機器學習訓練和註冊 Keras 分類模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介紹如何使用 Azure 機器學習訓練和註冊在 TensorFlow 上構建的 Keras 分類模型。 它使用流行的[MNIST資料集](http://yann.lecun.com/exdb/mnist/)，使用使用[運行在TensorFlow](https://www.tensorflow.org/overview)頂部的[Keras Python庫](https://keras.io)構建的深層神經網路 （DNN） 對手寫數位進行分類。

Keras 是一種高級神經網路 API，能夠運行其他流行的 DNN 框架的頂部，以簡化開發。 借助 Azure 機器學習，您可以使用彈性雲計算資源快速擴展培訓作業。 您還可以跟蹤您的培訓運行、版本模型、部署模型等。

無論您是從零開始開發 Keras 模型，還是將現有模型引入雲中，Azure 機器學習都可以説明您構建生產就緒模型。

有關機器學習和深度學習之間的差異的資訊，請參閱[概念文章](concept-deep-learning-vs-machine-learning.md)。

## <a name="prerequisites"></a>Prerequisites

在以下任一環境中運行此代碼：

- Azure 機器學習計算實例 - 無需下載或安裝

     - 完成[教程：設置環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以創建預載入 SDK 和示例存儲庫的專用筆記本伺服器。
    - 在筆記本伺服器上的示例資料夾中，通過導航到此目錄找到已完成和展開的筆記本：**如何使用-azureml >培訓-深度學習>訓練-超參數-調諧-部署-使用角圈**資料夾。

 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [創建工作區設定檔](how-to-configure-environment.md#workspace)。
    - [下載示例指令檔和](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)`mnist-keras.py``utils.py`

    您還可以在 GitHub 示例頁面上找到本指南的已完成[的 Jupyter 筆記本版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)。 該筆記本包括涵蓋智慧超參數調優、模型部署和筆記本小部件的擴展部分。

## <a name="set-up-the-experiment"></a>設置實驗

本節通過載入所需的 python 包、初始化工作區、創建實驗以及上載訓練資料和培訓腳本來設置訓練實驗。

### <a name="import-packages"></a>匯入套件

首先，導入必要的 Python 庫。

```Python
import os
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

### <a name="create-an-experiment"></a>建立實驗

在工作區中創建名為"角膜-mnist"的實驗。

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>創建檔資料集

`FileDataset` 物件會參考您工作區資料存放區中的一個或多個檔案或公用 URL。 檔案可以是任何格式，而類別可讓您將檔案下載或掛接至您的計算。 您可以藉由建立 `FileDataset` 來建立來源位置的參考。 如果您對資料集套用任何轉換，這些轉換也會儲存在資料集中。 資料會保留在現有的位置，因此不會產生額外的儲存成本。 如需詳細資訊，請參閱 `Dataset` 套件上的[操作](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)指南。

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

使用`register()`方法將資料集註冊到工作區，以便可以與他人共用、在各種實驗中重複使用，並在培訓腳本中按名稱引用。

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>建立計算目標

為 TensorFlow 作業創建要運行的計算目標。 在此示例中，創建啟用 GPU 的 Azure 機器學習計算群集。

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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>創建 TensorFlow 估計器並導入 Keras

[TensorFlow 估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供了一種在計算目標上啟動 TensorFlow 訓練作業的簡單方法。 由於 Keras 運行在 TensorFlow 之上，因此可以使用 TensorFlow 估計器並使用 參數`pip_packages`導入 Keras 庫。

首先使用`Dataset`類從工作區資料存儲獲取資料。

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

TensorFlow 估計器通過泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類實現，該類可用於支援任何框架。 此外，創建包含`script_params`DNN 超參數設置的字典。 有關使用通用估計器的訓練模型的詳細資訊，請參閱[使用估計器使用 Azure 機器學習訓練模型](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>提交運行

[Run 物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作業運行時和完成作業後提供執行歷程記錄的介面。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

執行運行時，它將經歷以下階段：

- **準備**： 根據 TensorFlow 估計器創建 docker 映射。 圖像將上載到工作區的容器註冊表，並緩存以以後運行。 日誌也會資料流到執行歷程記錄，並可以查看以監視進度。

- **縮放**：如果 Batch AI 群集需要比當前可用的節點更多的節點來執行運行，群集將嘗試向上擴展。

- **正在運行**：腳本資料夾中的所有腳本都將上載到計算目標，將裝載或複製資料存儲，並執行entry_script。 來自 stdout 和 ./logs 資料夾的輸出將資料流到執行歷程記錄，並可用於監視運行。

- **後處理**：運行的 ./輸出檔案夾將複製到執行歷程記錄。

## <a name="register-the-model"></a>註冊模型

訓練 DNN 模型後，即可將其註冊到工作區。 模型註冊允許您在工作區中存儲和版本模型，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> 您剛剛註冊的模型的部署方式與 Azure 機器學習中的任何其他註冊模型完全相同，而與用於培訓的估算器無關。 部署方式包含有關註冊模型的部分，但您可以直接跳到創建用於部署[的計算目標](how-to-deploy-and-where.md#choose-a-compute-target)，因為您已經具有已註冊的模型。

您還可以下載模型的本機複本。 這對於在本地執行其他模型驗證工作非常有用。 在訓練腳本中，TensorFlow`mnist-keras.py`保存器物件將模型保存到本地資料夾（計算目標的本地）。 您可以使用 Run 物件從資料存儲下載副本。

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

在本文中，您在 Azure 機器學習上訓練和註冊了 Keras 模型。 要瞭解如何部署模型，請繼續執行模型部署文章。

> [!div class="nextstepaction"]
> [如何以及在哪裡部署模型](how-to-deploy-and-where.md)
* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中分散式深度學習培訓的參考體系結構](/azure/architecture/reference-architectures/ai/training-deep-learning)
