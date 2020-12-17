---
title: 定型及部署 TensorFlow 模型
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 如何讓您使用彈性雲端計算資源來擴充 TensorFlow 訓練作業。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c82b1ffbb005542822016a55346d9067e23050b2
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630865"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>使用 Azure Machine Learning 大規模定型 TensorFlow 模型

在本文中，您將瞭解如何使用 Azure Machine Learning 大規模地執行您的 [TensorFlow](https://www.tensorflow.org/overview) 訓練腳本。

此範例會定型並註冊 TensorFlow 模型，使用深度類神經網路將手寫數位分類 (DNN) 。

無論您是從頭開始開發 TensorFlow 模型，或是將 [現有的模型](how-to-deploy-existing-model.md) 帶入雲端，您都可以使用 Azure Machine Learning 來向外延展開放原始碼訓練作業，以建立、部署、設定版本和監視生產等級的模型。

## <a name="prerequisites"></a>必要條件

在下列任一環境中執行此程式碼：

 - Azure Machine Learning 計算執行個體 - 不需要下載或安裝

     - 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
    - 在筆記本伺服器的 [範例深度學習] 資料夾中，流覽至下列目錄以找出已完成和展開的筆記本： **如何使用 azureml > ml-架構 > tensorflow > 超參數-tensorflow** 資料夾。 
 
 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) ( # B0 = 1.15.0) 。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。
    - [下載範例指令檔](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` 和 `utils.py`
     
    您也可以在 GitHub 範例頁面上找到本指南的完整 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) 。 筆記本包含擴充的章節，涵蓋智慧型超參數微調、模型部署和筆記本 widget。

## <a name="set-up-the-experiment"></a>設定實驗

本節會藉由載入所需的 Python 套件、初始化工作區、建立計算目標，以及定義定型環境，來設定定型實驗。

### <a name="import-packages"></a>匯入套件

首先，匯入必要的 Python 程式庫。

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure Machine Learning 工作區](concept-workspace.md)是服務的最上層資源。 其可提供集中式位置以處理您建立的所有成品。 在 Python SDK 中，您可以藉由建立物件來存取工作區成品 [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) 。

從「 `config.json` [必要條件」一節](#prerequisites)中建立的檔案建立工作區物件。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>建立檔案資料集

`FileDataset` 物件會參考您工作區資料存放區中的一個或多個檔案或公用 URL。 檔案可以是任何格式，而類別可讓您將檔案下載或掛接至您的計算。 您可以藉由建立 `FileDataset` 來建立來源位置的參考。 如果您對資料集套用任何轉換，這些轉換也會儲存在資料集中。 資料會保留在現有的位置，因此不會產生額外的儲存成本。 如需詳細資訊，請參閱 `Dataset` 套件上的[操作](./how-to-create-register-datasets.md)指南。

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
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>建立計算目標

建立要在其上執行之 TensorFlow 作業的計算目標。 在此範例中，請建立已啟用 GPU 的 Azure Machine Learning 計算叢集。

```Python
cluster_name = "gpu-cluster"

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

### <a name="define-your-environment"></a>定義您的環境

若要定義 Azure ML [環境](concept-environments.md) 以封裝定型腳本的相依性，您可以定義自訂環境，或使用 azure ml 策劃環境。

#### <a name="use-a-curated-environment"></a>使用策劃環境
如果您不想要定義自己的環境，Azure ML 會提供預建的策劃環境。 Azure ML 有數個 CPU 和 GPU 策劃環境，適用于 TensorFlow 的不同 TensorFlow 版本。 如需詳細資訊，請參閱 [這裡](resource-curated-environments.md)。

如果您想要使用策劃環境，您可以改為執行下列命令：

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

若要查看策劃環境中包含的套件，您可以將 conda 相依性寫出至磁片：
```python
tf_env.save_to_directory(path=curated_env_name)
```

請確定策劃環境包含定型腳本所需的所有相依性。 如果沒有，您就必須修改環境以包含遺漏的相依性。 請注意，如果環境已修改，您必須為它指定新的名稱，因為 ' AzureML ' 首碼保留給策劃環境。 如果您修改了 conda 相依性 YAML 檔案，您可以使用新名稱來建立新的環境，例如：
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

如果您改為直接修改策劃環境物件，您可以使用新的名稱來複製該環境：
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>建立自訂環境

您也可以建立自己的 Azure ML 環境，以封裝定型腳本的相依性。

首先，在 YAML 檔案中定義您的 conda 相依性;在此範例中，會將檔案命名為 `conda_dependencies.yml` 。

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

從這個 conda 環境規格建立 Azure ML 環境。 環境會在執行時間封裝至 Docker 容器。

依預設，如果未指定基底映射，Azure ML 會使用 CPU 映射 `azureml.core.environment.DEFAULT_CPU_IMAGE` 作為基底映射。 因為此範例會在 GPU 叢集上執行定型，所以您必須指定具有必要 GPU 驅動程式和相依性的 GPU 基礎映射。 Azure ML 會維護一組發佈于 Microsoft Container Registry (MCR) 可供您使用的基底映射，如需詳細資訊，請參閱 [Azure/AzureML 容器 GitHub 存放](https://github.com/Azure/AzureML-Containers) 庫。

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> （選擇性）您可以直接在自訂 Docker 映射或 Dockerfile 中捕捉所有相依性，並從該映射建立您的環境。 如需詳細資訊，請參閱 [使用自訂映射定型](how-to-train-with-custom-image.md)。

如需有關建立和使用環境的詳細資訊，請參閱 [在 Azure Machine Learning 中建立和使用軟體環境](how-to-use-environments.md)。

## <a name="configure-and-submit-your-training-run"></a>設定並提交定型回合

### <a name="create-a-scriptrunconfig"></a>建立 ScriptRunConfig

建立 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) 物件，以指定定型作業的組態詳細資料，包括您的定型指令碼、要使用的環境，以及要在其上執行的計算目標。 如果您在參數中指定，將會透過命令列傳遞定型腳本的任何引數 `arguments` 。

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning 藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的機密資料，請使用 [. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 檔案，或不要將它包含在來原始目錄中。 相反地，請使用 Azure ML [資料集](how-to-train-with-datasets.md)來存取您的資料。

如需使用 ScriptRunConfig 設定作業的詳細資訊，請參閱 [設定和提交定型](how-to-set-up-training-targets.md)回合。

> [!WARNING]
> 如果您先前使用 TensorFlow 估算器來設定 TensorFlow 訓練作業，請注意估算器已在 1.19.0 SDK 版本中被取代。 使用 Azure ML SDK >= 1.15.0，ScriptRunConfig 是設定定型作業的建議方式，包括使用深度學習架構的作業。 如需常見的遷移問題，請參閱 [估算器至 ScriptRunConfig 遷移指南](how-to-migrate-from-estimators-to-scriptrunconfig.md)。

### <a name="submit-a-run"></a>提交執行

當作業正在執行時， [執行物件](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) 會提供介面給執行歷程記錄，並在完成後提供。

```Python
run = Experiment(workspace=ws, name='tf-mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>執行期間發生的情況
執行執行時，它會經歷下列階段：

- **準備**：根據定義的環境建立 docker 映射。 映射上傳至工作區的容器登錄，並快取以供稍後執行。 記錄也會串流至執行歷程記錄，並可加以查看以監視進度。 如果改為指定策劃環境，則會使用支援該策劃環境的快取映射。

- **調整**：如果 Batch AI 叢集需要更多節點來執行執行比目前可用的節點，則叢集會嘗試擴大規模。

- 執行 **中：腳本** 資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區，並 `script` 執行。 Stdout 和 **./logs** 資料夾的輸出會串流處理至執行歷程記錄，並可用來監視執行。

- **後續處理**：執行的 **./outputs** 資料夾會複製到執行歷程記錄。

## <a name="register-or-download-a-model"></a>註冊或下載模型

當您將模型定型之後，就可以將它註冊到您的工作區。 註冊模型可讓您在工作區中儲存模型並設定其版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。 選擇性：藉由指定參數 `model_framework` 、 `model_framework_version` 和 `resource_configuration` ，將無法使用任何程式碼模型部署。 這可讓您直接從註冊的模型將模型部署為 web 服務，而物件會 `ResourceConfiguration` 定義 web 服務的計算資源。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

您也可以使用 Run 物件來下載模型的本機複本。 在定型腳本中 `tf_mnist.py` ，TensorFlow 保護物件會將模型保存到計算目標)  (本機的本機資料夾。 您可以使用 [執行] 物件下載複本。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>分散式定型

Azure Machine Learning 也支援多節點分散式 TensorFlow 作業，讓您可以調整定型工作負載。 您可以輕鬆地執行分散式 TensorFlow 作業，Azure ML 將會為您管理協調流程。

Azure ML 支援使用 Horovod 和 TensorFlow 的內建分散式訓練 API 來執行分散式 TensorFlow 作業。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) 是一個開放原始碼，可針對 Uber 所開發的分散式訓練進行縮減架構。 它提供簡單的途徑來撰寫分散式 TensorFlow 程式碼以進行定型。

您的定型程式碼必須使用 Horovod 進行檢測，以進行分散式訓練。 如需搭配使用 Horovod 與 TensorFlow 的詳細資訊，請參閱 Horovod 檔：

如需搭配使用 Horovod 與 TensorFlow 的詳細資訊，請參閱 Horovod 檔：

* [使用 TensorFlow 的 Horovod](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod 與 TensorFlow 的 Keras API](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

此外，請確定您的訓練環境包含 **horovod** 套件。 如果您使用 TensorFlow 策劃環境，則 horovod 已包含為其中一個相依性。 如果您使用自己的環境，請確定已包含 horovod 相依性，例如：

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

若要在 Azure ML 上使用 MPI/Horovod 執行分散式作業，您必須對 ScriptRunConfig 函式的參數指定 [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) `distributed_job_config` 。 下列程式碼會設定兩個節點的分散式作業，每個節點執行一個進程。 如果您也想要針對每個節點執行多個進程 (也就是，如果您的叢集 SKU) 有多個 Gpu，請另外 `process_count_per_node` 在 MpiConfiguration 中指定參數 (預設值為 `1`) 。

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

如需在 Azure ML 上搭配 Horovod 執行分散式 TensorFlow 的完整教學課程，請參閱 [使用 Horovod 的分散式 TensorFlow](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod)。

### <a name="tfdistribute"></a>tf. 散發

如果您在定型程式碼中使用 [原生分散式 TensorFlow](https://www.tensorflow.org/guide/distributed_training) ，例如 TensorFlow 2.X 的 `tf.distribute.Strategy` API，您也可以透過 Azure ML 啟動分散式作業。 

若要這樣做，請將 [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py) 指定為 ScriptRunConfig 函式的 `distributed_job_config` 參數。 如果您使用 `tf.distribute.experimental.MultiWorkerMirroredStrategy` ，請 `worker_count` 在 TensorflowConfiguration 中指定對應于定型作業節點數目的。

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

在 TensorFlow 中， `TF_CONFIG` 需要有環境變數，才能在多部電腦上進行定型。 Azure ML 會 `TF_CONFIG` 在執行定型腳本之前，為每個背景工作設定並適當地設定變數。 如果需要，您可以 `TF_CONFIG` 從訓練腳本進行存取 `os.environ['TF_CONFIG']` 。

`TF_CONFIG`主要背景工作節點上設定的範例結構：
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

如果您的定型腳本使用參數伺服器策略進行分散式定型，也就是針對舊版 TensorFlow 1.x，您也必須指定要在作業中使用的參數伺服器數目，例如 `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` 。

## <a name="deploy-a-tensorflow-model"></a>部署 TensorFlow 模型

部署如何包含註冊模型的區段，但您可以直接跳到建立部署的 [計算目標](how-to-deploy-and-where.md#choose-a-compute-target) ，因為您已經有已註冊的模型。

### <a name="preview-no-code-model-deployment"></a> (Preview) 無程式碼模型部署

您也可以使用 TensorFlow 的「無程式碼部署」功能 (preview) ，而不是傳統的部署路由。 藉由使用、和參數來註冊您的模型（如上所示 `model_framework` `model_framework_version` `resource_configuration` ），您可以直接使用靜態函式 `deploy()` 來部署模型。

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

[完整的作法涵蓋了 Azure Machine Learning](how-to-deploy-and-where.md)更深入的部署。

## <a name="next-steps"></a>下一步

在本文中，您已訓練並註冊 TensorFlow 模型，並瞭解部署的選項。 若要深入瞭解 Azure Machine Learning，請參閱這些其他文章。

* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [Azure 中分散式深度學習訓練的參考架構](/azure/architecture/reference-architectures/ai/training-deep-learning)
