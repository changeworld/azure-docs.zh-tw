---
title: 訓練深度學習 PyTorch 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 的 PyTorch 估計工具類別，以企業規模執行您的 PyTorch 訓練腳本。  範例腳本會將雞和土耳其影像分類，以根據 PyTorch 的傳輸學習教學課程來建立深度學習神經網路。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7f805a702e85b897f9807eb2294e4263a0ea2d8c
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876320"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>使用 Azure Machine Learning 以大規模訓練 Pytorch 深度學習模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何使用 Azure Machine Learning 的[PyTorch 估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)類別，以企業規模執行您的[PyTorch](https://pytorch.org/)訓練腳本。  

本文中的範例腳本可用來將雞和土耳其影像分類，以根據 PyTorch 的[傳輸學習教學](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)課程來建立深度學習神經網路。 

無論您是從基礎開始訓練深度學習 PyTorch 模型，或是將現有的模型帶入雲端，都可以使用 Azure Machine Learning，使用彈性雲端計算資源來相應放大開放原始碼訓練作業。 您可以使用 Azure Machine Learning 來建立、部署、版本及監視生產等級模型。 

[深入瞭解深度學習與機器學習](concept-deep-learning-vs-machine-learning.md)服務。

## <a name="prerequisites"></a>必要條件

在下列任一環境中執行此程式碼：

- Azure Machine Learning 計算執行個體 - 不需要下載或安裝

    - 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
    - 在筆記本伺服器上的範例深入學習資料夾中，流覽至此目錄以尋找已完成且已展開的筆記本：**使用方法 > 訓練-具有深度學習 > 訓練-超參數-微調-部署-使用-pytorch**資料夾。 
 
 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。
    - [下載範例腳本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)檔案`pytorch_train.py`
     
    您也可以在 GitHub 範例頁面上找到本指南的完整[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb)。 此筆記本包含擴充的章節，涵蓋智慧型超參數微調、模型部署和筆記本 widget。

## <a name="set-up-the-experiment"></a>設定實驗

本節會藉由載入所需的 python 套件、將工作區初始化、建立實驗，以及上傳定型資料和定型腳本，來設定定型實驗。

### <a name="import-packages"></a>匯入套件

首先，匯入必要的 Python 程式庫。

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

[Azure Machine Learning 工作區](concept-workspace.md)是服務的最上層資源。 其可提供集中式位置以處理您建立的所有成品。 在 Python SDK 中，您可以藉由建立物件來存取工作區構件 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 。

