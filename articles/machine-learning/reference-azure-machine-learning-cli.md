---
title: CLI 擴充功能
titleSuffix: Azure Machine Learning
description: 了解 Azure CLI 的 Azure Machine Learning CLI 擴充功能。 Azure CLI 是一個跨平台命令列公用工具，可讓您使用 Azure 雲端的資源。 Machine Learning 延伸模組可讓您使用 Azure Machine Learning。 ML CLI 會建立和管理資源，例如您的工作區、資料存放區、資料集、管線、模型和部署。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 11/05/2019
ms.custom: seodec18
ms.openlocfilehash: fe9cee44970da94c346caffc0d5fe76ffebf4a0b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845145"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>使用適用于 Azure Machine Learning 的 CLI 擴充功能
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 的擴充功能，而 Azure CLI 是 Azure 平台的跨平台命令列介面。 此延伸模組提供用來處理 Azure Machine Learning 的命令。 它可讓您將機器學習活動自動化。 下列清單提供您可以使用 CLI 擴充功能執行的一些範例動作：

+ 執行實驗來建立機器學習服務模型

+ 註冊供客戶使用的機器學習服務模型

+ 封裝、部署和追蹤機器學習模型的生命週期

CLI 不是 Azure Machine Learning SDK 的取代項目。 它是一種互補的工具，已優化以處理高度參數化的工作，這些工作非常適合自動化。

## <a name="prerequisites"></a>必要條件

* 若要使用 CLI，您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="full-reference-docs"></a>完整參考檔

尋找[適用于 Azure CLI 的 azure cli-ml 擴充功能的完整參考](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)檔。

## <a name="install-the-extension"></a>安裝延伸模組

