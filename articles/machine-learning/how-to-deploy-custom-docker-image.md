---
title: 使用自訂 Docker 映射部署模型
titleSuffix: Azure Machine Learning
description: 瞭解如何在部署 Azure Machine Learning 模型時使用自訂的 Docker 基底映射。 雖然 Azure Machine Learning 為您提供預設的基底映射，您也可以使用自己的基底映射。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 6aa08f91a9289984d15beac5fb215d112a5558da
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676037"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>使用自訂的 Docker 基底映射部署模型

瞭解如何在使用 Azure Machine Learning 部署定型的模型時，使用自訂的 Docker 基底映射。

如果未指定任何值，Azure Machine Learning 將會使用預設的基底 Docker 映射。 您可以找到與搭配使用的特定 Docker 映射 `azureml.core.runconfig.DEFAULT_CPU_IMAGE` 。 您也可以使用 Azure Machine Learning __環境__ 來選取特定的基底映射，或使用您提供的自訂映射。

當針對部署建立映射時，會使用基底映射作為起點。 它提供基礎作業系統和元件。 然後，部署程式會將其他元件（例如您的模型、conda 環境和其他資產）新增至映射。

一般來說，當您想要使用 Docker 來管理相依性時，您會建立自訂基底映射、維持對元件版本的更緊密控制權，或在部署期間節省時間。 您也可能想要安裝模型所需的軟體，安裝程式需要很長的時間。 在建立基底映射時安裝軟體，表示您不需要針對每個部署進行安裝。

> [!IMPORTANT]
> 當您部署模型時，無法覆寫核心元件，例如 web 伺服器或 IoT Edge 元件。 這些元件提供已知的工作環境，並受到 Microsoft 的測試和支援。

> [!WARNING]
> Microsoft 可能無法協助針對自訂映射所造成的問題進行疑難排解。 如果您遇到問題，系統可能會要求您使用預設影像或 Microsoft 提供的其中一個影像，以查看問題是否為您的映射所特有。

本檔分為兩個部分：

* 建立自訂基底映射：提供資訊給系統管理員和 DevOps，以建立自訂映射，以及使用 Azure CLI 和 Machine Learning CLI 來設定對 Azure Container Registry 的驗證。
* 使用自訂基底映射部署模型：從 Python SDK 或 ML CLI 部署定型的模型時，提供資訊給資料科學家和 DevOps/ML 工程師使用自訂映射。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立工作區](how-to-manage-workspace.md) 文章。
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)。
* [適用於 Azure Machine Learning 的 CLI 擴充功能](reference-azure-machine-learning-cli.md)。
* [Azure Container Registry](/azure/container-registry)或可在網際網路上存取的其他 Docker 登錄。
* 本檔中的步驟假設您已熟悉建立和使用 __推斷__ 設定物件作為模型部署的一部分。 如需詳細資訊，請參閱 [部署位置和方式](how-to-deploy-and-where.md)。

## <a name="create-a-custom-base-image"></a>建立自訂基底映射

本節中的資訊假設您使用 Azure Container Registry 來儲存 Docker 映射。 規劃建立 Azure Machine Learning 的自訂映射時，請使用下列檢查清單：

* 您是否會使用針對 Azure Machine Learning 工作區或獨立 Azure Container Registry 所建立的 Azure Container Registry？

    使用儲存在容器登錄中 __的映射做為工作區__ 時，您不需要向登錄進行驗證。 驗證是由工作區處理。

    > [!WARNING]
    > 當 __您第一次使用工作區定型或部署模型__ 時，會建立工作區的 Azure Container Registry。 如果您已建立新的工作區，但未定型或建立模型，工作區將不會有 Azure Container Registry。

    使用儲存在 __獨立容器__ 登錄中的映射時，您必須設定至少具有讀取存取權的服務主體。 然後，您可以將服務主體識別碼 (使用者名稱) 和密碼提供給使用登錄中映射的任何人。 例外狀況是您讓容器登錄可公開存取。

    如需建立私人 Azure Container Registry 的詳細資訊，請參閱 [建立私用容器](/azure/container-registry/container-registry-get-started-azure-cli)登錄。

    如需搭配 Azure Container Registry 使用服務主體的詳細資訊，請參閱 [Azure Container Registry 驗證與服務主體](/azure/container-registry/container-registry-auth-service-principal)。

* Azure Container Registry 和影像資訊：提供映射名稱給任何需要使用它的人。 例如，在 `myimage` `myregistry` `myregistry.azurecr.io/myimage` 使用模型部署的映射時，會參考名為的映射，並將其儲存在名為的登錄中。

