---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845168"
---
`inferenceconfig.json` 檔中的專案會對應至[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別的參數。 下表描述 JSON 檔中的實體與方法的參數之間的對應：

| JSON 實體 | 方法參數 | 說明 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 本機檔案的路徑，其中包含要為映射執行的程式碼。 |
| `runtime` | `runtime` | 選擇性。 要用於影像的執行時間。 支援的執行時間 `spark-py` 和 `python`。 如果已設定 `environment`，則會忽略此專案。 |
| `condaFile` | `conda_file` | 選擇性。 本機檔案的路徑，其中包含要用於影像的 Conda 環境定義。  如果已設定 `environment`，則會忽略此專案。 |
| `extraDockerFileSteps` | `extra_docker_file_steps` | 選擇性。 本機檔案的路徑，其中包含設定映射時要執行的其他 Docker 步驟。  如果已設定 `environment`，則會忽略此專案。|
| `sourceDirectory` | `source_directory` | 選擇性。 資料夾的路徑，其中包含要建立映射的所有檔案，讓您可以輕鬆地存取此資料夾或子資料夾中的任何檔案。 您可以從本機電腦上傳整個資料夾，做為 Webservice 的相依性。 注意：您的 entry_script、conda_file 和 extra_docker_file_steps 路徑是 source_directory 路徑的相對路徑。 |
| `enableGpu` | `enable_gpu` | 選擇性。 是否要在映射中啟用 GPU 支援。 GPU 映射必須在 Azure 服務（例如 Azure 容器實例）上使用。 例如，Azure Machine Learning 計算、Azure 虛擬機器和 Azure Kubernetes Service。 預設值是 False。 如果已設定 `environment`，則會忽略此專案。|
| `baseImage` | `base_image` | 選擇性。 要當做基底映射使用的自訂映射。 如果未提供基底映射，則映射會以提供的執行時間參數為基礎。 如果已設定 `environment`，則會忽略此專案。 |
| `baseImageRegistry` | `base_image_registry` | 選擇性。 包含基底映射的映射登錄。 如果已設定 `environment`，則會忽略此專案。|
| `cudaVersion` | `cuda_version` | 選擇性。 要為需要 GPU 支援的映射安裝的 CUDA 版本。 GPU 映射必須用於 Azure 服務。 例如，Azure 容器實例、Azure Machine Learning 計算、Azure 虛擬機器和 Azure Kubernetes Service。 支援的版本為9.0、9.1 和10.0。 如果已設定 `enable_gpu`，預設值為9.1。 如果已設定 `environment`，則會忽略此專案。 |
| `description` | `description` | 影像的描述。 如果已設定 `environment`，則會忽略此專案。  |
| `environment` | `environment` | 選擇性。  Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。|

下列 JSON 是與 CLI 搭配使用的範例推斷設定：

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

您可以在推斷設定檔中包含 Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)的完整規格。 如果此環境不存在於您的工作區中，Azure Machine Learning 會加以建立。 否則，Azure Machine Learning 將會視需要更新環境。 下列 JSON 是一個範例：

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
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

您也可以在不同的 CLI 參數中使用現有的 Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)，並從推斷設定檔案移除「環境」金鑰。 針對環境名稱使用-e，並針對環境版本使用--ev。 如果您未指定--ev，則會使用最新版本。 以下是推斷設定檔的範例：

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

下列命令示範如何使用先前的推斷設定檔（名為 myInferenceConfig）來部署模型。 

它也會使用現有 Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)的最新版本（名為 AzureML-最小）。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
