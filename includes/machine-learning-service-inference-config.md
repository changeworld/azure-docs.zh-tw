---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159401"
---
檔中的專案會 `inferenceconfig.json` 對應至[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)類別的參數。 下表描述 JSON 檔中的實體與方法的參數之間的對應：

| JSON 實體 | 方法參數 | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 本機檔案的路徑，其中包含要為映射執行的程式碼。 |
| `sourceDirectory` | `source_directory` | 選擇性。 資料夾的路徑，其中包含要建立映射的所有檔案，讓您可以輕鬆地存取此資料夾或子資料夾中的任何檔案。 您可以從本機電腦上傳整個資料夾，做為 Webservice 的相依性。 注意：您的 entry_script、conda_file 和 extra_docker_file_steps 路徑是 source_directory 路徑的相對路徑。 |
| `environment` | `environment` | 選擇性。  Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。|

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

下列命令示範如何使用先前的推斷設定檔（在中命名為 myInferenceConfig.js）來部署模型。 

它也會使用現有 Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)的最新版本（名為 AzureML-最小）。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
