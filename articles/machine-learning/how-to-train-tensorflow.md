---
title: 訓練和部署 TensorFlow 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習大規模運行 TensorFlow 訓練腳本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2bbd81f3858aa78b9e0e2d610c0fdb0a67816c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228307"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>使用 Azure 機器學習大規模構建 TensorFlow 深度學習模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介紹如何使用 Azure 機器學習的[TensorFlow 估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)類大規模運行[TensorFlow](https://www.tensorflow.org/overview)訓練腳本。 本示例訓練並註冊 TensorFlow 模型，以便使用深度神經網路 （DNN） 對手寫數位進行分類。

無論您是從開始開發 TensorFlow 模型，還是將[現有模型](how-to-deploy-existing-model.md)引入雲中，都可以使用 Azure 機器學習來擴展開源培訓作業，以構建、部署、版本和監視生產級模型。

瞭解有關[深度學習與機器學習的更多詳細資訊](concept-deep-learning-vs-machine-learning.md)。

## <a name="prerequisites"></a>Prerequisites

在以下任一環境中運行此代碼：

 - Azure 機器學習計算實例 - 無需下載或安裝

     - 完成[教程：設置環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以創建預載入 SDK 和示例存儲庫的專用筆記本伺服器。
    - 在筆記本伺服器上的示例深度學習資料夾中，通過導航到此目錄找到已完成和擴展的筆記本：**如何使用 azureml > ml 框架> >部署>訓練-超參數-調諧-部署-帶 tens流**資料夾的部署。 
 
 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [創建工作區設定檔](how-to-configure-environment.md#workspace)。
    - [下載示例指令檔和](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow)`mnist-tf.py``utils.py`
     
    您還可以在 GitHub 示例頁面上找到本指南的已完成[的 Jupyter 筆記本版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb)。 該筆記本包括涵蓋智慧超參數調優、模型部署和筆記本小部件的擴展部分。

## <a name="set-up-the-experiment"></a>設置實驗

本節通過載入所需的 Python 包、初始化工作區、創建實驗以及上載訓練資料和培訓腳本來設置訓練實驗。

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
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure 機器學習工作區](concept-workspace.md)是服務的頂級資源。 它為您提供了一個集中的位置，用於處理您創建的所有專案。 在 Python SDK 中，您可以通過創建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)物件來訪問工作區專案。

