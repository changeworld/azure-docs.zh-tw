---
title: 訓練深度學習 PyTorch 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 的 PyTorch 估算器類別，以企業規模執行您的 PyTorch 訓練腳本。  範例腳本會將雞和土耳其影像分類，以根據 PyTorch 的轉移學習教學課程建立深度學習神經網路。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 4b801552534bcb69afe426cce4aa109bf942000b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90893144"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>使用 Azure Machine Learning 大規模定型 Pytorch 深度學習模型


在本文中，您將瞭解如何使用 Azure Machine Learning 的[PyTorch 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py&preserve-view=true)類別，以企業規模執行您的[PyTorch](https://pytorch.org/)訓練腳本。  

本文中的範例腳本可用來將雞和土耳其影像分類，以根據 PyTorch 的 [轉移學習教學](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)課程建立深度學習神經網路。 

無論您是從頭開始訓練深度學習 PyTorch 模型，或是將現有的模型帶到雲端，您都可以使用 Azure Machine Learning，利用彈性的雲端計算資源來擴充開放原始碼訓練作業。 您可以使用 Azure Machine Learning 來建立、部署、版本和監視生產等級的模型。 

深入瞭解 [與機器學習的深度學習](concept-deep-learning-vs-machine-learning.md)。

## <a name="prerequisites"></a>必要條件

在下列任一環境中執行此程式碼：

- Azure Machine Learning 計算執行個體 - 不需要下載或安裝

    - 完成[教學課程：設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
    - 在筆記本伺服器的 [範例深度學習] 資料夾中，流覽至下列目錄以找出已完成和展開的筆記本： **如何使用-azureml > 訓練-深度學習 > 超參數-pytorch** ] 資料夾。 
 
 - 您自己的 Jupyter Notebook 伺服器

    - [安裝 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。
    - [建立工作區設定檔](how-to-configure-environment.md#workspace)。
    - [下載範例指令檔](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    您也可以在 GitHub 範例頁面上找到本指南的完整 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) 。 筆記本包含擴充的章節，涵蓋智慧型超參數微調、模型部署和筆記本 widget。

## <a name="set-up-the-experiment"></a>設定實驗

本節藉由載入所需的 python 套件、初始化工作區、建立實驗，以及上傳定型資料和定型腳本，來設定定型實驗。

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

[Azure Machine Learning 工作區](concept-workspace.md)是服務的最上層資源。 其可提供集中式位置以處理您建立的所有成品。 在 Python SDK 中，您可以藉由建立物件來存取工作區成品 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) 。

從「 `config.json` [必要條件」一節](#prerequisites)中建立的檔案建立工作區物件。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>建立深度學習實驗

建立實驗和資料夾來保存訓練腳本。 在此範例中，請建立名為「pytorch 鳥」的實驗。

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>取得資料

此資料集包含每個 turkeys 和 chickens 的大約120定型影像，每個類別都有100個驗證影像。 我們會將資料集下載並解壓縮，作為定型腳本的一部分 `pytorch_train.py` 。 映射是 [Open images V5 資料集](https://storage.googleapis.com/openimages/web/index.html)的子集。

### <a name="prepare-training-scripts"></a>準備訓練腳本

在本教學課程中， `pytorch_train.py` 已提供訓練腳本。 在實務上，您可以採用任何自訂定型腳本，也可以使用 Azure Machine Learning 來執行它。

上傳 Pytorch 訓練腳本 `pytorch_train.py` 。

```Python
shutil.copy('pytorch_train.py', project_folder)
```

但是，如果您想要使用 Azure Machine Learning 追蹤和計量功能，則必須在定型腳本內新增少量的程式碼。 您可以在中找到計量追蹤的範例 `pytorch_train.py` 。

## <a name="create-a-compute-target"></a>建立計算目標

建立要在其上執行之 PyTorch 作業的計算目標。 在此範例中，請建立已啟用 GPU 的 Azure Machine Learning 計算叢集。

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

## <a name="create-a-pytorch-estimator"></a>建立 PyTorch 估算器

[PyTorch 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py&preserve-view=true)提供簡單的方法，讓您在計算目標上啟動 PyTorch 訓練作業。

PyTorch 估算器是透過 [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) 可用於支援任何架構的泛型類別來執行。 如需如何使用泛型估算器來將模型定型的詳細資訊，請參閱[藉由估算器使用 Azure Machine Learning 將模型定型](how-to-train-ml-models.md)

如果您的定型腳本需要額外的 pip 或 conda 封裝來執行，您可以透過 `pip_packages` 和引數傳遞這些封裝的名稱，將套件安裝在產生的 docker 映射上 `conda_packages` 。

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
> Azure Machine Learning 藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的機密資料，請使用 [. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 檔案，或不要將它包含在來原始目錄中。 相反地， [使用資料存放](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)區存取您的資料。

如需自訂 Python 環境的詳細資訊，請參閱[建立和管理用於訓練和部署的環境](how-to-use-environments.md)。

## <a name="submit-a-run"></a>提交執行

當作業正在執行時， [執行物件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) 會提供介面給執行歷程記錄，並在完成後提供。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

執行執行時，它會經歷下列階段：

- **準備**：根據 PyTorch 估算器建立 docker 映射。 映射上傳至工作區的容器登錄，並快取以供稍後執行。 記錄也會串流至執行歷程記錄，並可加以查看以監視進度。

- **調整**：如果 Batch AI 叢集需要更多節點來執行執行比目前可用的節點，則叢集會嘗試擴大規模。

- **執行中：腳本**資料夾中的所有腳本都會上傳至計算目標、裝載或複製資料存放區，並執行 entry_script。 Stdout 和./logs 資料夾的輸出會串流處理至執行歷程記錄，並可用來監視執行。

- **後續處理**：執行的./outputs 資料夾會複製到執行歷程記錄。

## <a name="register-or-download-a-model"></a>註冊或下載模型

當您將模型定型之後，就可以將它註冊到您的工作區。 註冊模型可讓您在工作區中儲存模型並設定其版本，以簡化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> 您剛剛註冊的模型會以與 Azure Machine Learning 中任何其他已註冊模型完全相同的方式進行部署，不論您使用哪個估算器來進行定型。 部署如何包含註冊模型的區段，但您可以直接跳到建立部署的 [計算目標](how-to-deploy-and-where.md#choose-a-compute-target) ，因為您已經有已註冊的模型。

您也可以使用 Run 物件來下載模型的本機複本。 在定型腳本中 `pytorch_train.py` ，PyTorch 儲存物件會將模型保存到計算目標) 的本機資料夾 (本機。 您可以使用 [執行] 物件下載複本。

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

[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py&preserve-view=true)估算器也支援跨 CPU 和 GPU 叢集的分散式定型。 您可以輕鬆地執行分散式 PyTorch 作業，Azure Machine Learning 將會為您管理協調流程。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) 是一個開放原始碼，可針對 Uber 所開發的分散式訓練進行縮減架構。 它提供分散式 GPU PyTorch 作業的簡單路徑。

若要使用 Horovod，請 [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) 在 PyTorch 的函式中指定參數的物件 `distributed_training` 。 此參數可確保已安裝 Horovod 程式庫，供您在定型腳本中使用。


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.4',
                      use_gpu=True)
```
系統會為您安裝 Horovod 及其相依性，因此您可以將它匯入定型腳本中，如下所示 `train.py` ：

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>匯出至 ONNX

若要將 [ONNX 運行](concept-onnx.md)時間的推斷優化，請將您定型的 PyTorch 模型轉換成 ONNX 格式。 推斷或模型計分是部署的模型用於預測的階段，最常見的是生產資料。 如需範例，請參閱 [教學](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) 課程。

## <a name="next-steps"></a>下一步

在本文中，您已使用 PyTorch 在 Azure Machine Learning 上定型和註冊深度學習和類神經網路。 若要瞭解如何部署模型，請繼續進行我們的模型部署文章。

> [!div class="nextstepaction"]
> [部署模型的方式和位置](how-to-deploy-and-where.md)
* [追蹤定型期間的執行計量](how-to-track-experiments.md)
* [調整超參數](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中分散式深度學習訓練的參考架構](/azure/architecture/reference-architectures/ai/training-deep-learning)

