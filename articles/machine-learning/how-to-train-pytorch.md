---
title: 訓練深度學習 PyTorch 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning，以企業規模執行您的 PyTorch 訓練腳本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 12/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: ed368615395614bc0d3e9a6f06727da8c64d8486
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559636"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>使用 Azure Machine Learning 大規模定型 PyTorch 模型

在本文中，您將瞭解如何使用 Azure Machine Learning，以企業規模執行您的 [PyTorch](https://pytorch.org/) 訓練腳本。

本文中的範例腳本可用來將雞和土耳其映射分類，以根據 PyTorch 的轉移學習 [教學](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)課程，建立深度學習神經網路 (DNN) 。 轉移學習是一種技術，可從解決一個問題到不同但相關的問題，來運用所獲得的知識。 這種定型流程的快捷方式，是要求比從頭訓練更少的資料、時間和計算資源。

無論您是從頭開始訓練深度學習 PyTorch 模型，或是將現有的模型帶到雲端，您都可以使用 Azure Machine Learning，利用彈性的雲端計算資源來擴充開放原始碼訓練作業。 您可以使用 Azure Machine Learning 來建立、部署、版本和監視生產等級的模型。 

## <a name="prerequisites"></a>必要條件

在下列任一環境中執行此程式碼：

- Azure Machine Learning 計算執行個體 - 不需要下載或安裝

    - 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
    - 在筆記本伺服器的 [範例深度學習] 資料夾中，流覽至下列目錄以找出已完成和展開的筆記本： **如何使用 azureml > ml-架構 > pytorch > 超參數-pytorch** 資料夾。 
 
 - 您自己的 Jupyter Notebook 伺服器
    - [安裝 AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) ( # B0 = 1.15.0) 。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。
    - [下載範例指令檔](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    您也可以在 GitHub 範例頁面上找到本指南的完整 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) 。 筆記本包含擴充的章節，涵蓋智慧型超參數微調、模型部署和筆記本 widget。

## <a name="set-up-the-experiment"></a>設定實驗

本節會藉由載入所需的 Python 套件、初始化工作區、建立計算目標，以及定義定型環境，來設定定型實驗。

### <a name="import-packages"></a>匯入套件

首先，匯入必要的 Python 程式庫。

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
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

### <a name="get-the-data"></a>取得資料

此資料集包含每個 turkeys 和 chickens 的大約120定型影像，每個類別都有100個驗證影像。 我們會將資料集下載並解壓縮，作為定型腳本的一部分 `pytorch_train.py` 。 映射是 [Open images V5 資料集](https://storage.googleapis.com/openimages/web/index.html)的子集。

### <a name="prepare-training-script"></a>準備訓練腳本

在本教學課程中， `pytorch_train.py` 已提供訓練腳本。 在實務上，您可以採用任何自訂定型腳本，也可以使用 Azure Machine Learning 來執行它。

為您的定型腳本 (s) 建立資料夾。

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>建立計算目標

建立要在其上執行之 PyTorch 作業的計算目標。 在此範例中，請建立已啟用 GPU 的 Azure Machine Learning 計算叢集。

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
如果您不想要定義自己的環境，Azure ML 會提供預建的策劃環境。 Azure ML 有數個 CPU 和 GPU 策劃環境，適用于 PyTorch 的不同 PyTorch 版本。 如需詳細資訊，請參閱 [這裡](resource-curated-environments.md)。

如果您想要使用策劃環境，您可以改為執行下列命令：

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

若要查看策劃環境中包含的套件，您可以將 conda 相依性寫出至磁片：
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

請確定策劃環境包含定型腳本所需的所有相依性。 如果沒有，您就必須修改環境以包含遺漏的相依性。 請注意，如果環境已修改，您必須為它指定新的名稱，因為 ' AzureML ' 首碼保留給策劃環境。 如果您修改了 conda 相依性 YAML 檔案，您可以使用新名稱來建立新的環境，例如：
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

如果您改為直接修改策劃環境物件，您可以使用新的名稱來複製該環境：
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
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
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

從這個 conda 環境規格建立 Azure ML 環境。 環境會在執行時間封裝至 Docker 容器。

依預設，如果未指定基底映射，Azure ML 會使用 CPU 映射 `azureml.core.environment.DEFAULT_CPU_IMAGE` 作為基底映射。 因為此範例會在 GPU 叢集上執行定型，所以您必須指定具有必要 GPU 驅動程式和相依性的 GPU 基礎映射。 Azure ML 會維護一組發佈于 Microsoft Container Registry (MCR) 可供您使用的基底映射，如需詳細資訊，請參閱 [Azure/AzureML 容器 GitHub 存放](https://github.com/Azure/AzureML-Containers) 庫。

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> （選擇性）您可以直接在自訂 Docker 映射或 Dockerfile 中捕捉所有相依性，並從該映射建立您的環境。 如需詳細資訊，請參閱 [使用自訂映射定型](how-to-train-with-custom-image.md)。

如需有關建立和使用環境的詳細資訊，請參閱 [在 Azure Machine Learning 中建立和使用軟體環境](how-to-use-environments.md)。

## <a name="configure-and-submit-your-training-run"></a>設定並提交定型回合

### <a name="create-a-scriptrunconfig"></a>建立 ScriptRunConfig

建立 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) 物件，以指定定型作業的組態詳細資料，包括您的定型指令碼、要使用的環境，以及要在其上執行的計算目標。 如果您在參數中指定，將會透過命令列傳遞定型腳本的任何引數 `arguments` 。 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning 藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的機密資料，請使用 [. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 檔案，或不要將它包含在來原始目錄中。 相反地，請使用 Azure ML [資料集](how-to-train-with-datasets.md)來存取您的資料。

如需使用 ScriptRunConfig 設定作業的詳細資訊，請參閱 [設定和提交定型](how-to-set-up-training-targets.md)回合。

> [!WARNING]
> 如果您先前使用 PyTorch 估算器來設定您的 PyTorch 訓練作業，請注意，在未來的 Azure ML SDK 版本中，估算器將會被取代。 使用 Azure ML SDK >= 1.15.0，ScriptRunConfig 是設定定型作業（包括使用 DL 架構）的建議方式。

## <a name="submit-your-run"></a>提交您的執行

當作業正在執行時， [執行物件](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) 會提供介面給執行歷程記錄，並在完成後提供。

```Python
run = Experiment(ws, name='pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>執行期間發生的情況
執行執行時，它會經歷下列階段：

- **準備**：根據定義的環境建立 docker 映射。 映射上傳至工作區的容器登錄，並快取以供稍後執行。 記錄也會串流至執行歷程記錄，並可加以查看以監視進度。 如果改為指定策劃環境，則會使用支援該策劃環境的快取映射。

- **調整**：如果 Batch AI 叢集需要更多節點來執行執行比目前可用的節點，則叢集會嘗試擴大規模。

- 執行 **中：腳本** 資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區，並 `script` 執行。 Stdout 和 **./logs** 資料夾的輸出會串流處理至執行歷程記錄，並可用來監視執行。

- **後續處理**：執行的 **./outputs** 資料夾會複製到執行歷程記錄。

## <a name="register-or-download-a-model"></a>註冊或下載模型

當您將模型定型之後，就可以將它註冊到您的工作區。 註冊模型可讓您在工作區中儲存模型並設定其版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> 部署如何包含註冊模型的區段，但您可以直接跳到建立部署的 [計算目標](how-to-deploy-and-where.md#choose-a-compute-target) ，因為您已經有已註冊的模型。

您也可以使用 Run 物件來下載模型的本機複本。 在定型腳本中 `pytorch_train.py` ，PyTorch 儲存物件會將模型保存到計算目標) 的本機資料夾 (本機。 您可以使用 [執行] 物件下載複本。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>分散式定型

Azure Machine Learning 也支援多節點分散式 PyTorch 作業，讓您可以調整定型工作負載。 您可以輕鬆地執行分散式 PyTorch 作業，Azure ML 將會為您管理協調流程。

Azure ML 支援使用 Horovod 和 PyTorch 的內建 DistributedDataParallel 模組來執行分散式 PyTorch 作業。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) 是一個開放原始碼，可針對 Uber 所開發的分散式訓練進行縮減架構。 它提供簡單的途徑來撰寫分散式 PyTorch 程式碼以進行定型。

您的定型程式碼必須使用 Horovod 進行檢測，以進行分散式訓練。 如需搭配使用 Horovod 與 PyTorch 的詳細資訊，請參閱 [Horovod 檔](https://horovod.readthedocs.io/en/stable/pytorch.html)。

此外，請確定您的訓練環境包含 **horovod** 套件。 如果您使用 PyTorch 策劃環境，則 horovod 已包含為其中一個相依性。 如果您使用自己的環境，請確定已包含 horovod 相依性，例如：

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

若要在 Azure ML 上使用 MPI/Horovod 執行分散式作業，您必須對 ScriptRunConfig 函式的參數指定 [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) `distributed_job_config` 。 下列程式碼會設定兩個節點的分散式作業，每個節點執行一個進程。 如果您也想要針對每個節點執行多個進程 (也就是，如果您的叢集 SKU) 有多個 Gpu，請另外 `process_count_per_node` 在 MpiConfiguration 中指定參數 (預設值為 `1`) 。

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

如需在 Azure ML 上搭配 Horovod 執行分散式 PyTorch 的完整教學課程，請參閱 [使用 Horovod 的分散式 PyTorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod)。

### <a name="distributeddataparallel"></a>DistributedDataParallel
如果您使用 PyTorch 的內建 [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) 模組，該模組是使用您定型程式碼中的 **torch 散發** 套件所建立，您也可以透過 Azure ML 啟動分散式作業。

若要使用 DistributedDataParallel 執行分散式 PyTorch 作業，請指定 ScriptRunConfig 函[](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py)式 `distributed_job_config` 之參數的 PyTorchConfiguration。 若要使用 torch 的 NCCL 後端，請 `communication_backend='Nccl'` 在 PyTorchConfiguration 中指定。 下列程式碼將設定2個節點的分散式工作。 NCCL 後端是建議用來 PyTorch 分散式 GPU 訓練的後端。

針對透過 PyTorchConfiguration 設定的分散式 PyTorch 作業，Azure ML 會在計算目標的節點上設定下列環境變數：

* `AZ_BATCHAI_PYTORCH_INIT_METHOD`：進程群組的共用檔案系統初始化 URL
* `AZ_BATCHAI_TASK_INDEX`：背景工作進程的全域排名

您可以透過 ScriptRunConfig 的參數，將這些環境變數指定給定型腳本的對應引數 `arguments` 。

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

args = ['--dist-backend', 'nccl',
        '--dist-url', '$AZ_BATCHAI_PYTORCH_INIT_METHOD',
        '--rank', '$AZ_BATCHAI_TASK_INDEX',
        '--world-size', 2]

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=PyTorchConfiguration(communication_backend='Nccl', node_count=2))
```

如果您想要使用 Gloo 後端來進行分散式訓練，請改為指定 `communication_backend='Gloo'` 。 建議使用 Gloo 後端來進行分散式 CPU 定型。

如需在 Azure ML 上執行分散式 PyTorch 的完整教學課程，請參閱 [使用 DistributedDataParallel 的分散式 PyTorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-nccl-gloo)。

## <a name="export-to-onnx"></a>匯出至 ONNX

若要將 [ONNX 運行](concept-onnx.md)時間的推斷優化，請將您定型的 PyTorch 模型轉換成 ONNX 格式。 推斷或模型計分是部署的模型用於預測的階段，最常見的是生產資料。 如需範例，請參閱 [教學](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) 課程。

## <a name="next-steps"></a>後續步驟

在本文中，您已使用 PyTorch 在 Azure Machine Learning 上定型和註冊深度學習和類神經網路。 若要瞭解如何部署模型，請繼續進行我們的模型部署文章。

* [部署模型的方式和位置](how-to-deploy-and-where.md)
* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中分散式深度學習訓練的參考架構](/azure/architecture/reference-architectures/ai/training-deep-learning)
