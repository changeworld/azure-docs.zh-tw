---
title: 將 ml 模型部署到 Azure 函數應用（預覽）
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習將模型部署到 Azure 函數應用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: d03a3d482d147d3bc69354ee09dfe0b187610a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927448"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>將機器學習模型部署到 Azure 函數（預覽）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何在 Azure 函數中將 Azure 機器學習中的模型作為函數應用進行部署。

> [!IMPORTANT]
> 雖然 Azure 機器學習和 Azure 函數通常都可用，但從函數的機器學習服務打包模型的功能處於預覽階段。

使用 Azure 機器學習，可以從經過訓練的機器學習模型創建 Docker 映射。 Azure 機器學習現在具有預覽功能，可以將這些機器學習模型構建到函數應用中，這些應用程式可以[部署到 Azure 函數](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)中。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 有關詳細資訊，請參閱[創建工作區](how-to-manage-workspace.md)一文。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 在工作區中註冊的經過培訓的機器學習模型。 如果沒有模型，請使用[圖像分類教程：訓練模型](tutorial-train-models-with-aml.md)來訓練和註冊一個模型。

    > [!IMPORTANT]
    > 本文中的程式碼片段假定您設置了以下變數：
    >
    > * `ws`- Azure 機器學習工作區。
    > * `model`- 將部署的已註冊模型。
    > * `inference_config`- 模型的推理配置。
    >
    > 有關設置這些變數的詳細資訊，請參閱[使用 Azure 機器學習部署模型](how-to-deploy-and-where.md)。

## <a name="prepare-for-deployment"></a>準備開始部署

在部署之前，必須定義將模型作為 Web 服務運行所需的內容。 以下清單描述了部署所需的基本項：

* __條目腳本__。 此腳本接受請求，使用模型對請求進行評分，並返回結果。

    > [!IMPORTANT]
    > 條目腳本特定于您的模型;它必須瞭解傳入請求資料的格式、模型預期資料的格式以及返回給用戶端的資料的格式。
    >
    > 如果請求資料的格式不是模型可用的，則腳本可以將其轉換為可接受的格式。 在返回到用戶端之前，它還可能轉換回應。
    >
    > 預設情況下，在打包函數時，輸入被視為文本。 如果您有興趣使用輸入的原始位元組（例如 Blob 觸發器），則應使用[AMLRequest 接受原始資料](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data)。


* **依賴項**，如運行條目腳本或模型所需的説明器腳本或 Python/Conda 包

這些實體封裝在__推理配置__中。 推斷設定會參考輸入指令碼和其他相依性。

> [!IMPORTANT]
> 創建用於 Azure 函數的推理配置時，必須使用[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py)物件。 請注意，如果要定義自訂環境，則必須將版本>= 1.0.45 的版本\azureml 預設值添加為點依賴項。 此套件包含將模型裝載為 Web 服務所需的功能。 下面的示例演示了創建環境物件並將其與推理配置一起使用：
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

有關環境的詳細資訊，請參閱[創建和管理用於培訓和部署的環境](how-to-use-environments.md)。

有關推理配置的詳細資訊，請參閱使用[Azure 機器學習部署模型](how-to-deploy-and-where.md)。

> [!IMPORTANT]
> 部署到函數時，不需要創建__部署配置__。

## <a name="install-the-sdk-preview-package-for-functions-support"></a>安裝 SDK 預覽包以支援功能

要為 Azure 函數生成包，必須安裝 SDK 預覽包。

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>建立映像

要創建部署到 Azure 函數的 Docker 映射，請使用[azureml.contrib.函數.包](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)或您感興趣的觸發器的特定包函數。 以下程式碼片段演示如何使用模型和推理配置中的 Blob 觸發器創建新包：

> [!NOTE]
> 程式碼片段假定它包含`model`已註冊的模型，`inference_config`並且包含推理環境的配置。 有關詳細資訊，請參閱使用[Azure 機器學習部署模型](how-to-deploy-and-where.md)。

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

此時`show_output=True`將顯示 Docker 生成過程的輸出。 該過程完成後，映射已在工作區的 Azure 容器註冊表中創建。 生成映射後，將顯示 Azure 容器註冊表中的位置。 返回的位置以格式`<acrinstance>.azurecr.io/package@sha256:<hash>`。

> [!NOTE]
> 函數的打包當前支援 HTTP 觸發器、Blob 觸發器和服務匯流排觸發器。 有關觸發器的詳細資訊，請參閱 Azure[函數綁定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns)。

> [!IMPORTANT]
> 保存位置資訊，就像部署映射時使用時一樣。

