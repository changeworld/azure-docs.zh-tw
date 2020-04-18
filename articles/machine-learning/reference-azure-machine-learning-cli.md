---
title: CLI 擴充
titleSuffix: Azure Machine Learning
description: 了解 Azure CLI 的 Azure Machine Learning CLI 擴充功能。 Azure CLI 是一個跨平台命令列公用工具，可讓您使用 Azure 雲端的資源。 通過機器學習擴展,您可以使用 Azure 機器學習。 ML CLI 創建和管理資源,如工作區、資料存儲、資料集、管道、模型和部署。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 16f9080487af95e7de5c5f8c91fd5c8d356b7bde
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618071"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>將 CLI 延伸用於 Azure 機器學習
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 的擴充功能，而 Azure CLI 是 Azure 平台的跨平台命令列介面。 此擴展提供用於使用 Azure 機器學習的命令。 它允許您自動執行機器學習活動。 下面的清單提供一些可以使用 CLI 延伸執行的操作範例操作:

+ 執行實驗來建立機器學習服務模型

+ 註冊供客戶使用的機器學習服務模型

+ 封裝、部署和追蹤機器學習模型的生命週期

CLI 不是 Azure Machine Learning SDK 的取代項目。 它是一種互補工具,經過優化,可處理適合自動化的高度參數化任務。

## <a name="prerequisites"></a>Prerequisites

