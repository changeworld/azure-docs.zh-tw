---
title: 定型及部署 TensorFlow 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 大規模執行 TensorFlow 訓練腳本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c25464444abe8b4bc274f71618c62a751143d594
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648277"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>使用 Azure Machine Learning 大規模建立 TensorFlow 深度學習模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文說明如何使用 Azure Machine Learning 的[TensorFlow 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true)類別，大規模地執行您的[TensorFlow](https://www.tensorflow.org/overview)訓練腳本。 此範例會定型並註冊 TensorFlow 模型，使用深度類神經網路將手寫數位分類 (DNN) 。

無論您是從頭開始開發 TensorFlow 模型，或是將 [現有的模型](how-to-deploy-existing-model.md) 帶入雲端，您都可以使用 Azure Machine Learning 來向外延展開放原始碼訓練作業，以建立、部署、設定版本和監視生產等級的模型。

深入瞭解 [與機器學習的深度學習](concept-deep-learning-vs-machine-learning.md)。

## <a name="prerequisites"></a>必要條件

在下列任一環境中執行此程式碼：

 - Azure Machine Learning 計算執行個體 - 不需要下載或安裝

     - 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
    - 在筆記本伺服器的 [範例深度學習] 資料夾中，流覽至下列目錄以找出已完成和展開的筆記本： **如何使用 azureml > ml-架構 > tensorflow > 部署 > 訓練-超參數-tensorflow** 資料夾。 
 
 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。
    - [下載範例指令檔](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` 和 `utils.py`
     
    您也可以在 GitHub 範例頁面上找到本指南的完整 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) 。 筆記本包含擴充的章節，涵蓋智慧型超參數微調、模型部署和筆記本 widget。

## <a name="set-up-the-experiment"></a>設定實驗

本節藉由載入所需的 Python 套件、初始化工作區、建立實驗，以及上傳定型資料和定型腳本，來設定定型實驗。

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
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure Machine Learning 工作區](concept-workspace.md)是服務的最上層資源。 其可提供集中式位置以處理您建立的所有成品。 在 Python SDK 中，您可以藉由建立物件來存取工作區成品 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) 。

從「 `config.json` [必要條件」一節](#prerequisites)中建立的檔案建立工作區物件。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>建立深度學習實驗

建立實驗和資料夾來保存訓練腳本。 在此範例中，請建立名為 "tf-mnist" 的實驗。

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>建立檔案資料集

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

您可以使用方法，將 `register()` 資料集註冊到您的工作區，以便與其他人共用、跨不同的實驗重複使用，以及在定型腳本中依名稱參考。

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>建立計算目標

建立要在其上執行之 TensorFlow 作業的計算目標。 在此範例中，請建立已啟用 GPU 的 Azure Machine Learning 計算叢集。

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

如需計算目標的詳細資訊，請參閱「 [什麼是計算目標」一](concept-compute-target.md) 文。

## <a name="create-a-tensorflow-estimator"></a>建立 TensorFlow 估算器

[TensorFlow 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true)提供簡單的方法，讓您在計算目標上啟動 TensorFlow 訓練作業。

TensorFlow 估算器是透過 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) 可用於支援任何架構的泛型類別來執行。 如需如何使用泛型估算器來將模型定型的詳細資訊，請參閱[藉由估算器使用 Azure Machine Learning 將模型定型](how-to-train-ml-models.md)

如果您的定型腳本需要額外的 pip 或 conda 封裝來執行，您可以透過 `pip_packages` 和引數傳遞這些封裝的名稱，將套件安裝在產生的 Docker 映射上 `conda_packages` 。


> [!WARNING]
> Azure Machine Learning 藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的機密資料，請使用 [. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 檔案，或不要將它包含在來原始目錄中。 相反地， [使用資料存放](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)區存取您的資料。


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
> **Tensorflow 2.0**的支援已新增至 Tensorflow 估算器類別。 如需詳細資訊，請參閱[部落格文章](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) \(英文\)。

如需自訂 Python 環境的詳細資訊，請參閱[建立和管理用於訓練和部署的環境](how-to-use-environments.md)。 

## <a name="submit-a-run"></a>提交執行

當作業正在執行時， [執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) 會提供介面給執行歷程記錄，並在完成後提供。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

執行執行時，它會經歷下列階段：

- **準備**：根據 TensorFlow 估算器建立 Docker 映射。 映射上傳至工作區的容器登錄，並快取以供稍後執行。 記錄也會串流至執行歷程記錄，並可加以查看以監視進度。

- **調整**：如果 Batch AI 叢集需要更多節點來執行執行比目前可用的節點，則叢集會嘗試擴大規模。

- **執行中：腳本**資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區，並執行 entry_script。 Stdout 和./logs 資料夾的輸出會串流處理至執行歷程記錄，並可用來監視執行。

- **後續處理**：執行的./outputs 資料夾會複製到執行歷程記錄。

## <a name="register-or-download-a-model"></a>註冊或下載模型

當您將模型定型之後，就可以將它註冊到您的工作區。 註冊模型可讓您在工作區中儲存模型並設定其版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。 藉由指定參數 `model_framework` 、 `model_framework_version` 和 `resource_configuration` ，將無法使用任何程式碼模型部署。 這可讓您直接從註冊的模型將模型部署為 web 服務，而物件會 `ResourceConfiguration` 定義 web 服務的計算資源。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

您也可以使用 Run 物件來下載模型的本機複本。 在定型腳本中 `mnist-tf.py` ，TensorFlow 保護物件會將模型保存到計算目標)  (本機的本機資料夾。 您可以使用 [執行] 物件下載複本。

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

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true)估算器也支援跨 CPU 和 GPU 叢集的分散式定型。 您可以輕鬆地執行分散式 TensorFlow 作業，Azure Machine Learning 將會為您管理協調流程。

Azure Machine Learning 支援 TensorFlow 中兩種分散式訓練方法：

- 使用[Horovod](https://github.com/uber/horovod)架構的[MPI 型](https://www.open-mpi.org/)分散式訓練
- 使用參數伺服器方法的原生[分散式 TensorFlow](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) 是一種開放原始碼架構，適用于 Uber 所開發的分散式訓練。 它提供分散式 GPU TensorFlow 作業的簡單路徑。

若要使用 Horovod，請 [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) 在 TensorFlow 的函式中指定參數的物件 `distributed_training` 。 此參數可確保已安裝 Horovod 程式庫，供您在定型腳本中使用。

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

若要使用參數伺服器方法，請 [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) 在 TensorFlow 函式 `distributed_training` 中指定參數的物件。

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

#### <a name="define-cluster-specifications-in-tf_config"></a>在 ' TF_CONFIG ' 中定義叢集規格

您也需要的群集的網路位址和埠 [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) ，因此 Azure Machine Learning `TF_CONFIG` 為您設定環境變數。

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

針對 TensorFlow 的高層級 [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API，TensorFlow 會剖析 `TF_CONFIG` 變數並為您建立叢集規格。

針對用於定型的 TensorFlow 較低層級核心 Api，請剖析 `TF_CONFIG` 變數，並 `tf.train.ClusterSpec` 在您的定型程式碼中建立。

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

您剛剛註冊的模型可以與 Azure Machine Learning 中的任何其他已註冊模型完全相同，無論您使用何種估算器來進行定型。 部署如何包含註冊模型的區段，但您可以直接跳到建立部署的 [計算目標](how-to-deploy-and-where.md#choose-a-compute-target) ，因為您已經有已註冊的模型。

## <a name="preview-no-code-model-deployment"></a> (Preview) 無程式碼模型部署

您也可以使用 Tensorflow 的「無程式碼部署」功能 (preview) ，而不是傳統的部署路由。 藉由使用、和參數來註冊您的模型（如上所示 `model_framework` `model_framework_version` `resource_configuration` ），您可以直接使用靜態函式 `deploy()` 來部署模型。

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

[完整的作法涵蓋了 Azure Machine Learning](how-to-deploy-and-where.md)更深入的部署。

## <a name="next-steps"></a>接下來的步驟

在本文中，您已訓練並註冊 TensorFlow 模型，並瞭解部署的選項。 若要深入瞭解 Azure Machine Learning，請參閱這些其他文章。

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [Azure 中分散式深度學習訓練的參考架構](/azure/architecture/reference-architectures/ai/training-deep-learning)
