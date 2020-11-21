---
title: 使用自訂 Docker 映射將模型定型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用您自己的 Docker 映射，或從 Microsoft 策劃它們，在 Azure Machine Learning 中將模型定型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: a441d1d22f938e1d1e05aea547929fa3b315d406
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012901"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>使用自訂 Docker 映射將模型定型

在本文中，您將瞭解如何在使用 Azure Machine Learning 來定型模型時使用自訂 Docker 映射。 您將使用本文中的範例腳本，藉由建立卷積類神經網路來分類寵物影像。 

Azure Machine Learning 提供預設的 Docker 基底映射。 您也可以使用 Azure Machine Learning 環境來指定不同的基底映射，例如其中一個已維護的 [Azure Machine Learning 基礎](https://github.com/Azure/AzureML-Containers) 映射或您自己的 [自訂映射](how-to-deploy-custom-docker-image.md#create-a-custom-base-image)。 自訂基底映射可讓您在執行定型作業時，密切地管理相依性，並對元件版本保持更緊密的控制權。

## <a name="prerequisites"></a>必要條件

在下列任一環境中執行程式碼：

* Azure Machine Learning 計算實例 (不需要下載或安裝) ：
  * 完成「 [設定環境和工作區](tutorial-1st-experiment-sdk-setup.md) 」教學課程，以建立預先載入 SDK 和範例存放庫的專用筆記本伺服器。
  * 在 Azure Machine Learning [範例存放庫](https://github.com/Azure/azureml-examples)中，前往 **筆記本**  >  **fastai**  >  **resnet34. .ipynb** 目錄來尋找已完成的筆記本。 
* 您自己的 Jupyter Notebook server：
  * 建立[工作區組態檔](how-to-configure-environment.md#workspace)。
  * 安裝 [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。 
  * 建立可在網際網路上使用的 [Azure container registry](../container-registry/index.yml) 或其他 Docker 登錄。

## <a name="set-up-a-training-experiment"></a>設定定型實驗

在本節中，您會藉由初始化工作區、定義您的環境，以及設定計算目標，來設定您的訓練實驗。

### <a name="initialize-a-workspace"></a>初始化工作區

[Azure Machine Learning 工作區](concept-workspace.md)是服務的最上層資源。 它提供集中的位置來處理您建立的所有成品。 在 Python SDK 中，您可以藉由建立物件來存取工作區成品 [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) 。

`Workspace`從您建立為必要條件的檔案 config.js建立物件。 [prerequisite](#prerequisites)

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>定義您的環境

建立 `Environment` 物件並啟用 Docker。

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

下列程式碼中指定的基底映射支援 fast.ai 程式庫，可允許分散式深度學習功能。 如需詳細資訊，請參閱 [fast.ai Docker Hub 存放庫](https://hub.docker.com/u/fastdotai)。 

當您使用自訂的 Docker 映射時，您可能已經正確設定 Python 環境。 在此情況下，請將旗標設為， `user_managed_dependencies` `True` 以使用自訂映射的內建 Python 環境。 依預設，Azure Machine Learning 會使用您指定的相依性來建立 Conda 環境。 服務會在該環境中執行腳本，而不是使用您在基底映射上安裝的任何 Python 程式庫。

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>使用私人容器登錄 (選用) 

若要使用不在您工作區中的私人容器登錄中的映射，請使用 `docker.base_image_registry` 來指定存放庫的位址以及使用者名稱和密碼：

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>使用自訂 Dockerfile (選用) 

也可以使用自訂 Dockerfile。 如果您需要安裝非 Python 套件作為相依性，請使用此方法。 請記得將基底映射設定為 `None` 。

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

如需有關建立和管理 Azure Machine Learning 環境的詳細資訊，請參閱 [建立和使用軟體環境](how-to-use-environments.md)。 

### <a name="create-or-attach-a-compute-target"></a>建立或附加計算目標

您必須建立用來定型模型的 [計算目標](concept-azure-machine-learning-architecture.md#compute-targets) 。 在本教學課程中，您會建立 `AmlCompute` 為定型計算資源。

建立 `AmlCompute` 需要幾分鐘的時間。 如果 `AmlCompute` 資源已存在於您的工作區中，此程式碼會略過建立程式。

如同其他 Azure 服務，某些資源有一些限制 (例如， `AmlCompute` 與 Azure Machine Learning 服務相關聯的) 。 如需詳細資訊，請參閱 [預設限制及如何要求更高的配額](how-to-manage-quotas.md)。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>設定定型作業

在本教學課程中，請使用 [GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py)上的定型腳本 *train.py* 。 在實務上，您可以採用任何自訂定型腳本，並以 Azure Machine Learning 的方式執行它。

建立 `ScriptRunConfig` 資源以設定您的作業，以在所需的 [計算目標](how-to-set-up-training-targets.md)上執行。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>提交您的定型作業

當您使用物件提交定型回合時，方法會傳回型別為 `ScriptRunConfig` `submit` 的物件 `ScriptRun` 。 傳回的 `ScriptRun` 物件可讓您以程式設計方式存取定型執行的相關資訊。 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning 藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的機密資料，請使用 [. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 檔案，或不要將它包含在來原始目錄中。 相反地， [使用資料存放](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py)區存取您的資料。

## <a name="next-steps"></a>下一步
在本文中，您已使用自訂 Docker 映射來定型模型。 若要深入瞭解 Azure Machine Learning，請參閱下列文章：
* 訓練期間[追蹤執行計量](how-to-track-experiments.md)。
* 使用自訂 Docker 映射[部署模型](how-to-deploy-custom-docker-image.md)。
