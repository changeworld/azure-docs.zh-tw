---
title: 使用自訂 Docker 映射部署模型
titleSuffix: Azure Machine Learning
description: 瞭解如何在部署 Azure 機器學習模型時使用自訂 Docker 基本映射。 雖然 Azure 機器學習為您提供預設的基本映射，但也可以使用自己的基本映射。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: 1f11d6667c22990b3cba2079959bec6f413d5951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296940"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>使用自訂 Docker 基本映射部署模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何在使用 Azure 機器學習部署已訓練的模型時使用自訂 Docker 基本映射。

將訓練的模型部署到 Web 服務或 IoT Edge 設備時，將創建一個包，其中包含用於處理傳入請求的 Web 服務器。

Azure 機器學習提供了預設 Docker 基本映射，因此您不必擔心創建該映射。 您還可以使用 Azure 機器學習__環境__來選擇特定的基本映射，或使用提供的自訂映射。

在為部署創建映射時，基本映射用作起點。 它提供基礎作業系統和元件。 然後，部署過程在部署映射之前向映射添加其他元件，如模型、conda 環境和其他資產。

通常，當您希望使用 Docker 管理依賴項、保持對元件版本的更嚴格控制或在部署期間節省時間時，可以創建自訂基本映射。 例如，您可能希望對 Python、Conda 或其他元件的特定版本進行標準化。 您可能還需要安裝模型所需的軟體，安裝過程需要很長時間。 在創建基本映射時安裝軟體意味著您不必為每個部署安裝該軟體。

> [!IMPORTANT]
> 部署模型時，不能覆蓋核心元件（如 Web 服務器或 IoT Edge 元件）。 這些元件提供了由 Microsoft 測試和支援的已知工作環境。

> [!WARNING]
> Microsoft 可能無法説明解決由自訂映射引起的問題。 如果遇到問題，可能會要求您使用預設映射或 Microsoft 提供的映射之一，以查看問題是否特定于您的圖像。

本文檔分為兩個部分：

* 創建自訂基本映射：向管理員和 DevOps 提供有關使用 Azure CLI 和機器學習 CLI 創建自訂映射和將身份驗證配置為 Azure 容器註冊表的資訊。
* 使用自訂基本映射部署模型：向資料科學家和 DevOps / ML 工程師提供有關在從 Python SDK 或 ML CLI 部署經過訓練的模型時使用自訂映射的資訊。

## <a name="prerequisites"></a>Prerequisites

