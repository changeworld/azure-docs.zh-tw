---
title: 訓練深度學習 Keras 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 來定型和註冊在 TensorFlow 上執行的 Keras 深度類神經網路分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: d7cd452b6d1107f440d952c7db930281f3d86c11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743793"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>使用 Azure Machine Learning 大規模定型 Keras 模型

在本文中，您將瞭解如何使用 Azure Machine Learning 執行您的 Keras 訓練腳本。

本文中的範例程式碼會示範如何定型和註冊使用 TensorFlow 後端搭配 Azure Machine Learning 建立的 Keras 分類模型。 它使用熱門的[MNIST 資料集](http://yann.lecun.com/exdb/mnist/)，使用深度類神經網路來分類手寫數位 (DNN) 使用在[TensorFlow](https://www.tensorflow.org/overview)之上執行的[Keras Python 程式庫](https://keras.io)來建立。

Keras 是一種高階神經網路 API，能夠執行其他熱門的 DNN 架構，以簡化開發工作。 有了 Azure Machine Learning，您就可以使用彈性的雲端計算資源，快速地相應放大定型作業。 您也可以追蹤定型執行、版本模型、部署模型等。

無論您是從頭開始開發 Keras 模型，或是將現有的模型帶入雲端，Azure Machine Learning 都能協助您建立可供生產環境使用的模型。

> [!NOTE]
> 如果您使用內建于 TensorFlow 中的 Keras API **tf. Keras** ，而不是獨立 Keras 套件，請改為參閱 [定型 TensorFlow 模型](how-to-train-tensorflow.md)。

## <a name="prerequisites"></a>必要條件

在下列任一環境中執行此程式碼：

- Azure Machine Learning 計算執行個體 - 不需要下載或安裝

     - 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
    - 在筆記本伺服器的 [samples] 資料夾中，流覽至下列目錄以尋找已完成和已展開的筆記本： **如何使用 azureml > ml-架構 > keras > 超參數-keras** 資料夾。

 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) ( # B0 = 1.15.0) 。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。
    - [下載範例指令檔](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` 和 `utils.py`

    您也可以在 GitHub 範例頁面上找到本指南的完整 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) 。 筆記本包含擴充的章節，涵蓋智慧型超參數微調、模型部署和筆記本 widget。

## <a name="set-up-the-experiment"></a>設定實驗

本節會透過載入所需的 Python 套件、初始化工作區、建立輸入定型資料的 FileDataset、建立計算目標，以及定義定型環境，來設定定型實驗。

### <a name="import-packages"></a>匯入套件

首先，匯入必要的 Python 程式庫。

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure Machine Learning 工作區](concept-workspace.md)是服務的最上層資源。 其可提供集中式位置以處理您建立的所有成品。 在 Python SDK 中，您可以藉由建立物件來存取工作區成品 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) 。

從「 `config.json` [必要條件」一節](#prerequisites)中建立的檔案建立工作區物件。

```Python
ws = Workspace.from_config()
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

您可以使用 `register()` 方法將資料集註冊到您的工作區，讓它們可以與其他人共用、重複使用於各種不同的實驗，並在定型腳本中依名稱參考。

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>建立計算目標

建立要在其中執行定型作業的計算目標。 在此範例中，請建立已啟用 GPU 的 Azure Machine Learning 計算叢集。

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

定義封裝定型腳本相依性的 Azure ML [環境](concept-environments.md) 。

首先，在 YAML 檔案中定義您的 conda 相依性;在此範例中，會將檔案命名為 `conda_dependencies.yml` 。

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

從這個 conda 環境規格建立 Azure ML 環境。 環境會在執行時間封裝至 Docker 容器。

依預設，如果未指定基底映射，Azure ML 會使用 CPU 映射 `azureml.core.environment.DEFAULT_CPU_IMAGE` 作為基底映射。 因為此範例會在 GPU 叢集上執行定型，所以您必須指定具有必要 GPU 驅動程式和相依性的 GPU 基礎映射。 Azure ML 會維護一組發佈于 Microsoft Container Registry (MCR) 可供您使用的基底映射，如需詳細資訊，請參閱 [Azure/AzureML 容器 GitHub 存放](https://github.com/Azure/AzureML-Containers) 庫。

```python
from azureml.core import Environment

keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

如需有關建立和使用環境的詳細資訊，請參閱 [在 Azure Machine Learning 中建立和使用軟體環境](how-to-use-environments.md)。

## <a name="configure-and-submit-your-training-run"></a>設定並提交定型回合

### <a name="create-a-scriptrunconfig"></a>建立 ScriptRunConfig
首先，使用類別從工作區資料存放區取得資料 `Dataset` 。

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

建立 ScriptRunConfig 物件，以指定定型作業的組態詳細資料，包括您的定型指令碼、要使用的環境，以及要在其上執行的計算目標。

如果您在參數中指定，將會透過命令列傳遞定型腳本的任何引數 `arguments` 。 FileDataset 的 DatasetConsumptionConfig 會作為引數傳遞至定型腳本的引數 `--data-folder` 。 Azure ML 會將此 DatasetConsumptionConfig 解析為支援資料存放區的掛接點，然後可從定型腳本進行存取。

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

如需使用 ScriptRunConfig 設定作業的詳細資訊，請參閱 [設定和提交定型](how-to-set-up-training-targets.md)回合。

> [!WARNING]
> 如果您先前使用 TensorFlow 估算器來設定您的 Keras 訓練作業，請注意，在未來的 Azure ML SDK 版本中，估算器將會被取代。 使用 Azure ML SDK >= 1.15.0，ScriptRunConfig 是設定定型作業（包括使用 DL 架構）的建議方式。

### <a name="submit-your-run"></a>提交您的執行

當作業正在執行時， [執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) 會提供介面給執行歷程記錄，並在完成後提供。

```Python
run = Experiment(workspace=ws, name='keras-mnist').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>執行期間發生的情況
執行執行時，它會經歷下列階段：

- **準備**：根據定義的環境建立 docker 映射。 映射上傳至工作區的容器登錄，並快取以供稍後執行。 記錄也會串流至執行歷程記錄，並可加以查看以監視進度。 如果改為指定策劃環境，則會使用支援該策劃環境的快取映射。

- **調整**：如果 Batch AI 叢集需要更多節點來執行執行比目前可用的節點，則叢集會嘗試擴大規模。

- 執行**中：腳本**資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區，並 `script` 執行。 Stdout 和 **./logs** 資料夾的輸出會串流處理至執行歷程記錄，並可用來監視執行。

- **後續處理**：執行的 **./outputs** 資料夾會複製到執行歷程記錄。

## <a name="register-the-model"></a>註冊模型

當您將模型定型之後，就可以將它註冊到您的工作區。 註冊模型可讓您在工作區中儲存模型並設定其版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> 部署如何包含註冊模型的區段，但您可以直接跳到建立部署的 [計算目標](how-to-deploy-and-where.md#choose-a-compute-target) ，因為您已經有已註冊的模型。

您也可以下載模型的本機複本。 這有助於在本機進行額外的模型驗證工作。 在定型腳本中， `keras_mnist.py` TensorFlow 保護物件會將模型保存到計算目標)  (本機的本機資料夾。 您可以使用 [執行] 物件，從執行歷程記錄下載複本。

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

在本文中，您已在 Azure Machine Learning 上訓練並註冊 Keras 模型。 若要瞭解如何部署模型，請繼續進行我們的模型部署文章。

* [部署模型的方式和位置](how-to-deploy-and-where.md)
* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中分散式深度學習訓練的參考架構](/azure/architecture/reference-architectures/ai/training-deep-learning)
