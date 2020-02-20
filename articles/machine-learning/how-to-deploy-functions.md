---
title: 將 ml 模型部署至 Azure Functions 應用程式（預覽）
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 將模型部署至 Azure Functions 應用程式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 29c91cf14413a11804de82eeaf08d628b125d76a
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471936"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>將機器學習模型部署到 Azure Functions （預覽）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何在 Azure Functions 中將模型從 Azure Machine Learning 部署為函數應用程式。

> [!IMPORTANT]
> 雖然 Azure Machine Learning 和 Azure Functions 都已正式推出，但從適用于函式的 Machine Learning 服務封裝模型的功能目前為預覽狀態。

有了 Azure Machine Learning，您就可以從定型的機器學習模型建立 Docker 映射。 Azure Machine Learning 現在具有預覽功能，可將這些機器學習模型建立到函式應用程式中，以[部署至 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱[建立工作區](how-to-manage-workspace.md)文章。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 在您的工作區中註冊的定型機器學習模型。 如果您沒有模型，請使用[影像分類教學課程：訓練模型](tutorial-train-models-with-aml.md)來定型並註冊一個。

    > [!IMPORTANT]
    > 本文中的程式碼片段假設您已設定下列變數：
    >
    > * `ws`-您的 Azure Machine Learning 工作區。
    > * `model`-即將部署的已註冊模型。
    > * `inference_config`-模型的推斷設定。
    >
    > 如需有關設定這些變數的詳細資訊，請參閱[使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

## <a name="prepare-for-deployment"></a>準備開始部署

在部署之前，您必須定義以 web 服務的形式執行模型所需的內容。 下列清單描述部署所需的基本專案：

* __輸入腳本__。 此腳本會接受要求、使用模型對要求評分，並傳回結果。

    > [!IMPORTANT]
    > 專案腳本專屬於您的模型;它必須瞭解傳入要求資料的格式、您的模型所預期的資料格式，以及傳回給用戶端的資料格式。
    >
    > 如果要求資料的格式無法供您的模型使用，腳本就可以將它轉換成可接受的格式。 它也可以在將回應傳回給用戶端之前，先將它轉換。
    >
    > 根據預設，封裝函式時，會將輸入視為文字。 如果您有興趣取用輸入的原始位元組（例如，針對 Blob 觸發程式），您應該使用[AMLRequest 來接受原始資料](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data)。


* 執行專案腳本或模型所**需的相依性，例如**helper 腳本或 Python/Conda 套件

這些實體會封裝成__推斷__設定。 推斷設定會參考專案腳本和其他相依性。

> [!IMPORTANT]
> 建立要與 Azure Functions 搭配使用的推斷設定時，您必須使用[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py)物件。 請注意，如果您要定義自訂環境，您必須將 > = 1.0.45 版本的 azureml 預設值新增為 pip 相依性。 此套件包含將模型裝載為 Web 服務所需的功能。 下列範例示範如何建立環境物件，並將它與推斷設定搭配使用：
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

如需環境的詳細資訊，請參閱[建立和管理用於定型和部署的環境](how-to-use-environments.md)。

如需有關推斷設定的詳細資訊，請參閱[使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

> [!IMPORTANT]
> 當部署至函式時，您不需要建立__部署__設定。

## <a name="install-the-sdk-preview-package-for-functions-support"></a>安裝 SDK preview 套件以取得函數支援

若要建立 Azure Functions 的封裝，您必須安裝 SDK preview 套件。

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>建立映像

若要建立部署到 Azure Functions 的 Docker 映射，請使用[contrib](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)或您想要使用之觸發程式的特定封裝功能。 下列程式碼片段示範如何從模型和推斷設定建立具有 blob 觸發程式的新封裝：

> [!NOTE]
> 此程式碼片段假設 `model` 包含已註冊的模型，而且該 `inference_config` 包含推斷環境的設定。 如需詳細資訊，請參閱[使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

當 `show_output=True`時，會顯示 Docker 組建進程的輸出。 程式完成後，就會在您工作區的 Azure Container Registry 中建立映射。 建立映射之後，就會顯示 Azure Container Registry 中的位置。 傳回的位置格式為 `<acrinstance>.azurecr.io/package@sha256:<hash>`。

> [!NOTE]
> 封裝功能目前支援 HTTP 觸發程式、Blob 觸發程式和服務匯流排觸發程式。 如需觸發程式的詳細資訊，請參閱[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns)系結。

> [!IMPORTANT]
> 儲存位置資訊，因為它會在部署映射時使用。

## <a name="deploy-image-as-a-web-app"></a>將映射部署為 web 應用程式

1. 使用下列命令來取得包含映射之 Azure Container Registry 的登入認證。 將 `<myacr>` 取代為先前從 `package.location`傳回的值： 

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

    儲存 [使用者__名稱__] 和其中一個__密碼__的值。

1. 如果您還沒有資源群組或 app service 方案來部署服務，下列命令會示範如何建立兩者：

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    在此範例中，會使用_Linux Premium_定價層（`--sku EP1`）。

    > [!IMPORTANT]
    > Azure Machine Learning 所建立的映射會使用 Linux，因此您必須使用 `--is-linux` 參數。

1. 建立要用於 web 作業儲存體的儲存體帳戶，並取得其連接字串。 以您要使用的名稱取代 `<webjobStorage>`。

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. 若要建立函數應用程式，請使用下列命令。 以您要使用的名稱取代 `<app-name>`。 以先前傳回 `package.location` 中的值取代 `<acrinstance>` 和 `<imagename>`。 將 Replace `<webjobStorage>` 取代為上一個步驟中的儲存體帳戶名稱：

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > 此時，已建立函數應用程式。 不過，由於您尚未將 blob 觸發程式或認證的連接字串提供給包含該影像的 Azure Container Registry，因此函式應用程式不是作用中。 在接下來的步驟中，您會提供容器登錄的連接字串和驗證資訊。 

1. 建立要用於 blob 觸發程式儲存體的儲存體帳戶，並取得其連接字串。 以您要使用的名稱取代 `<triggerStorage>`。

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    記錄此連接字串，以提供給函式應用程式。 我們稍後會在要求 `<triggerConnectionString>` 時使用

1. 在儲存體帳戶中建立輸入和輸出的容器。 將 `<triggerConnectionString>` 取代為先前傳回的連接字串：

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. 若要將觸發程式連接字串與函數應用程式產生關聯，請使用下列命令。 以函數應用程式的名稱取代 `<app-name>`。 將 `<triggerConnectionString>` 取代為先前傳回的連接字串：

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. 您將需要使用下列命令來抓取與所建立容器相關聯的標記。 以先前從容器登錄中傳回的使用者名稱取代 `<username>`：

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    儲存傳回的值，將在下一個步驟中用來做為 `imagetag`。

1. 若要為函數應用程式提供存取容器登錄所需的認證，請使用下列命令。 以您要使用的名稱取代 `<app-name>`。 以上一個步驟中 AZ CLI call 的值取代 `<acrinstance>` 和 `<imagetag>`。 以先前抓取的 ACR 登入資訊取代 `<username>` 和 `<password>`：

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    此命令會傳回與下列 JSON 檔類似的資訊：

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
> 可能需要幾分鐘的時間，才會載入映射。 您可以使用 Azure 入口網站來監視進度。

## <a name="next-steps"></a>後續步驟

* 瞭解[如何在函式檔中](/azure/azure-functions/functions-create-function-linux-custom-image)設定函數應用程式。
* 深入瞭解 Blob 儲存體會觸發[Azure blob 儲存體](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)系結。
* [將您的模型部署至 Azure App Service](how-to-deploy-app-service.md)。
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [API 參考](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