* 若要使用 CLI，您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 要從**本地環境**使用此文件中的 CLI 指令,需要 Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

    如果使用 Azure[雲外殼](https://azure.microsoft.com//features/cloud-shell/),則 CLI 將透過瀏覽器訪問並生活在雲中。

## <a name="full-reference-docs"></a>完整參考文件

尋找[Azure CLI 的 azure-cli-ml 延伸的完整參考文件](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)。

## <a name="connect-the-cli-to-your-azure-subscription"></a>將 CLI 連接至您的 Azure 訂用帳戶

> [!IMPORTANT]
> 如果使用 Azure 雲外殼,則可以跳過此部分。 雲外殼使用登錄到 Azure 訂閱的帳戶自動對您進行身份驗證。

有幾種方法可以從 CLI 對 Azure 訂閱進行身份驗證。 最基本的是使用瀏覽器進行互動式身份驗證。 要進行互動身份驗證,請使用以下命令開啟命令列或終端機:

```azurecli-interactive
az login
```

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則,您需要打開瀏覽器並按照命令列上的說明進行操作。 這些說明涉及瀏[https://aka.ms/devicelogin](https://aka.ms/devicelogin)覽和輸入授權代碼。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

有關其他身份驗證方法,請參閱[使用 Azure CLI 登入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

## <a name="install-the-extension"></a>安裝延伸模組

若要安裝 Machine Learning CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 與下面的命令一起使用的範例檔[可以在這裡](https://aka.ms/azml-deploy-cloud)找到。

出現提示時，請選取 `y` 來安裝擴充功能。

若要確認已安裝擴充功能，請使用下列命令來顯示 ML 特有的子命令清單：

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>更新擴充功能

要更新機器學習 CLI 擴充,請使用以下指令:

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

+ 如果還沒有資源群組,請建立一個資源群組:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ 建立 Azure 機器學習工作區:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > 此命令創建基本版本工作區。 要創建企業工作區,請使用該`--sku enterprise`命令`az ml workspace create`的 開關。 有關 Azure 機器學習版本的詳細資訊,請參閱什麼是[Azure 機器學習](overview-what-is-azure-ml.md#sku)。

    有關詳細資訊,請參閱[az ml 工作區建立](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)。

+ 將工作區配置附加到資料夾,以啟用 CLI 上下文感知。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令建立一`.azureml`個子目錄,其中包含範例 runconfig 和 conda 環境檔。 它還包含用於與`config.json`Azure 機器學習工作區通信的檔。

    有關詳細資訊,請參閱[az ml 資料夾附加](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

+ 將 Azure Blob 容器附加到數據存儲。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    有關詳細資訊,請參閱[az ml 資料儲存額外的執行 。](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)

+ 將檔上載到數據存儲。

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    有關詳細資訊,請參閱[az ml 資料儲存上載](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload)。

+ 將 AKS 群集附加到計算目標。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    有關詳細資訊,請參閱[az ml 計算目標附加 aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ 創建新的 AML 計算目標。

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    關於詳細資訊,請參閱[az ml 計算目標建立 aml 計算](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)。

## <a name="run-experiments"></a><a id="experiments"></a>執行實驗

* 開始您的實驗回合。 使用此命令時,請針對 -c 參數指定 runconfig 檔\*的名稱(如果查看檔案系統,則 .runconfig 之前的文本)。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > 該`az ml folder attach`命令創建`.azureml`一個子目錄,其中包含兩個範例 runconfig 檔。 
    >
    > 如果您有一個以程式設計方式建立執行設定物件的 Python 文稿,則可以使用[RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)將其另存為 runconfig 檔案。
    >
    > 完整的運行配置架構可以在此[JSON 檔中](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)找到。 架構通過每個對象`description`的 鍵進行自我記錄。 此外,還有可能值的枚舉,以及末尾的範本代碼段。

    有關詳細資訊,請參閱[az ml 執行提交文稿](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

* 檢視實驗清單:

    ```azurecli-interactive
    az ml experiment list
    ```

    有關詳細資訊,請參閱[az ml 實驗清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

## <a name="dataset-management"></a>資料組管理

以下命令展示如何在 Azure 機器學習中處理資料集:

+ 註冊資料集:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    有關用於定義資料集的 JSON 檔案的格式的資訊`az ml dataset register --show-template`,請使用 。

    有關詳細資訊,請參閱[az ml 資料集寄存器](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 已儲存到資料集:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    有關詳細資訊,請參閱[az ml 資料集檔](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 棄用資料集:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    有關詳細資訊,請參閱[az ml 資料集棄用](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 列出工作區中的所有資料集:

    ```azurecli-interactive
    az ml dataset list
    ```

    有關詳細資訊,請參閱[az ml 資料集清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 取得資料集的詳細資訊:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    關於詳細資訊,請參考[az ml 資料集顯示](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 重新啟動已存檔或已棄到資料集:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    有關詳細資訊,請參閱[重新啟動 az ml 資料集](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

+ 取消註冊資料集:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    有關詳細資訊,請參閱[az ml 資料集取消註冊](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

## <a name="environment-management"></a>環境管理

以下指令展示如何為工作區建立、註冊和列出 Azure 機器學習[環境](how-to-configure-environment.md):

+ 為環境建立基架檔案:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    有關詳細資訊,請參閱[az ml 環境基架](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)。

+ 註冊環境:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    有關詳細資訊,請參閱[az ml 環境寄存器](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)。

+ 列出已註冊的環境:

    ```azurecli-interactive
    az ml environment list
    ```

    有關詳細資訊,請參閱[az ml 環境清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)。

+ 下載註冊環境:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    有關詳細資訊,請參閱[az ml 環境下載](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)。

### <a name="environment-configuration-schema"></a>環境設定架構

如果使用`az ml environment scaffold`該 命令,它將生成一個`azureml_environment.json`樣本 檔,該檔可以修改並用於使用 CLI 創建自訂環境配置。 頂級物件鬆散地映射到 Python SDK[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)中的類。 

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

下表詳細介紹了 JSON 檔中的每個頂級欄位、類型和說明。 如果物件類型連結到 Python SDK 中的類,則每個 JSON 欄位與 Python 類中的公共變數名稱之間有一個鬆散的 1:1 匹配。 在某些情況下,該欄位可能映射到構造函數參數,而不是類變數。 例如,`environmentVariables`欄位映射到類`environment_variables`[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)中的變數。

| JSON 欄位 | 類型 | 描述 |
|---|---|---|
| `name` | `string` | 環境的名稱。 不要使用**微軟**或**AzureML**開始名稱。 |
| `version` | `string` | 環境的版本。 |
| `environmentVariables` | `{string: string}` | 環境變數名稱和值的哈希映射。 |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | 定義要在目標計算資源上使用的 Python 環境和解釋器的物件。 |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | 定義設置以自定義根據環境規範構建的 Docker 映射。 |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | 該部分配置 Spark 設置。 僅當框架設置為 PySpark 時,才使用它。 |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | 配置數據磚庫依賴項。 |
| `inferencingStackVersion` | `string` | 指定添加到映射的推斷堆疊版本。 為了避免新增推斷堆疊,請保留此欄位`null`。 有效值:「最新」。。 |

## <a name="ml-pipeline-management"></a>ML 管管管理

以下命令展示如何使用機器學習管道:

+ 建立機器學習管道:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    有關詳細資訊,請參閱[az ml 管道建立](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create)。

    關於導管 YAML 檔案的詳細資訊,請參閱[在 YAML 中定義機器學習管線](reference-pipeline-yaml.md)。

+ 執行管道:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    有關詳細資訊,請參閱[az ml 執行提交導管](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline)。

    關於導管 YAML 檔案的詳細資訊,請參閱[在 YAML 中定義機器學習管線](reference-pipeline-yaml.md)。

+ 排程管:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    關於詳細資訊,請參閱[az ml 管道建立計畫](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)。

    有關導管計劃 YAML 檔的詳細資訊,請參閱[在 YAML 中定義機器學習管道](reference-pipeline-yaml.md#schedules)。

## <a name="model-registration-profiling-deployment"></a>模型註冊、分析、部署

下列命令示範如何註冊定型的模型，然後將它部署為生產服務：

+ 向 Azure Machine Learning 註冊模型：

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    有關詳細資訊,請參閱[az ml 模型寄存器](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)。

+ **可選**分析模型,以獲得用於部署的最佳 CPU 和記憶體值。
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    有關詳細資訊,請參閱[az ml 模型設定檔](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)。

+ 將模型部署到 AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    有關推理設定檔架構的詳細資訊,請參考[推理設定架構](#inferenceconfig)。
    
    有關部署設定檔架構的詳細資訊,請參考[部署設定架構](#deploymentconfig)。

    有關詳細資訊,請參閱[az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)。

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>推理設定架構

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>部署設定架構

### <a name="local-deployment-configuration-schema"></a>本地部署設定架構

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure 容器實例部署配置架構 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure 庫伯內斯服務部署配置架構

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>後續步驟

* [機器學習 CLI 延伸的指令參考](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)。

* [使用 Azure 管道訓練和部署機器學習模型](/azure/devops/pipelines/targets/azure-machine-learning)