若要安裝 Machine Learning CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 可以在[這裡](https://aka.ms/azml-deploy-cloud)找到您可以搭配下列命令使用的範例檔案。

出現提示時，請選取 `y` 來安裝擴充功能。

若要確認已安裝擴充功能，請使用下列命令來顯示 ML 特有的子命令清單：

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>更新擴充功能

若要更新 Machine Learning CLI 擴充功能，請使用下列命令：

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

+ 如果您還沒有帳戶，請建立一個資源群組：

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ 建立 Azure Machine Learning 工作區：

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > 此命令會建立基本版本工作區。 若要建立企業工作區，請使用 `--sku enterprise` 參數搭配 `az ml workspace create` 命令。 如需 Azure Machine Learning 版本的詳細資訊，請參閱[什麼是 Azure Machine Learning](overview-what-is-azure-ml.md#sku)。

    如需詳細資訊，請參閱[az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)。

+ 將工作區設定附加至資料夾，以啟用 CLI 內容感知。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令會建立一個 `.azureml` 子目錄，其中包含範例 runconfig 和 conda 環境檔案。 它也包含用來與您的 Azure Machine Learning 工作區通訊的 `config.json` 檔案。

    如需詳細資訊，請參閱[az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

+ 將 Azure blob 容器附加為數據存放區。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    如需詳細資訊，請參閱[az ml 資料存放區附加-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)。

+ 將檔案上傳到資料存放區。

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    如需詳細資訊，請參閱[az ml 資料存放區上傳](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload)。

+ 將 AKS 叢集連結為計算目標。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    如需詳細資訊，請參閱[az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ 建立新的 AMLcompute 目標。

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    如需詳細資訊，請參閱[az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)。

## <a id="experiments"></a>執行實驗

* 開始您的實驗回合。 使用此命令時，請指定 runconfig 檔的名稱（如果您要查看檔案系統，則 \*為 runconfig），並針對-c 參數。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` 命令會建立 `.azureml` 子目錄，其中包含兩個範例 runconfig 檔案。 
    >
    > 如果您有以程式設計方式建立回合設定物件的 Python 腳本，您可以使用[RunConfig （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)將它儲存為 RunConfig 檔案。
    >
    > 您可以在此[JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)檔案中找到完整的 runconfig 架構。 架構會透過每個物件的 `description` 索引鍵自行記錄。 此外，也有可能值的列舉，以及結尾的範本程式碼片段。

    如需詳細資訊，請參閱[az ml 執行提交-腳本](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

* 查看實驗清單：

    ```azurecli-interactive
    az ml experiment list
    ```

    如需詳細資訊，請參閱[az ml 實驗清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

## <a name="dataset-management"></a>資料集管理

下列命令示範如何使用 Azure Machine Learning 中的資料集：

+ 註冊資料集：

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    如需用來定義資料集之 JSON 檔案格式的詳細資訊，請使用 `az ml dataset register --show-template`。

    如需詳細資訊，請參閱[az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 封存作用中或已被取代的資料集：

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    如需詳細資訊，請參閱[az ml dataset archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 取代資料集：

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    如需詳細資訊，請參閱[az ml dataset 取代](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 列出工作區中的所有資料集：

    ```azurecli-interactive
    az ml dataset list
    ```

    如需詳細資訊，請參閱[az ml dataset list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 取得資料集的詳細資料：

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    如需詳細資訊，請參閱[az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 重新啟用封存或過時的資料集：

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    如需詳細資訊，請參閱[az ml dataset 重新](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)啟動。

+ 取消註冊資料集：

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    如需詳細資訊，請參閱[az ml dataset 取消註冊](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

## <a name="environment-management"></a>環境管理

下列命令示範如何建立、註冊及列出工作區的 Azure Machine Learning[環境](how-to-configure-environment.md)：

+ 建立環境的基案檔案：

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    如需詳細資訊，請參閱[az ml 環境 scaffold](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)。

+ 註冊環境：

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    如需詳細資訊，請參閱[az ml 環境 register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)。

+ 列出已註冊的環境：

    ```azurecli-interactive
    az ml environment list
    ```

    如需詳細資訊，請參閱[az ml 環境 list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)。

+ 下載已註冊的環境：

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    如需詳細資訊，請參閱[az ml 環境下載](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)。

### <a name="environment-configuration-schema"></a>環境設定架構

如果您使用 `az ml environment scaffold` 命令，它會產生可修改的範本 `azureml_environment.json` 檔案，並使用 CLI 來建立自訂環境設定。 最上層物件會鬆散對應至 Python SDK 中的[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)類別。 

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

下表詳細說明 JSON 檔案中的每個最上層欄位、其類型，以及描述。 如果物件類型從 Python SDK 連結到類別，則每個 JSON 欄位與 Python 類別中的公用變數名稱之間會有鬆散的1:1 比對。 在某些情況下，欄位可能會對應至函式引數，而不是類別變數。 例如，`environmentVariables` 欄位會對應至[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)類別中的 `environment_variables` 變數。

| JSON 欄位 | 類型 | 說明 |
|---|---|---|
| `name` | `string` | 環境的名稱。 不要以**Microsoft**或**AzureML**起始名稱。 |
| `version` | `string` | 環境的版本。 |
| `environmentVariables` | `{string: string}` | 環境變數名稱和值的雜湊對應。 |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | 物件，定義要在目標計算資源上使用的 Python 環境和解譯器。 |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | 定義設定以自訂以環境規格為基礎的 Docker 映射。 |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | 區段會進行 Spark 設定。 只有當 framework 設定為 PySpark 時，才會使用它。 |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | 設定 Databricks 程式庫相依性。 |
| `inferencingStackVersion` | `string` | 指定新增至映射的推斷堆疊版本。 若要避免新增推斷堆疊，請將此欄位保留 `null`。 有效的值：「最新」。 |

## <a name="ml-pipeline-management"></a>ML 管線管理

下列命令示範如何使用機器學習管線：

+ 建立機器學習管線：

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    如需詳細資訊，請參閱[az ml 管線 create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create)。

    如需管線 YAML 檔的詳細資訊，請參閱[在 YAML 中定義機器學習管線](reference-pipeline-yaml.md)。

+ 執行管線：

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    如需詳細資訊，請參閱[az ml run submit-管線](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline)。

    如需管線 YAML 檔的詳細資訊，請參閱[在 YAML 中定義機器學習管線](reference-pipeline-yaml.md)。

+ 排程管線：

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    如需詳細資訊，請參閱[az ml 管線 create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)。

    如需有關管線排程 YAML 檔的詳細資訊，請參閱[在 YAML 中定義機器學習管線](reference-pipeline-yaml.md#schedules)。

## <a name="model-registration-profiling-deployment"></a>模型註冊，分析，部署

下列命令示範如何註冊定型的模型，然後將它部署為生產服務：

+ 向 Azure Machine Learning 註冊模型：

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    如需詳細資訊，請參閱[az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)。

+ **選擇性**分析您的模型，以取得最佳的 CPU 和記憶體值來進行部署。
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    如需詳細資訊，請參閱[az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)。

+ 將您的模型部署至 AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    如需推斷設定檔架構的詳細資訊，請參閱[推斷設定架構](#inferenceconfig)。
    
    如需部署設定檔架構的詳細資訊，請參閱[部署設定架構](#deploymentconfig)。

    如需詳細資訊，請參閱[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)。

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>推斷設定架構

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>部署設定架構

### <a name="local-deployment-configuration-schema"></a>本機部署設定架構

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure 容器實例部署設定架構 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes Service 部署設定架構

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>後續步驟

* [MACHINE LEARNING CLI 擴充功能的命令參考](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)。

* [使用 Azure Pipelines 訓練和部署機器學習模型](/azure/devops/pipelines/targets/azure-machine-learning)