## <a name="deploy-image-as-a-web-app"></a>將映射部署為 Web 應用

1. 使用以下命令獲取包含映射的 Azure 容器註冊表的登錄憑據。 替換為`<myacr>`以前從`package.location`返回的值。 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    此命令的輸出類似于以下 JSON 文檔：

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    保存__使用者名__和密碼之__一的值。__

1. 如果尚未部署服務的資源組或應用服務方案，以下命令將演示如何同時創建這兩個命令：

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    在此示例中，使用_Linux 基本_定價層`--sku B1`（ 。

    > [!IMPORTANT]
    > Azure 機器學習創建的圖像使用 Linux，因此必須使用 參數`--is-linux`。

1. 創建用於 Web 作業存儲的存儲帳戶，並獲取其連接字串。 替換為`<webjobStorage>`要使用的名稱。

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. 要創建函數應用，請使用以下命令。 替換為`<app-name>`要使用的名稱。 替換`<acrinstance>``<imagename>`和 返回`package.location`前面的值。 替換為`<webjobStorage>`上一步驟中的存儲帳戶的名稱：

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > 此時，已創建函數應用。 但是，由於您尚未為包含映射的 Azure 容器註冊表提供 Blob 觸發器的連接字串或憑據，因此函數應用不處於活動狀態。 在接下來的步驟中，您將提供容器註冊表的連接字串和身份驗證資訊。 

1. 創建用於 Blob 觸發器存儲的存儲帳戶，並獲取其連接字串。 替換為`<triggerStorage>`要使用的名稱。

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    記錄此連接字串以提供給函數應用。 稍後，當我們要求`<triggerConnectionString>`

1. 為存儲帳戶中的輸入和輸出創建容器。 替換為`<triggerConnectionString>`前面返回的連接字串：

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. 要將觸發器連接字串與函數應用關聯，請使用以下命令。 替換為`<app-name>`函數應用的名稱。 替換為`<triggerConnectionString>`前面返回的連接字串：

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. 您需要使用以下命令檢索與創建容器關聯的標記。 替換為`<username>`以前從容器註冊表返回的使用者名：

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    保存返回的值，它將用作下一步中的`imagetag`。

1. 要向函數應用提供訪問容器註冊表所需的憑據，請使用以下命令。 替換為`<app-name>`函數應用的名稱。 替換`<acrinstance>`上`<imagetag>`一步驟中來自 AZ CLI 調用的值。 `<password>`替換`<username>`之前檢索的 ACR 登錄資訊：

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    此命令返回類似于以下 JSON 文檔的資訊：

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

此時，函數應用開始載入圖像。

> [!IMPORTANT]
> 載入映射可能需要幾分鐘時間。 您可以使用 Azure 門戶監視進度。

## <a name="test-the-deployment"></a>測試部署

載入映射且應用可用後，請使用以下步驟觸發應用：

1. 創建包含score.py檔期望的資料的文字檔。 以下示例將處理一個score.py，該score.py需要 10 個數字的陣列：

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > 資料的格式取決於您的score.py和模型的期望。

2. 使用以下命令將此檔上載到前面創建的觸發器存儲 Blob 中的輸入容器。 替換為`<file>`包含資料的檔的名稱。 替換為`<triggerConnectionString>`前面返回的連接字串。 在此示例中，`input`是前面創建的輸入容器的名稱。 如果使用其他名稱，請替換此值：

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    此命令的輸出類似于以下 JSON：

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. 要查看函數生成的輸出，請使用以下命令列出生成的輸出檔案。 替換為`<triggerConnectionString>`前面返回的連接字串。 在此示例中，`output`是前面創建的輸出容器的名稱。 如果您使用其他名稱，請替換此值：

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    此命令的輸出類似于`sample_input_out.json`。

4. 要下載檔案並檢查內容，請使用以下命令。 替換為`<file>`前一命令返回的檔案名。 替換為`<triggerConnectionString>`前面返回的連接字串： 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    完成命令後，打開該檔。 它包含模型返回的資料。

有關使用 Blob 觸發器的詳細資訊，請參閱[創建由 Azure Blob 存儲存儲觸發的函數](/azure/azure-functions/functions-create-storage-blob-triggered-function)。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在[函數](/azure/azure-functions/functions-create-function-linux-custom-image)文檔中配置函數應用。
* 瞭解有關 Blob 存儲觸發器[Azure Blob 存儲綁定的更多詳細資訊](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)。
* [將模型部署到 Azure 應用服務](how-to-deploy-app-service.md)。
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [API 參考](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
