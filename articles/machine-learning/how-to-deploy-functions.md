---
title: '將 ml 模型部署至 Azure Functions 應用程式 (preview) '
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 將模型部署至 Azure Functions 應用程式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: vaidya-s
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.topic: conceptual
ms.custom: how-to, racking-python
ms.openlocfilehash: cc5bc5b2c9f78854909b965487f7b4299f3a1579
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567446"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>將機器學習模型部署至 Azure Functions (preview) 


瞭解如何將模型從 Azure Machine Learning 部署為 Azure Functions 中的函數應用程式。

> [!IMPORTANT]
> 雖然 Azure Machine Learning 和 Azure Functions 都已正式推出，但從適用于函式的 Machine Learning 服務封裝模型的功能目前為預覽狀態。

使用 Azure Machine Learning，您就可以從已定型的機器學習模型建立 Docker 映射。 Azure Machine Learning 現在有預覽功能可將這些機器學習模型建立至函式應用程式，而這些應用程式可以 [部署到 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立工作區](how-to-manage-workspace.md) 文章。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)。
* 在您的工作區中註冊的已定型機器學習模型。 如果您沒有模型，請使用 [影像分類教學課程：定型模型](tutorial-train-models-with-aml.md) 來定型和註冊模型。

    > [!IMPORTANT]
    > 本文中的程式碼片段假設您已設定下列變數：
    >
    > * `ws` -您的 Azure Machine Learning 工作區。
    > * `model` -即將部署的註冊模型。
    > * `inference_config` -模型的推斷設定。
    >
    > 如需有關設定這些變數的詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

## <a name="prepare-for-deployment"></a>準備開始部署

在部署之前，您必須定義以 web 服務的形式執行模型所需的內容。 下列清單描述部署所需的核心專案：