### <a name="image-requirements"></a>影像需求

Azure Machine Learning 僅支援提供下列軟體的 Docker 映射：
* Ubuntu 16.04 或更新版本。
* Conda 4.5. # 或更高的版本。
* Python 3.5 +。

若要使用資料集，請安裝 libfuse 開發套件。 也請務必安裝您可能需要的任何使用者空間套件。

Azure ML 會維護一組已發佈至 Microsoft Container Registry 的 CPU 和 GPU 基礎映射，您可以選擇性地利用 (或參考) ，而不是建立您自己的自訂映射。 若要查看這些映射的 Dockerfile，請參閱 [Azure/AzureML-容器 GitHub 存放](https://github.com/Azure/AzureML-Containers) 庫。

針對 GPU 映射，Azure ML 目前提供 cuda9 和 cuda10 基底映射。 這些基底映射中所安裝的主要相依性如下：

| 相依性 | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 |
| Mpi | intelmpi = = 2018.3.222 |openmpi = = 3.1。2 |intelmpi = = 2018.3.222| openmpi = = 3.1。2 |
| cuda | - | - | 9.0/10。0 | 9.0/10.0/10。1 |
| cudnn | - | - | 7.4/7。5 | 7.4/7。5 |
| nccl | - | - | 2.4 | 2.4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

CPU 映射是從 ubuntu 16.04 所建立。 Cuda9 的 GPU 映射是從 nvidia/cuda： 9.0-cudnn7->unixodbc-devel-ubuntu 16.04 所建立。 Cuda10 的 GPU 映射是從 nvidia/cuda： 10.0-cudnn7->unixodbc-devel-ubuntu 16.04 所建立。
<a id="getname"></a>

### <a name="get-container-registry-information"></a>取得容器登錄資訊

在本節中，您將瞭解如何取得 Azure Machine Learning 工作區的 Azure Container Registry 名稱。

> [!WARNING]
> 當 __您第一次使用工作區定型或部署模型__ 時，會建立工作區的 Azure Container Registry。 如果您已建立新的工作區，但未定型或建立模型，工作區將不會有 Azure Container Registry。

如果您已經使用 Azure Machine Learning 定型或部署模型，則會為您的工作區建立容器登錄。 若要尋找此容器登錄的名稱，請使用下列步驟：

1. 開啟新的 shell 或命令提示字元，並使用下列命令來驗證您的 Azure 訂用帳戶：

    ```azurecli-interactive
    az login
    ```

    遵循提示來對訂用帳戶進行驗證。

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. 使用下列命令來列出工作區的容器登錄。 `<myworkspace>`將取代為您的 Azure Machine Learning 工作區名稱。 取代 `<resourcegroup>` 為包含您工作區的 Azure 資源群組：

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    所傳回的資訊類似下列文字︰

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>`值是您工作區的 Azure Container Registry 名稱。

### <a name="build-a-custom-base-image"></a>建立自訂基底映射

本節中的步驟逐步解說如何在您的 Azure Container Registry 中建立自訂 Docker 映射。 如需範例 dockerfile，請參閱 [Azure/AzureML-容器 GitHub 存放](https://github.com/Azure/AzureML-Containers) 庫) 。

1. 建立名為的新文字檔 `Dockerfile` ，並使用下列文字做為內容：

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. 從 shell 或命令提示字元，使用下列命令來驗證 Azure Container Registry。 將取代為 `<registry_name>` 您要用來儲存映射的容器登錄名稱：

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. 若要上傳 Dockerfile 並建立它，請使用下列命令。 以 `<registry_name>` 您要用來儲存映射的容器登錄名稱取代：

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > 在此範例中，會將的標記套用 `:v1` 至映射。 如果未提供標記，就會套用的標記 `:latest` 。

    在建立過程中，會將資訊串流處理回命令列。 如果組建成功，您會收到類似下列文字的訊息：

    ```text
    Run ID: cda was successful after 2m56s
    ```

如需使用 Azure Container Registry 建立映射的詳細資訊，請參閱 [使用 Azure Container Registry 工作建立和執行容器映射](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

如需將現有映射上傳至 Azure Container Registry 的詳細資訊，請參閱將 [您的第一個映射推送至私人 Docker 容器](/azure/container-registry/container-registry-get-started-docker-cli)登錄。

## <a name="use-a-custom-base-image"></a>使用自訂基底映射

若要使用自訂映射，您需要下列資訊：

* __映射名稱__ 。 例如， `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` 是 Microsoft 所提供之簡單 Docker 映射的路徑。

    > [!IMPORTANT]
    > 針對您已建立的自訂映射，請務必包含任何與該映射搭配使用的標記。 例如，如果您的映射是使用特定標記建立的，例如 `:v1` 。 如果您在建立映射時未使用特定標記，就會套用的標記 `:latest` 。

* 如果映射位於 __私人存放庫__ 中，您需要下列資訊：

    * 登錄 __位址__ 。 例如： `myregistry.azureecr.io` 。
    * 具有登錄讀取存取權的服務主體使用者 __名稱__ 和 __密碼__ 。

    如果您沒有這種資訊，請向系統管理員說出包含您映射的 Azure Container Registry。

### <a name="publicly-available-base-images"></a>公開可用的基底映射

Microsoft 在可公開存取的存放庫上提供數個 docker 映射，可與本節中的步驟搭配使用：

| 映像 | 描述 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning 的核心映射 |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | 包含 CPU 推斷的 ONNX 執行時間 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | 包含適用于 GPU 的 ONNX 執行時間和 CUDA |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | 包含適用于 GPU 的 ONNX 執行時間和 TensorRT |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | 包含以 <sup></sup> Movidius<sup>TM</sup> MyriadX VPUs 為基礎的 Intel 視覺加速器設計 ONNX 執行時間和 OpenVINO |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | 包含 Intel <sup></sup> Movidius<sup>TM</sup> USB 杆的 ONNX Runtime 和 OpenVINO |

如需 ONNX 執行時間基底映射的詳細資訊，請參閱 GitHub 存放庫中的 [ONNX runtime dockerfile 一節](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) 。

> [!TIP]
> 因為這些映射可公開使用，所以在使用時，您不需要提供位址、使用者名稱或密碼。

如需詳細資訊，請參閱 GitHub 上的 [Azure Machine Learning 容器存放](https://github.com/Azure/AzureML-Containers) 庫。

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>使用映射搭配 Azure Machine Learning SDK

若要使用儲存在 **您工作區的 Azure Container Registry** 中的映射，或可 **公開存取的容器** 登錄，請設定下列 [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) 屬性：

+ `docker.enabled=True`
+ `docker.base_image`：設定為映射的登錄和路徑。

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

若要使用不在工作區中的 __私人容器__ 登錄中的映射，您必須使用 `docker.base_image_registry` 指定存放庫的位址以及使用者名稱和密碼：

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

您必須將 >= 1.0.45 版 azureml-defaults 版的 azureml 預設值新增為 pip 相依性。 此套件包含將模型裝載為 Web 服務所需的功能。 您也必須在環境上將 inferencing_stack_version 屬性設定為「最新」，這將會安裝 web 服務所需的特定 apt 套件。 

定義環境之後，請將它與 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true) 物件搭配使用，以定義將在其中執行模型和 web 服務的推斷環境。

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

此時，您可以繼續進行部署。 例如，下列程式碼片段會使用推斷設定和自訂映射在本機部署 web 服務：

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

如需部署的詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

如需自訂 Python 環境的詳細資訊，請參閱[建立和管理用於訓練和部署的環境](how-to-use-environments.md)。 

### <a name="use-an-image-with-the-machine-learning-cli"></a>使用 Machine Learning CLI 的映射

> [!IMPORTANT]
> 目前 Machine Learning CLI 可以針對您的工作區或可公開存取的儲存機制，使用 Azure Container Registry 的映射。 它無法使用獨立私用登錄中的映射。

使用 Machine Learning CLI 部署模型之前，請先建立使用自訂映射的 [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) 。 然後建立參考環境的推斷設定檔。 您也可以直接在推斷設定檔中定義環境。 下列 JSON 檔示範如何參考公用容器登錄中的映射。 在此範例中，環境是以內嵌方式定義的：

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

此檔案可搭配命令使用 `az ml model deploy` 。 `--ic`參數是用來指定推斷設定檔案。

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

如需使用 ML CLI 部署模型的詳細資訊，請參閱 Azure Machine Learning 文章的 [CLI 擴充](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) 功能的「模型註冊、分析和部署」一節。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [部署的位置和方式](how-to-deploy-and-where.md)。
* 瞭解如何 [使用 Azure Pipelines 來定型及部署機器學習模型](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops&preserve-view=true)。
