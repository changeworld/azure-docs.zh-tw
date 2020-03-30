---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159401"
---
文檔中的`inferenceconfig.json`條目映射到[推理配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類的參數。 下表描述了 JSON 文檔中的實體與方法的參數之間的映射：

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 包含要為映射運行的代碼的本地檔的路徑。 |
| `sourceDirectory` | `source_directory` | 選擇性。 包含創建映射的所有檔的資料夾的路徑，從而輕鬆訪問此資料夾或子資料夾中的任何檔。 可以從本地電腦上載整個資料夾作為 Web 服務的依賴項。 注意：entry_script、conda_file和extra_docker_file_steps路徑是source_directory路徑的相對路徑。 |
| `environment` | `environment` | 選擇性。  Azure 機器學習[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。|

您可以在推理設定檔中包括 Azure 機器學習[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)的完整規範。 如果工作區中不存在此環境，Azure 機器學習將創建它。 否則，Azure 機器學習將在必要時更新環境。 以下 JSON 就是一個示例：

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

還可以在分離的 CLI 參數中使用現有的 Azure 機器學習[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)，並從推理設定檔中刪除"環境"金鑰。 將 -e 用於環境名稱，對於環境版本使用 -ev。 如果不指定 --ev，將使用最新版本。 下面是推理設定檔的示例：

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

以下命令演示如何使用以前的推理設定檔（名為 myinferenceConfig.json）部署模型。 

它還使用現有 Azure 機器學習[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)的最新版本（稱為 AzureML-最小）。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