* __輸入腳本__。 此腳本會接受要求、使用模型來評分要求，並傳回結果。

    > [!IMPORTANT]
    > 專案腳本為您的模型所特有;它必須瞭解傳入要求資料的格式、您的模型所預期的資料格式，以及傳回給用戶端的資料格式。
    >
    > 如果要求資料的格式不能供您的模型使用，腳本就可以將它轉換成可接受的格式。 它也可能會在回應傳回給用戶端之前轉換回應。
    >
    > 依預設，封裝函式時，會將輸入視為文字。 如果您有興趣針對 Blob 觸發程式) 使用輸入 (的原始位元組，您應該使用 [AMLRequest 來接受原始資料](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data)。

如需輸入腳本的詳細資訊，請參閱 [定義評分程式碼](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#script)

* 執行輸入腳本或模型所**需的相依性，例如**helper 腳本或 Python/Conda 套件

這些實體會封裝成 __推斷__設定。 推斷設定會參考輸入指令碼和其他相依性。

> [!IMPORTANT]
> 建立用於 Azure Functions 的推斷設定時，您必須使用 [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) 物件。 請注意，如果您要定義自訂環境，您必須將 >= 1.0.45 版 azureml-defaults 版的 azureml 預設值新增為 pip 相依性。 此套件包含將模型裝載為 Web 服務所需的功能。 下列範例示範如何建立環境物件，並將它與推斷設定搭配使用：
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

如需環境的詳細資訊，請參閱 [建立和管理用於定型和部署的環境](how-to-use-environments.md)。

如需有關推斷設定的詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

> [!IMPORTANT]
> 部署至函式時，您不需要建立 __部署__設定。

## <a name="install-the-sdk-preview-package-for-functions-support"></a>安裝 SDK preview 套件以取得函數支援

若要建立 Azure Functions 的封裝，您必須安裝 SDK preview 套件。

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>建立映像

若要建立部署至 Azure Functions 的 Docker 映射，請使用 [contrib](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py&preserve-view=true) ，或您想要使用之觸發程式的特定套件函式。 下列程式碼片段示範如何使用模型和推斷設定中的 blob 觸發程式來建立新的封裝：

> [!NOTE]
> 程式碼片段假設 `model` 包含已註冊的模型，且其中 `inference_config` 包含推斷環境的設定。 如需詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

若 `show_output=True` 為，則會顯示 Docker 組建進程的輸出。 程式完成後，就會在您工作區的 Azure Container Registry 中建立映射。 建立映射之後，就會顯示 Azure Container Registry 中的位置。 傳回的位置格式為 `<acrinstance>.azurecr.io/package@sha256:<imagename>` 。

> [!NOTE]
> 函數的封裝目前支援 HTTP 觸發程式、Blob 觸發程式和服務匯流排觸發程式。 如需觸發程式的詳細資訊，請參閱 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns)系結。

> [!IMPORTANT]
> 儲存位置資訊，因為它會在部署映射時使用。

## <a name="deploy-image-as-a-web-app"></a>將映射部署為 web 應用程式

1. 使用下列命令來取得包含映射之 Azure Container Registry 的登入認證。 取代 `<myacr>` 為先前從傳回的值 `blob.location` ： 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    此命令的輸出與下列 JSON 檔類似：

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

    儲存使用者 __名稱__ 和其中一個 __密碼__的值。

1. 如果您還沒有資源群組或 app service 方案可部署服務，則下列命令會示範如何建立兩者：

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    在此範例中，會使用 _Linux 基本_ 定價層 (`--sku B1`) 。

    > [!IMPORTANT]
    > Azure Machine Learning 所建立的映射會使用 Linux，因此您必須使用 `--is-linux` 參數。

1. 建立用於 web 作業儲存體的儲存體帳戶，並取得其連接字串。 取代 `<webjobStorage>` 為您要使用的名稱。

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. 若要建立函數應用程式，請使用下列命令。 取代 `<app-name>` 為您要使用的名稱。 `<acrinstance>`將和取代 `<imagename>` 為先前傳回的值 `package.location` 。 `<webjobStorage>`以上一個步驟中的儲存體帳戶名稱取代：

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > 此時已建立函數應用程式。 不過，因為您尚未提供 blob 觸發程式的連接字串，或包含該映射的 Azure Container Registry 的認證，所以函式應用程式不是使用中狀態。 在接下來的步驟中，您會提供容器登錄的連接字串和驗證資訊。 

1. 建立要用於 blob 觸發程式儲存體的儲存體帳戶，並取得其連接字串。 取代 `<triggerStorage>` 為您要使用的名稱。

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    記錄此連接字串，以提供給函數應用程式。 我們稍後會在要求時使用 `<triggerConnectionString>`

1. 在儲存體帳戶中建立輸入和輸出的容器。 取代 `<triggerConnectionString>` 為先前傳回的連接字串：

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. 若要將觸發程式連接字串與函數應用程式產生關聯，請使用下列命令。 取代 `<app-name>` 為函數應用程式的名稱。 取代 `<triggerConnectionString>` 為先前傳回的連接字串：

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. 您必須使用下列命令，來取得與建立之容器相關聯的標記。 取代 `<username>` 為先前從容器登錄傳回的使用者名稱：

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    儲存傳回的值，將在 `imagetag` 下一個步驟中用來做為。

1. 若要使用存取容器登錄所需的認證來提供函數應用程式，請使用下列命令。 取代 `<app-name>` 為函數應用程式的名稱。 `<acrinstance>` `<imagetag>` 使用上一個步驟中 AZ CLI 呼叫的值取代和。 `<username>` `<password>` 以先前抓取的 ACR 登入資訊取代和：

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    此命令會傳回類似下列 JSON 檔的資訊：

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

此時，函數應用程式會開始載入映射。

> [!IMPORTANT]
> 可能需要幾分鐘的時間才會載入映射。 您可以使用 Azure 入口網站來監視進度。

## <a name="test-the-deployment"></a>測試部署

載入映射並提供應用程式之後，請使用下列步驟來觸發應用程式：

1. 建立文字檔，其中包含 score.py 檔預期的資料。 下列範例會使用預期有10個數字陣列的 score.py：

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > 資料的格式取決於您的 score.py 和模型所預期的內容。

2. 使用下列命令，將此檔案上傳至稍早建立的觸發程式儲存體 blob 中的輸入容器。 取代 `<file>` 為包含資料之檔案的名稱。 取代 `<triggerConnectionString>` 為先前傳回的連接字串。 在此範例中， `input` 是稍早建立的輸入容器名稱。 如果您使用不同的名稱，請取代此值：

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    此命令的輸出類似下列 JSON：

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. 若要查看函數所產生的輸出，請使用下列命令來列出產生的輸出檔。 取代 `<triggerConnectionString>` 為先前傳回的連接字串。 在此範例中， `output` 是稍早建立之輸出容器的名稱。 如果您使用不同的名稱，請將此值取代為：

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    此命令的輸出類似于 `sample_input_out.json` 。

4. 若要下載檔案並檢查內容，請使用下列命令。 取代 `<file>` 為前一個命令傳回的檔案名。 取代 `<triggerConnectionString>` 為先前傳回的連接字串： 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    命令完成後，開啟檔案。 它包含模型傳回的資料。

如需使用 blob 觸發程式的詳細資訊，請參閱 [建立 Azure blob 儲存體所觸發](/azure/azure-functions/functions-create-storage-blob-triggered-function) 的函式一文。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在 [函數](/azure/azure-functions/functions-create-function-linux-custom-image) 檔中設定函數應用程式。
* 深入瞭解 Blob 儲存體如何觸發 [Azure blob 儲存體](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)系結。
* [將您的模型部署至 Azure App Service](how-to-deploy-app-service.md)。
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [API 參考](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py&preserve-view=true)