從`config.json`[先決條件部分](#prerequisites)中創建的檔創建工作區物件。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>創建深度學習實驗

創建一個實驗和一個資料夾來保存你的訓練腳本。 在此示例中，創建一個名為"tf-mnist"的實驗。

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

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

# list the files referenced by dataset
dataset.to_path()
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

## <a name="create-a-tensorflow-estimator"></a>創建 TensorFlow 估計器

[TensorFlow 估計器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供了一種在計算目標上啟動 TensorFlow 訓練作業的簡單方法。

TensorFlow 估計器通過泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類實現，該類可用於支援任何框架。 有關使用通用估計器的訓練模型的詳細資訊，請參閱[使用估計器使用 Azure 機器學習訓練模型](how-to-train-ml-models.md)

如果訓練腳本需要額外的 pip 或 conda 包來運行，則可以通過 和`pip_packages``conda_packages`參數傳遞它們的名稱，從而在生成的 Docker 映射上安裝包。

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> 對**Tensorflow 2.0**的支援已添加到 Tensorflow 估計器類中。 如需詳細資訊，請參閱[部落格文章](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) \(英文\)。

有關自訂 Python 環境的詳細資訊，請參閱[創建和管理用於培訓和部署的環境](how-to-use-environments.md)。 

## <a name="submit-a-run"></a>提交運行

[Run 物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作業運行時和完成作業後提供執行歷程記錄的介面。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

執行運行時，它將經歷以下階段：

- **準備**： 根據 TensorFlow 估計器創建 Docker 映射。 圖像將上載到工作區的容器註冊表，並緩存以以後運行。 日誌也會資料流到執行歷程記錄，並可以查看以監視進度。

- **縮放**：如果 Batch AI 群集需要比當前可用的節點更多的節點來執行運行，群集將嘗試向上擴展。

- **正在運行**：腳本資料夾中的所有腳本都將上載到計算目標，將裝載或複製資料存儲，並執行entry_script。 來自 stdout 和 ./logs 資料夾的輸出將資料流到執行歷程記錄，並可用於監視運行。

- **後處理**：運行的 ./輸出檔案夾將複製到執行歷程記錄。

## <a name="register-or-download-a-model"></a>註冊或下載模型

訓練模型後，即可將其註冊到工作區。 模型註冊允許您在工作區中存儲和版本模型，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。 通過指定參數`model_framework`、`model_framework_version`和`resource_configuration`， 無代碼模型部署將變為可用。 這允許您直接從已註冊的模型將模型部署為 Web 服務，`ResourceConfiguration`並且物件定義 Web 服務的計算資源。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

您還可以使用 Run 物件下載模型的本機複本。 在訓練腳本`mnist-tf.py`中，TensorFlow 保存器物件將模型保存到本地資料夾（計算目標的本地）。 您可以使用 Run 物件下載副本。

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

估算[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)器還支援跨 CPU 和 GPU 群集的分散式訓練。 您可以輕鬆地運行分散式 TensorFlow 作業，Azure 機器學習將為您管理業務流程。

Azure Machine Learning 支援 TensorFlow 中兩種分散式訓練方法：

- 使用[霍羅沃德](https://github.com/uber/horovod)框架[進行基於 MPI](https://www.open-mpi.org/)的分散式培訓
- 使用參數伺服器方法的本機[分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod)是 Uber 開發的分散式培訓的開源框架。 它提供了一條通往分散式 GPU TensorFlow 作業的簡單路徑。

要使用 Horovod，請在[`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py)TensorFlow 建構函式中為`distributed_training`參數指定物件。 此參數可確保安裝 Horovod 庫，供您在訓練腳本中使用。

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>參數伺服器

您也可以執行[原生分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)，其會使用參數伺服器模型。 在此方法中，您會在參數伺服器與工作者的整個叢集中進行訓練。 工作者會在訓練期間會計算升降度，而參數伺服器會彙總升降度。

要使用參數伺服器方法，請在 TensorFlow`distributed_training`建構函式中為參數指定物件[`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py)。

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>在"TF_CONFIG"中定義群集規範

您還需要 群集的網路位址和埠[`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)，以便 Azure 機器學習為您設置`TF_CONFIG`環境變數。

`TF_CONFIG` 環境變數為 JSON 字串。 以下為參數伺服器變數的範例：

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

對於 TensorFlow 的高級[`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator)API，TensorFlow 會分析`TF_CONFIG`變數並為您構建群集規範。

對於 TensorFlow 用於訓練的較低級別核心 API，請分析`TF_CONFIG`變數並在訓練代碼`tf.train.ClusterSpec`中構建。

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>部署 TensorFlow 模型

無論使用哪個估計器用於培訓，您剛剛註冊的模型都可以以與 Azure 機器學習中的任何其他註冊模型完全相同的方式進行部署。 部署方式包含有關註冊模型的部分，但您可以直接跳到創建用於部署[的計算目標](how-to-deploy-and-where.md#choose-a-compute-target)，因為您已經具有已註冊的模型。

## <a name="preview-no-code-model-deployment"></a>（預覽版）無代碼模型部署

也可以使用 Tensorflow 的無代碼部署功能（預覽），而不是傳統的部署路由。 通過將模型與`model_framework`、`model_framework_version`和`resource_configuration`參數註冊，只需使用`deploy()`靜態函數來部署模型即可。

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

完整的["手"功能涵蓋](how-to-deploy-and-where.md)Azure 機器學習中更深入的部署。

## <a name="next-steps"></a>後續步驟

在本文中，您訓練和註冊了 TensorFlow 模型，並瞭解了部署選項。 請參閱這些其他文章，瞭解有關 Azure 機器學習的更多詳細資訊。

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [Azure 中分散式深度學習培訓的參考體系結構](/azure/architecture/reference-architectures/ai/training-deep-learning)
