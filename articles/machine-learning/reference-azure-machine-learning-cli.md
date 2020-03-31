---
title: CLI 擴展
titleSuffix: Azure Machine Learning
description: 了解 Azure CLI 的 Azure Machine Learning CLI 擴充功能。 Azure CLI 是一個跨平台命令列公用工具，可讓您使用 Azure 雲端的資源。 通過機器學習擴展，您可以使用 Azure 機器學習。 ML CLI 創建和管理資源，如工作區、資料存儲、資料集、管道、模型和部署。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 471b26ebc4bd4aecb814ec43c7eba56e3d764fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402487"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>將 CLI 擴展用於 Azure 機器學習
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 的擴充功能，而 Azure CLI 是 Azure 平台的跨平台命令列介面。 此擴展提供用於使用 Azure 機器學習的命令。 它允許您自動執行機器學習活動。 下面的清單提供了一些可以使用 CLI 擴展執行的操作示例操作：

+ 執行實驗來建立機器學習服務模型

+ 註冊供客戶使用的機器學習服務模型

+ 封裝、部署和追蹤機器學習模型的生命週期

CLI 不是 Azure Machine Learning SDK 的取代項目。 它是一種互補工具，經過優化，可處理適合自動化的高度參數化任務。

## <a name="prerequisites"></a>Prerequisites

* 若要使用 CLI，您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="full-reference-docs"></a>完整參考文檔

查找[Azure CLI 的 azure-cli-ml 擴展的完整參考文檔](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)。

## <a name="install-the-extension"></a>安裝延伸模組