從 `config.json` [必要條件一節](#prerequisites)中建立的檔案建立工作區物件。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>建立深度學習實驗

建立實驗和資料夾，以保存您的訓練腳本。 在此範例中，建立名為「pytorch-鳥」的實驗。

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>取得資料

此資料集包含每個 turkeys 和 chickens 的大約120定型影像，每個類別都有100個驗證影像。 我們會下載資料集並將其解壓縮，做為定型腳本的一部分 `pytorch_train.py` 。 映射是[開啟影像 V5 資料集](https://storage.googleapis.com/openimages/web/index.html)的子集。

### <a name="prepare-training-scripts"></a>準備訓練腳本

在本教學課程中， `pytorch_train.py` 已經提供訓練腳本。 在實務上，您可以採用任何自訂訓練腳本，並以 Azure Machine Learning 執行。

上傳 Pytorch 訓練腳本 `pytorch_train.py` 。

```Python
shutil.copy('pytorch_train.py', project_folder)
```

不過，如果您想要使用 Azure Machine Learning 追蹤和計量功能，就必須在訓練腳本內新增少量的程式碼。 您可以在中找到計量追蹤的範例 `pytorch_train.py` 。

## <a name="create-a-compute-target"></a>建立計算目標

建立要在其上執行之 PyTorch 作業的計算目標。 在此範例中，建立已啟用 GPU 功能的 Azure Machine Learning 計算叢集。

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

如需計算目標的詳細資訊，請參閱[什麼是計算目標一](concept-compute-target.md)文。

## <a name="create-a-pytorch-estimator"></a>建立 PyTorch 估計工具

[PyTorch 估計工具](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)提供一種簡單的方式，在計算目標上啟動 PyTorch 訓練作業。

PyTorch 估計工具是透過泛型類別來執行 [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) ，它可以用來支援任何架構。 如需如何使用泛型估算器來將模型定型的詳細資訊，請參閱[藉由估算器使用 Azure Machine Learning 將模型定型](how-to-train-ml-models.md)

如果您的訓練腳本需要額外的 pip 或 conda 封裝來執行，您可以藉由透過 `pip_packages` 和引數傳遞它們的名稱，將套件安裝在產生的 docker 映射上 `conda_packages` 。

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

> [!WARNING]
> Azure Machine Learning 會藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的敏感性資料，請使用[. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)檔案，或不要將它包含在來原始目錄中。 相反地，請[使用資料存放](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)區來存取您的資料。

如需自訂 Python 環境的詳細資訊，請參閱[建立和管理用於訓練和部署的環境](how-to-use-environments.md)。

## <a name="submit-a-run"></a>提交執行

[執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)會在作業執行時和完成後，提供介面給執行歷程記錄。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

執行時，它會經歷下列階段：

- **準備**： docker 映射是根據 PyTorch 估計工具建立的。 映射會上傳至工作區的容器登錄，並快取以供稍後執行。 記錄也會串流處理至執行歷程記錄，並可加以查看以監視進度。

- **調整**：如果 Batch AI 叢集需要執行比目前可用的節點更多的節點，叢集會嘗試相應增加。

- **執行：腳本**資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區，以及執行 entry_script。 Stdout 和./logs 資料夾的輸出會串流處理至執行歷程記錄，並可用來監視執行。

- **後置處理**：執行的./outputs 資料夾會複製到執行歷程記錄。

## <a name="register-or-download-a-model"></a>註冊或下載模型

定型模型之後，您就可以將它註冊到您的工作區。 註冊模型可讓您在工作區中儲存模型並設定其版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> 您剛註冊的模型部署的方式與 Azure Machine Learning 中任何其他已註冊的模型完全相同，不論您使用哪一個估計工具來進行定型。 部署如何包含註冊模型的區段，但您可以直接跳到建立部署的[計算目標](how-to-deploy-and-where.md#choose-a-compute-target)，因為您已經有已註冊的模型。

您也可以使用執行物件來下載模型的本機複本。 在定型腳本中 `pytorch_train.py` ，PyTorch 儲存物件會將模型保存至計算目標)  (本機的本機資料夾。 您可以使用執行物件來下載複本。

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

[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)估計工具也支援跨 CPU 和 GPU 叢集的分散式訓練。 您可以輕鬆地執行分散式 PyTorch 作業，Azure Machine Learning 將會為您管理協調流程。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod)是一個開放原始碼，所有 Uber 所開發的分散式訓練都能縮減架構。 它提供分散式 GPU PyTorch 作業的簡單路徑。

若要使用 Horovod，請 [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) `distributed_training` 在 PyTorch 函數中指定參數的物件。 此參數可確保已安裝 Horovod 程式庫，以供您在定型腳本中使用。


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.6',
                      use_gpu=True)
```
系統會為您安裝 Horovod 及其相依性，因此您可以在定型腳本中匯入它，如下所示 `train.py` ：

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>匯出至 ONNX

若要使用[ONNX 運行](concept-onnx.md)時間優化推斷，請將定型的 PyTorch 模型轉換為 ONNX 格式。 推斷（或模型計分）是已部署的模型用於預測的階段，最常見的是生產資料。 如需範例，請參閱[教學](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)課程。

## <a name="next-steps"></a>後續步驟

在本文中，您已使用 PyTorch 在 Azure Machine Learning 上訓練並註冊深度學習、類神經網路。 若要瞭解如何部署模型，請繼續進行我們的模型部署一文。

> [!div class="nextstepaction"]
> [部署模型的方式和位置](how-to-deploy-and-where.md)
* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中分散式深度學習訓練的參考架構](/azure/architecture/reference-architectures/ai/training-deep-learning)

