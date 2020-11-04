---
title: 安裝和使用 Azure Machine Learning CLI
description: 瞭解如何使用 ML 的 Azure CLI 擴充功能，來建立 & 管理資源，例如您的工作區、資料存放區、資料集、管線、模型和部署。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: a413cabf59fbd89cfe53b581d1018231c0e22e84
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323926"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>安裝和使用 Azure Machine Learning 的 CLI 擴充功能


Azure Machine Learning CLI 是 [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) 的擴充功能，而 Azure CLI 是 Azure 平台的跨平台命令列介面。 此擴充功能提供命令，供您使用 Azure Machine Learning， 讓您能夠自動化機器學習活動。 下列清單提供一些可透過 CLI 擴充功能執行的一些範例動作：

+ 執行實驗來建立機器學習服務模型

+ 註冊供客戶使用的機器學習服務模型

+ 封裝、部署和追蹤機器學習模型的生命週期

CLI 不是 Azure Machine Learning SDK 的取代項目。 它是已經過最佳化處理的補充工具，可處理適合自動化的高度參數化工作。

## <a name="prerequisites"></a>Prerequisites

* 若要使用 CLI，您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 若要從您的 **本機環境** 使用本文件中的 CLI 命令，您需要 [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。

    如果您使用 [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)，則可透過瀏覽器存取 CLI，並存留在雲端。

## <a name="full-reference-docs"></a>完整參考文件

尋找[適用於 Azure CLI 的 azure-cli-ml 擴充功能完整參考文件](/cli/azure/ext/azure-cli-ml/?preserve-view=true&view=azure-cli-latest)。

## <a name="connect-the-cli-to-your-azure-subscription"></a>將 CLI 連接至您的 Azure 訂用帳戶

> [!IMPORTANT]
> 如果您是使用 Azure Cloud Shell，則可以跳過本節。 Cloud Shell 會使用您登入 Azure 訂閱的帳戶，自動驗證您的身分。

有數種方式可讓您從 CLI 向您的 Azure 訂閱進行驗證。 最基本的是使用瀏覽器，以互動方式進行驗證。 若要以互動方式進行驗證，請開啟命令列或終端機，並使用下列命令：

```azurecli-interactive
az login
```

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器，並遵循命令列上的指示。 這些指示需要瀏覽至 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，並輸入授權碼。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

如需其他驗證方法，請參閱[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest)。

## <a name="install-the-extension"></a>安裝延伸模組

若要安裝 Machine Learning CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 您可以在[這裡](https://aka.ms/azml-deploy-cloud)找到可與下列命令搭配使用的範例檔案。

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

+ 如果您還沒有資源群組，請建立一個：

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ 建立 Azure Machine Learning 工作區：

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    如需詳細資訊，請參閱 [az ml workspace create](/cli/azure/ext/azure-cli-ml/ml/workspace?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)。

+ 將工作區設定連結至資料夾，以啟用 CLI 內容感知。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令會建立一個 `.azureml` 子目錄，其中包含範例 runconfig 和 conda 環境檔案。 它也包含用來與 Azure Machine Learning 工作區通訊的 `config.json` 檔案。

    如需詳細資訊，請參閱 [az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

+ 連結 Azure Blob 容器做為資料存放區。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    如需詳細資訊，請參閱 [az ml datastore attach-blob](/cli/azure/ext/azure-cli-ml/ml/datastore?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)。

+ 上傳檔案到資料存放區。

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    如需詳細資訊，請參閱 [az ml datastore upload](/cli/azure/ext/azure-cli-ml/ml/datastore?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload)。

+ 連結 AKS 叢集做為計算目標。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    如需詳細資訊，請參閱 [az ml computetarget attach aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

### <a name="compute-clusters"></a>計算叢集

+ 建立新的受控計算叢集。

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ 使用受控識別建立新的受控計算叢集

  + 使用者指派的受控識別

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + 系統指派的受控識別

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ 將受控識別新增至現有的叢集：

    + 使用者指派的受控識別
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + 系統指派的受控識別

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

如需詳細資訊，請參閱 [az ml computetarget create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>計算執行個體
管理計算實例。  在下列所有範例中，計算實例的名稱為 **cpu**

+ 建立新的 computeinstance。

    ```azurecli-interactive
    az ml computetarget create computeinstance  -n cpu -s "STANDARD_D3_V2" -v
    ```

    如需詳細資訊，請參閱 [az ml computetarget create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance)。

+ 停止 computeinstance。

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n cpu -v
    ```

    如需詳細資訊，請參閱 [az ml computetarget stop computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop)。

+ 啟動 computeinstance。

    ```azurecli-interactive
    az ml computetarget start computeinstance -n cpu -v
    ```

    如需詳細資訊，請參閱 [az ml computetarget start computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start)。

+ 重新開機 computeinstance。

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n cpu -v
    ```

    如需詳細資訊，請參閱 [az ml computetarget restart computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart)。

+ 刪除 computeinstance。

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    如需詳細資訊，請參閱 [az ml computetarget delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-delete)。


## <a name="run-experiments"></a><a id="experiments"></a>執行實驗

* 開始您的實驗回合。 使用此命令時，請對應 -c 參數指定 runconfig 檔案的名稱 (如果您是在檔案系統中查看，則為 \*.runconfig 前面的文字)。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` 命令會建立一個 `.azureml` 子目錄，其中包含兩個範例 runconfig 檔案。 
    >
    > 如果您有使用程式設計方式建立執行設定物件的 Python 指令碼，則可以使用 [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) 以將其儲存為 runconfig 檔案。
    >
    > 您可以在此 [JSON 檔案](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)中找到完整的 runconfig 結構描述。 此結構描述是透過每個物件的 `description` 索引鍵自行記錄。 此外，有一些列舉的可能值，以及結尾的範本程式碼片段。

    如需詳細資訊，請參閱 [az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

* 檢視實驗清單：

    ```azurecli-interactive
    az ml experiment list
    ```

    如需詳細資訊，請參閱 [az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

### <a name="hyperdrive-run"></a>HyperDrive 回合

您可以搭配使用 HyperDrive 與 Azure CLI 來執行參數微調回合。 首先，以下列格式建立 HyperDrive 組態檔。 如需超參數微調參數的詳細資訊，請參閱[為您的模型微調超參數](how-to-tune-hyperparameters.md)一文。

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

連同回合組態檔一起新增此檔案。 然後，使用下列命令提交 HyperDrive 回合：
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

請注意 runconfig 中的 *arguments* 區段，以及 HyperDrive config 中的 *parameter space* 。其包含要傳遞至定型指令碼的命令列引數。 runconfig 中的值會在每次反覆運算時維持不變，而 HyperDrive config 中的範圍則會遭到反覆處理。 請勿在這兩個檔案中指定相同的引數。

## <a name="dataset-management"></a>資料集管理

下列命令示範如何使用 Azure Machine Learning 中的資料集：

+ 註冊資料集：

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    如需用於定義資料集的 JSON 檔案格式相關資訊，請使用 `az ml dataset register --show-template`。

    如需詳細資訊，請參閱 [az ml dataset register](/cli/azure/ext/azure-cli-ml/ml/dataset?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-register)。

+ 列出工作區中的所有資料集：

    ```azurecli-interactive
    az ml dataset list
    ```

    如需詳細資訊，請參閱 [az ml dataset list](/cli/azure/ext/azure-cli-ml/ml/dataset?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-list)。

+ 取得資料集的詳細資料：

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    如需詳細資訊，請參閱 [az ml dataset show](/cli/azure/ext/azure-cli-ml/ml/dataset?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-show)。

+ 取消註冊資料集：

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    如需詳細資訊，請參閱 [az ml dataset unregister](/cli/azure/ext/azure-cli-ml/ml/dataset?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)。

## <a name="environment-management"></a>環境管理

下列命令示範如何為您的工作區建立、註冊和列出 Azure Machine Learning [環境](how-to-configure-environment.md)：

+ 為環境建立 Scaffolding 檔案：

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    如需詳細資訊，請參閱 [az ml environment scaffold](/cli/azure/ext/azure-cli-ml/ml/environment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)。

+ 註冊環境：

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    如需詳細資訊，請參閱 [az ml environment register](/cli/azure/ext/azure-cli-ml/ml/environment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)。

+ 列出註冊的環境：

    ```azurecli-interactive
    az ml environment list
    ```

    如需詳細資訊，請參閱 [az ml environment list](/cli/azure/ext/azure-cli-ml/ml/environment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)。

+ 下載註冊的環境：

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    如需詳細資訊，請參閱 [az ml environment download](/cli/azure/ext/azure-cli-ml/ml/environment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)。

### <a name="environment-configuration-schema"></a>環境設定結構描述

如果您使用 `az ml environment scaffold` 命令，它會產生可修改的範本 `azureml_environment.json` 檔案，並使用 CLI 來建立自訂環境設定。 最上層物件會鬆散地對應至 Python SDK 中的 [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) 類別。 

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

下表詳細說明 JSON 檔案中的每個最上層欄位、欄位類型，以及描述。 如果物件類型已連結至 Python SDK 的類別，則每個 JSON 欄位與 Python 類別中的公用變數名稱之間會有鬆散的 1:1 比對。 在某些情況下，欄位可能會對應至建構函式引數，而不是類別變數。 例如，`environmentVariables` 欄位會對應至 [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) 類別中的 `environment_variables` 變數。

| JSON 欄位 | 類型 | 描述 |
|---|---|---|
| `name` | `string` | 環境的名稱。 名稱開頭請勿使用 **Microsoft** 或 **AzureML** 。 |
| `version` | `string` | 環境的版本。 |
| `environmentVariables` | `{string: string}` | 環境變數名稱和值的雜湊對應。 |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection?preserve-view=true&view=azure-ml-py)hat 會定義要在目標計算資源上使用的 Python 環境和解譯器。 |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection?preserve-view=true&view=azure-ml-py) | 定義設定，以自訂根據環境規格建立的 Docker 映像。 |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection?preserve-view=true&view=azure-ml-py) | 此區段可設定 Spark 設定。 只有當架構設為 PySpark 時，才會使用此設定。 |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection?preserve-view=true&view=azure-ml-py) | 設定 Databricks 程式庫相依性。 |
| `inferencingStackVersion` | `string` | 指定新增至映像的推斷堆疊版本。 若要避免新增推斷堆疊，請將此欄位保留 `null`。 有效值："latest"。 |

## <a name="ml-pipeline-management"></a>ML 管線管理

下列命令示範如何使用機器學習管線：

+ 建立機器學習管線：

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    如需詳細資訊，請參閱 [az ml pipeline create](/cli/azure/ext/azure-cli-ml/ml/pipeline?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create)。

    如需關於管線 YAML 檔案的詳細資訊，請參閱[在 YAML 中定義機器學習管線](reference-pipeline-yaml.md)。

+ 執行管線：

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    如需詳細資訊，請參閱 [az ml run submit-pipeline](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline)。

    如需關於管線 YAML 檔案的詳細資訊，請參閱[在 YAML 中定義機器學習管線](reference-pipeline-yaml.md)。

+ 排程管線：

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    如需詳細資訊，請參閱 [az ml pipeline create-schedule](/cli/azure/ext/azure-cli-ml/ml/pipeline?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)。

    如需關於管線排程 YAML 檔案的詳細資訊，請參閱[在 YAML 中定義機器學習管線](reference-pipeline-yaml.md#schedules)。

## <a name="model-registration-profiling-deployment"></a>模型註冊、分析、部署

下列命令示範如何註冊定型的模型，然後將它部署為生產服務：

+ 向 Azure Machine Learning 註冊模型：

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    如需詳細資訊，請參閱 [az ml model register](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)。

+ **選擇性** 分析您的模型，以取得最佳的 CPU 和記憶體以供部署使用。
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    如需詳細資訊，請參閱 [az ml model profile](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)。

+ 將您的模型部署至 AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    如需關於推斷設定檔結構描述的詳細資訊，請參閱[推斷設定結構描述](#inferenceconfig)。
    
    如需關於部署設定檔結構描述的詳細資訊，請參閱[部署設定結構描述](#deploymentconfig)。

    如需詳細資訊，請參閱 [az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)。

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>推斷設定結構描述

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>部署設定結構描述

### <a name="local-deployment-configuration-schema"></a>本地部署設定結構描述

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure 容器執行個體部署設定結構描述 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes Service 部署設定結構描述

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>後續步驟

* [Machine Learning CLI 擴充功能的命令參考](/cli/azure/ext/azure-cli-ml/ml?preserve-view=true&view=azure-cli-latest)。

* [使用 Azure Pipelines 定型及部署機器學習模型](/azure/devops/pipelines/targets/azure-machine-learning)