若要安裝 Machine Learning CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 與下面的命令一起使用的示例檔[可以在這裡](https://aka.ms/azml-deploy-cloud)找到。

出現提示時，請選取 `y` 來安裝擴充功能。

若要確認已安裝擴充功能，請使用下列命令來顯示 ML 特有的子命令清單：

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>更新擴充功能

要更新機器學習 CLI 擴展，請使用以下命令：

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>移除擴充功能

若要移除 CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>資源管理

下列命令示範如何使用 CLI 來管理 Azure Machine Learning 所使用的資源。

+ 如果還沒有資源組，請創建一個資源組：

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ 建立 Azure Machine Learning 工作區：

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > 此命令創建基本版本工作區。 要創建企業工作區，請使用 該`--sku enterprise`命令的`az ml workspace create`開關。 有關 Azure 機器學習版本的詳細資訊，請參閱什麼是[Azure 機器學習](overview-what-is-azure-ml.md#sku)。

    有關詳細資訊，請參閱[az ml 工作區創建](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)。

+ 將工作區配置附加到資料夾，以啟用 CLI 上下文感知。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令創建一`.azureml`個子目錄，其中包含示例 runconfig 和 conda 環境檔。 它還包含用於與`config.json`Azure 機器學習工作區通信的檔。

    有關詳細資訊，請參閱[az ml 資料夾附加](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

+ 將 Azure Blob 容器附加到資料存儲。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    有關詳細資訊，請參閱[az ml 資料存儲附加 blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)。

+ 將檔上載到資料存儲。

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    有關詳細資訊，請參閱[az ml 資料存儲上載](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload)。

+ 將 AKS 群集附加到計算目標。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    有關詳細資訊，請參閱[az ml 計算目標附加 aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ 創建新的 AML 計算目標。

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    有關詳細資訊，請參閱[az ml 計算目標創建 aml 計算](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)。

## <a name="run-experiments"></a><a id="experiments"></a>運行實驗

* 開始您的實驗回合。 使用此命令時，請針對 -c 參數指定 runconfig 檔\*的名稱（如果查看檔案系統，則 .runconfig 之前的文本）。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > 該`az ml folder attach`命令創建一`.azureml`個子目錄，其中包含兩個示例 runconfig 檔。 
    >
    > 如果您有一個以程式設計方式創建回合組態物件的 Python 腳本，則可以使用[RunConfig.save（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)將其另存為 runconfig 檔。
    >
    > 完整的回合組態架構可以在此[JSON 檔中](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)找到。 架構通過每個物件的`description`鍵進行自我記錄。 此外，還有可能值的枚舉，以及末尾的範本程式碼片段。

    有關詳細資訊，請參閱[az ml 運行提交腳本](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

* 查看實驗清單：

    ```azurecli-interactive
    az ml experiment list
    ```

    有關詳細資訊，請參閱[az ml 實驗清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

## <a name="dataset-management"></a>資料集管理

以下命令演示如何在 Azure 機器學習中處理資料集：

+ 註冊資料集：

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    有關用於定義資料集的 JSON 檔的格式的資訊，請使用`az ml dataset register --show-template`。

    有關詳細資訊，請參閱[az ml 資料集寄存器](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 存檔活動或棄用資料集：

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    有關詳細資訊，請參閱[az ml 資料集存檔](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 棄用資料集：

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    有關詳細資訊，請參閱[az ml 資料集棄用](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 列出工作區中的所有資料集：

    ```azurecli-interactive
    az ml dataset list
    ```

    有關詳細資訊，請參閱[az ml 資料集清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 獲取資料集的詳細資訊：

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    有關詳細資訊，請參閱[az ml 資料集顯示](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 重新啟動已存檔或已棄用的資料集：

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    有關詳細資訊，請參閱[重新啟動 az ml 資料集](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 取消註冊資料集：

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    有關詳細資訊，請參閱[az ml 資料集取消註冊](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

## <a name="environment-management"></a>環境管理

以下命令演示如何為工作區創建、註冊和列出 Azure 機器學習[環境](how-to-configure-environment.md)：

+ 為環境創建基架檔：

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    有關詳細資訊，請參閱[az ml 環境基架](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)。

+ 註冊環境：

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    有關詳細資訊，請參閱[az ml 環境寄存器](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)。

+ 列出已註冊的環境：

    ```azurecli-interactive
    az ml environment list
    ```

    有關詳細資訊，請參閱[az ml 環境清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)。

+ 下載註冊環境：

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    有關詳細資訊，請參閱[az ml 環境下載](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)。

### <a name="environment-configuration-schema"></a>環境配置架構

如果使用 該`az ml environment scaffold`命令，它將生成一個範本`azureml_environment.json`檔，該檔可以修改並用於使用 CLI 創建自訂環境配置。 頂級物件鬆散地映射到 Python SDK[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)中的類。 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

下表詳細介紹了 JSON 檔中的每個頂級欄位、類型和說明。 如果物件類型連結到 Python SDK 中的類，則每個 JSON 欄位與 Python 類中的公開變數名稱之間有一個鬆散的 1：1 匹配。 在某些情況下，該欄位可能映射到建構函式參數，而不是類變數。 例如，`environmentVariables`欄位映射到類中的`environment_variables`[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)變數。

| JSON 欄位 | 類型 | 描述 |
|---|---|---|
| `name` | `string` | 環境的名稱。 不要使用**微軟**或**AzureML**開始名稱。 |
| `version` | `string` | 環境的版本。 |
| `environmentVariables` | `{string: string}` | 環境變數名稱和值的雜湊映射。 |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | 定義要在目標計算資源上使用的 Python 環境和解譯器的物件。 |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | 定義設置以自訂根據環境規範構建的 Docker 映射。 |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | 該部分配置 Spark 設置。 僅當框架設置為 PySpark 時，才使用它。 |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | 配置資料磚庫依賴項。 |
| `inferencingStackVersion` | `string` | 指定添加到映射的推斷堆疊版本。 為了避免添加推斷堆疊，請保留此欄位`null`。 有效值："最新"。 |

## <a name="ml-pipeline-management"></a>ML 管道管理

以下命令演示如何使用機器學習管道：

+ 創建機器學習管道：

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    有關詳細資訊，請參閱[az ml 管道創建](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create)。

    有關管道 YAML 檔的詳細資訊，請參閱[在 YAML 中定義機器學習管道](reference-pipeline-yaml.md)。

+ 運行管道：

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    有關詳細資訊，請參閱[az ml 運行提交管道](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline)。

    有關管道 YAML 檔的詳細資訊，請參閱[在 YAML 中定義機器學習管道](reference-pipeline-yaml.md)。

+ 計畫管道：

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    有關詳細資訊，請參閱[az ml 管道創建計畫](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)。

    有關管道計畫 YAML 檔的詳細資訊，請參閱[在 YAML 中定義機器學習管道](reference-pipeline-yaml.md#schedules)。

## <a name="model-registration-profiling-deployment"></a>模型註冊、分析、部署

下列命令示範如何註冊定型的模型，然後將它部署為生產服務：

+ 向 Azure Machine Learning 註冊模型：

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    有關詳細資訊，請參閱[az ml 模型寄存器](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)。

+ **可選**分析模型，以獲得用於部署的最佳 CPU 和記憶體值。
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    有關詳細資訊，請參閱[az ml 模型設定檔](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)。

+ 將模型部署到 AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    有關推理設定檔架構的詳細資訊，請參閱[推理配置架構](#inferenceconfig)。
    
    有關部署設定檔架構的詳細資訊，請參閱[部署配置架構](#deploymentconfig)。

    有關詳細資訊，請參閱[az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)。

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>推理配置架構

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>部署配置架構

### <a name="local-deployment-configuration-schema"></a>本地部署配置架構

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure 容器實例部署配置架構 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure 庫伯內斯服務部署配置架構

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>後續步驟

* [機器學習 CLI 擴展的命令引用](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)。

* [使用 Azure 管道訓練和部署機器學習模型](/azure/devops/pipelines/targets/azure-machine-learning)