* Azure 機器學習工作組。 有關詳細資訊，請參閱[創建工作區](how-to-manage-workspace.md)一文。
* [Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Azure 機器學習的 CLI 擴展。](reference-azure-machine-learning-cli.md)
* 可在 Internet 上訪問的[Azure 容器註冊表](/azure/container-registry)或其他 Docker 註冊表。
* 本文檔中的步驟假定您熟悉創建和使用__推理配置__物件作為模型部署的一部分。 有關詳細資訊，請參閱部署[位置和 中的](how-to-deploy-and-where.md#prepare-to-deploy)"準備部署"部分。

## <a name="create-a-custom-base-image"></a>創建自訂基本映射

本節中的資訊假定您正在使用 Azure 容器註冊表來存儲 Docker 映射。 計畫為 Azure 機器學習創建自訂映射時，請使用以下檢查表：

* 是使用為 Azure 機器學習工作區創建的 Azure 容器註冊表，還是使用獨立的 Azure 容器註冊表？

    使用存儲在__工作區的容器註冊表__中的圖像時，不需要對註冊表進行身份驗證。 身份驗證由工作區處理。

    > [!WARNING]
    > 工作區的 Azure 容器註冊表是在__首次使用工作區訓練或部署模型時創建的__。 如果已創建新工作區，但未訓練或創建模型，則工作區將不存在 Azure 容器註冊表。

    有關檢索工作區的 Azure 容器註冊表的名稱的資訊，請參閱本文的["獲取容器註冊表名稱](#getname)"部分。

    使用存儲在__獨立容器註冊表__中的映射時，您需要配置至少具有讀取存取許可權的服務主體。 然後，您將服務主體 ID（使用者名）和密碼提供給使用註冊表中圖像的任何人。 例外情況是，如果使容器註冊表可公開訪問。

    有關創建專用 Azure 容器註冊表的資訊，請參閱[創建專用容器註冊表](/azure/container-registry/container-registry-get-started-azure-cli)。

    有關將服務主體與 Azure 容器註冊表一起使用的資訊，請參閱[Azure 容器註冊表身份驗證與服務主體](/azure/container-registry/container-registry-auth-service-principal)。

* Azure 容器註冊表和映射資訊：向需要使用它的任何人提供映射名稱。 例如，在名為`myimage``myregistry`的註冊表中存儲的映射被引用為`myregistry.azurecr.io/myimage`使用映射進行模型部署時

* 映射要求：Azure 機器學習僅支援提供以下軟體的 Docker 映射：

    * 烏本圖 16.04 或更高。
    * 康達 4.5.* 或更高。
    * Python 3.5.* 或 3.6.*

<a id="getname"></a>

### <a name="get-container-registry-information"></a>獲取容器註冊表資訊

在本節中，瞭解如何獲取 Azure 機器學習工作區的 Azure 容器註冊表的名稱。

> [!WARNING]
> 工作區的 Azure 容器註冊表是在__首次使用工作區訓練或部署模型時創建的__。 如果已創建新工作區，但未訓練或創建模型，則工作區將不存在 Azure 容器註冊表。

如果您已經使用 Azure 機器學習訓練或部署模型，則為工作區創建了容器註冊表。 要查找此容器註冊表的名稱，請使用以下步驟：

1. 打開新的 shell 或命令提示符，並使用以下命令對 Azure 訂閱進行身份驗證：

    ```azurecli-interactive
    az login
    ```

    按照提示對訂閱進行身份驗證。

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

2. 使用以下命令列出工作區的容器註冊表。 替換為`<myworkspace>`Azure 機器學習工作區名稱。 替換為`<resourcegroup>`包含工作區的 Azure 資源組：

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    所傳回的資訊類似下列文字︰

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    該`<registry_name>`值是工作區的 Azure 容器註冊表的名稱。

### <a name="build-a-custom-base-image"></a>構建自訂基本映射

本節中的步驟演練了在 Azure 容器註冊表中創建自訂 Docker 映射。

1. 創建名為`Dockerfile`的新文字檔，並使用以下文本作為內容：

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. 從 shell 或命令提示符中，使用以下內容對 Azure 容器註冊表進行身份驗證。 `<registry_name>`將 替換為要將映射存儲在以下中的容器註冊表的名稱：

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. 要上載 Dockerfile 並生成它，請使用以下命令。 替換為`<registry_name>`要將映射存儲在以下中的容器註冊表的名稱：

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > 在此示例中，`:v1`將 標記應用於圖像。 如果未提供標記，則應用 標記`:latest`。

    在生成過程中，資訊將資料流回命令列。 如果生成成功，您將收到類似于以下文本的消息：

    ```text
    Run ID: cda was successful after 2m56s
    ```

有關使用 Azure 容器註冊表生成映射的詳細資訊，請參閱[使用 Azure 容器註冊表任務生成和運行容器映射](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

有關將現有映射上載到 Azure 容器註冊表的詳細資訊，請參閱[將第一個映射推送到專用 Docker 容器註冊表](/azure/container-registry/container-registry-get-started-docker-cli)。

## <a name="use-a-custom-base-image"></a>使用自訂基本映射

要使用自訂映射，您需要以下資訊：

* __圖像名稱__。 例如，`mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`是 Microsoft 提供的基本 Docker 映射的路徑。

    > [!IMPORTANT]
    > 對於已創建的自訂圖像，請確保包含與圖像一起使用的任何標記。 例如，如果圖像是使用特定標記（如`:v1`） 創建的。 如果在創建圖像時未使用特定標記，`:latest`則應用了

* 如果映射位於__專用存儲庫__中，則需要以下資訊：

    * 註冊表__位址__。 例如： `myregistry.azureecr.io` 。
    * 具有對註冊表的讀取__password__存取權限的服務主體__使用者名和密碼__。

    如果沒有此資訊，請與包含映射的 Azure 容器註冊表的管理員聯繫。

### <a name="publicly-available-base-images"></a>公開提供的基本映射

Microsoft 在可公開訪問的存儲庫上提供了多個 Docker 映射，可用於本節中的步驟：

| 映像 | 描述 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure 機器學習的基本映射 |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | 包含用於 CPU 推斷的 ONNX 運行時 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | 包含用於 GPU 的 ONNX 運行時和 CUDA |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | 包含用於 GPU 的 ONNX 運行時和 TensorRT |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | 包含基於 Movidius<sup>TM</sup> MyriadX<sup> </sup> VUS 的適用于英特爾視覺加速器設計的 ONNX 運行時和 OpenVINO |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | 包含適用于英特爾<sup></sup>莫迪迪斯<sup>TM</sup> USB 棒的 ONNX 運行時和 OpenVINO |

有關 ONNX 運行時基本映射的詳細資訊，請參閱 GitHub 存儲庫中的[ONNX 運行時 Dockerfile 部分](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md)。

> [!TIP]
> 由於這些圖像是公開的，因此在使用它們時無需提供位址、使用者名或密碼。

有關詳細資訊，請參閱[Azure 機器學習容器](https://github.com/Azure/AzureML-Containers)。

> [!TIP]
>__如果您的模型在 Azure 機器學習計算上訓練__，請使用 Azure 機器學習 SDK__的版本 1.0.22 或更高__版本，則在培訓期間將創建映射。 要發現此圖像的名稱，請使用`run.properties["AzureML.DerivedImageName"]`。 以下示例演示如何使用此圖像：
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>將映射與 Azure 機器學習 SDK 一起使用

要使用存儲在工作區的**Azure 容器註冊表**中的圖像，或**可公開訪問的容器註冊表，** 請設置以下[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)屬性：

+ `docker.enabled=True`
+ `docker.base_image`：設置為註冊表和映射的路徑。

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

要使用工作區中不位於__的專用容器註冊表__中的映射，必須使用 來`docker.base_image_registry`指定存儲庫的位址和使用者名和密碼：

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

您必須將版本>= 1.0.45 的 azureml 預設值添加為點依賴項。 此套件包含將模型裝載為 Web 服務所需的功能。 還必須將環境中inferencing_stack_version屬性設置為"最新"，這將安裝 Web 服務所需的特定貼切包。 

定義環境後，將其與["推理Config"](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)物件一起定義模型和 Web 服務將在其中運行的推理環境。

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

此時，您可以繼續部署。 例如，以下程式碼片段將使用推理配置和自訂映射在本地部署 Web 服務：

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

有關部署的詳細資訊，請參閱使用[Azure 機器學習部署模型](how-to-deploy-and-where.md)。

有關自訂 Python 環境的詳細資訊，請參閱[創建和管理用於培訓和部署的環境](how-to-use-environments.md)。 

### <a name="use-an-image-with-the-machine-learning-cli"></a>將圖像與機器學習 CLI 一起使用

> [!IMPORTANT]
> 目前，機器學習 CLI 可以使用 Azure 容器註冊表中的圖像用於工作區或可公開訪問的存儲庫。 它不能使用來自獨立私有註冊表的圖像。

在使用機器學習 CLI 部署模型之前，請創建使用自訂映射[的環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。 然後創建引用環境的推理設定檔。 您還可以直接在推理設定檔中定義環境。 以下 JSON 文檔演示如何在公共容器註冊表中引用映射。 在此示例中，環境是內聯定義的：

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
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

此檔與`az ml model deploy`命令一起使用。 參數`--ic`用於指定推理設定檔。

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

有關使用 ML CLI 部署模型的詳細資訊，請參閱 Azure 機器學習文章[CLI 擴展](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)的"模型註冊、分析和部署"部分。

## <a name="next-steps"></a>後續步驟

* 詳細瞭解[部署地點以及如何](how-to-deploy-and-where.md)部署。
* 瞭解如何使用[Azure 管道訓練和部署機器學習模型](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)。
