---
title: 訓練深度學習 Keras 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 來定型和註冊在 TensorFlow 上執行的 Keras 深度類神經網路分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7c049b56bd72a0b59862e655da3b79f63c264fbf
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90882795"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>使用 Azure Machine Learning 定型和註冊 Keras 分類模型


本文說明如何使用 Azure Machine Learning 來定型和註冊以 TensorFlow 建立的 Keras 分類模型。 它使用熱門的[MNIST 資料集](http://yann.lecun.com/exdb/mnist/)，使用深度類神經網路來分類手寫數位 (DNN) 使用在[TensorFlow](https://www.tensorflow.org/overview)之上執行的[Keras Python 程式庫](https://keras.io)來建立。

Keras 是一種高階神經網路 API，能夠執行其他熱門的 DNN 架構，以簡化開發工作。 有了 Azure Machine Learning，您就可以使用彈性的雲端計算資源，快速地相應放大定型作業。 您也可以追蹤定型執行、版本模型、部署模型等。

無論您是從頭開始開發 Keras 模型，或是將現有的模型帶入雲端，Azure Machine Learning 都能協助您建立可供生產環境使用的模型。

如需機器學習和深度學習之間差異的詳細資訊，請參閱 [概念文章](concept-deep-learning-vs-machine-learning.md) 。

## <a name="prerequisites"></a>必要條件

在下列任一環境中執行此程式碼：

- Azure Machine Learning 計算執行個體 - 不需要下載或安裝

     - 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
    - 在筆記本伺服器的 samples 資料夾中，流覽至下列目錄以找出已完成且已展開的筆記本： **如何使用-azureml > > 定型** ----------------------------------keras 資料夾。

 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。
    - [下載範例指令檔](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` 和 `utils.py`

    您也可以在 GitHub 範例頁面上找到本指南的完整 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) 。 筆記本包含擴充的章節，涵蓋智慧型超參數微調、模型部署和筆記本 widget。

## <a name="set-up-the-experiment"></a>設定實驗

本節藉由載入所需的 python 套件、初始化工作區、建立實驗，以及上傳定型資料和定型腳本，來設定定型實驗。

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

### <a name="create-an-experiment"></a>建立實驗

在您的工作區中建立名為 "keras-mnist" 的實驗。

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>建立 TensorFlow 估算器並匯入 Keras

[TensorFlow 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true)提供簡單的方法，讓您在計算目標上啟動 TensorFlow 訓練作業。 由於 Keras 是在 TensorFlow 上執行，因此您可以使用 TensorFlow 估算器，並使用引數匯入 Keras 程式庫 `pip_packages` 。

首先，使用類別從工作區資料存放區取得資料 `Dataset` 。

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

TensorFlow 估算器是透過 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) 可用於支援任何架構的泛型類別來執行。 此外，建立 `script_params` 包含 DNN 超參數設定的字典。 如需如何使用泛型估算器來將模型定型的詳細資訊，請參閱[藉由估算器使用 Azure Machine Learning 將模型定型](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>提交執行

當作業正在執行時， [執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) 會提供介面給執行歷程記錄，並在完成後提供。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

執行執行時，它會經歷下列階段：

- **準備**：根據 TensorFlow 估算器建立 docker 映射。 映射上傳至工作區的容器登錄，並快取以供稍後執行。 記錄也會串流至執行歷程記錄，並可加以查看以監視進度。

- **調整**：如果 Batch AI 叢集需要更多節點來執行執行比目前可用的節點，則叢集會嘗試擴大規模。

- **執行中：腳本**資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區，並執行 entry_script。 Stdout 和./logs 資料夾的輸出會串流處理至執行歷程記錄，並可用來監視執行。

- **後續處理**：執行的./outputs 資料夾會複製到執行歷程記錄。

## <a name="register-the-model"></a>註冊模型

在您定型 DNN 模型之後，就可以將它註冊到您的工作區。 註冊模型可讓您在工作區中儲存模型並設定其版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> 您剛剛註冊的模型會以與 Azure Machine Learning 中任何其他已註冊模型完全相同的方式進行部署，不論您使用哪個估算器來進行定型。 部署如何包含註冊模型的區段，但您可以直接跳到建立部署的 [計算目標](how-to-deploy-and-where.md#choose-a-compute-target) ，因為您已經有已註冊的模型。

您也可以下載模型的本機複本。 這有助於在本機進行額外的模型驗證工作。 在定型腳本中， `mnist-keras.py` TensorFlow 保護物件會將模型保存到計算目標)  (本機的本機資料夾。 您可以使用 [執行] 物件從資料存放區下載複本。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>下一步

在本文中，您已在 Azure Machine Learning 上訓練並註冊 Keras 模型。 若要瞭解如何部署模型，請繼續進行我們的模型部署文章。

> [!div class="nextstepaction"]
> [部署模型的方式和位置](how-to-deploy-and-where.md)
* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中分散式深度學習訓練的參考架構](/azure/architecture/reference-architectures/ai/training-deep-learning)
