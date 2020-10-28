---
title: '將 ml 模型部署至 Azure App Service (preview) '
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 在 Azure App Service 中將模型部署至 Web 應用程式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 31c9f203a8602b6c078fe2e9c672c539140f9990
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744433"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>將機器學習模型部署至 Azure App Service (preview) 


瞭解如何在 Azure App Service 將模型從 Azure Machine Learning 部署為 web 應用程式。

> [!IMPORTANT]
> 雖然 Azure Machine Learning 和 Azure App Service 都已正式推出，但是將模型從 Machine Learning 服務部署到 App Service 的功能目前為預覽狀態。

使用 Azure Machine Learning，您就可以從已定型的機器學習模型建立 Docker 映射。 此影像包含可接收資料的 web 服務，將資料提交至模型，然後傳迴響應。 Azure App Service 可以用來部署映射，並提供下列功能：

* 增強安全性的 Advanced [authentication](/azure/app-service/configure-authentication-provider-aad) 。 驗證方法包括 Azure Active Directory 和多重要素驗證。
* [自動](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) 調整，而不需要重新部署。
* 用戶端與服務之間安全通訊的[TLS 支援](/azure/app-service/configure-ssl-certificate-in-code)。

如需 Azure App Service 所提供之功能的詳細資訊，請參閱 [App Service 總覽](/azure/app-service/overview)。

> [!IMPORTANT]
> 如果您需要能夠記錄部署的模型所使用的評分資料，或是評分的結果，您應該改為部署至 Azure Kubernetes Service。 如需詳細資訊，請參閱 [收集生產模型的資料](how-to-enable-data-collection.md)。

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

在部署之前，您必須定義以 web 服務的形式執行模型所需的內容。 下列清單描述部署所需的主要專案：

* __輸入腳本__ 。 此腳本會接受要求、使用模型來評分要求，並傳回結果。

    > [!IMPORTANT]
    > 專案腳本為您的模型所特有;它必須瞭解傳入要求資料的格式、您的模型所預期的資料格式，以及傳回給用戶端的資料格式。
    >
    > 如果要求資料的格式不能供您的模型使用，腳本就可以將它轉換成可接受的格式。 它也可能會在回應傳回給用戶端之前轉換回應。

    > [!IMPORTANT]
    > Azure Machine Learning SDK 不提供 web 服務存取資料存放區或資料集的方式。 如果您需要部署的模型來存取儲存在部署外部的資料，例如 Azure 儲存體帳戶中，您必須使用相關的 SDK 開發自訂程式碼解決方案。 例如， [適用于 Python 的 AZURE 儲存體 SDK](https://github.com/Azure/azure-storage-python)。
    >
    > 另一個可能適用于您的案例的替代方案是 [批次預測](how-to-use-parallel-run-step.md)，可在評分時提供資料存放區存取權。

    如需輸入腳本的詳細資訊，請參閱[使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

* 執行輸入腳本或模型所 **需的相依性，例如** helper 腳本或 Python/Conda 套件

這些實體會封裝成 __推斷__ 設定。 推斷設定會參考輸入指令碼和其他相依性。

> [!IMPORTANT]
> 建立用於 Azure App Service 的推斷設定時，您必須使用 [環境](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) 物件。 請注意，如果您要定義自訂環境，您必須將 >= 1.0.45 版 azureml-defaults 版的 azureml 預設值新增為 pip 相依性。 此套件包含將模型裝載為 Web 服務所需的功能。 下列範例示範如何建立環境物件，並將它與推斷設定搭配使用：
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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
> 部署至 Azure App Service 時，您不需要建立 __部署__ 設定。

## <a name="create-the-image"></a>建立映像

若要建立部署至 Azure App Service 的 Docker 映射，請使用 [Model. 封裝](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-)。 下列程式碼片段示範如何從模型建立新的映射並推斷設定：

> [!NOTE]
> 程式碼片段假設 `model` 包含已註冊的模型，且其中 `inference_config` 包含推斷環境的設定。 如需詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

若 `show_output=True` 為，則會顯示 Docker 組建進程的輸出。 程式完成後，就會在您工作區的 Azure Container Registry 中建立映射。 建立映射之後，就會顯示 Azure Container Registry 中的位置。 傳回的位置格式為 `<acrinstance>.azurecr.io/package@sha256:<imagename>` 。 例如： `myml08024f78fd10.azurecr.io/package@sha256:20190827151241` 。

> [!IMPORTANT]
> 儲存位置資訊，因為它會在部署映射時使用。

## <a name="deploy-image-as-a-web-app"></a>將映射部署為 web 應用程式

1. 使用下列命令來取得包含映射之 Azure Container Registry 的登入認證。 取代 `<acrinstance>` 為先前從傳回的值 `package.location` ：

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

    儲存使用者 __名稱__ 和其中一個 __密碼__ 的值。

1. 如果您還沒有資源群組或 app service 方案可部署服務，則下列命令會示範如何建立兩者：

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    在此範例中，會使用 () 的 __基本__ 定價層 `--sku B1` 。

    > [!IMPORTANT]
    > Azure Machine Learning 所建立的映射會使用 Linux，因此您必須使用 `--is-linux` 參數。

1. 若要建立 web 應用程式，請使用下列命令。 取代 `<app-name>` 為您要使用的名稱。 `<acrinstance>` `<imagename>` 使用先前傳回的值取代和 `package.location` ：

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    此命令會傳回類似下列 JSON 檔的資訊：

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > 現在已建立 web 應用程式。 不過，因為您尚未提供包含影像之 Azure Container Registry 的認證，所以 web 應用程式不在使用中。 在下一個步驟中，您會提供容器登錄的驗證資訊。

1. 若要為 web 應用程式提供存取容器登錄所需的認證，請使用下列命令。 取代 `<app-name>` 為您要使用的名稱。 `<acrinstance>`將和取代 `<imagename>` 為先前傳回的值 `package.location` 。 `<username>` `<password>` 以先前抓取的 ACR 登入資訊取代和：

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

此時，web 應用程式會開始載入映射。

> [!IMPORTANT]
> 可能需要幾分鐘的時間才會載入映射。 若要監視進度，請使用下列命令：
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> 一旦載入映射且網站處於作用中狀態，記錄檔就會顯示一則訊息，指出 `Container <container name> for site <app-name> initialized successfully and is ready to serve requests` 。

映射部署完成後，您可以使用下列命令來尋找主機名稱：

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

此命令會傳回類似下列主機名稱的資訊 `<app-name>.azurewebsites.net` 。 使用此值做為服務 __基底 url__ 的一部分。

## <a name="use-the-web-app"></a>使用 Web 應用程式

將要求傳遞給模型的 web 服務位於 `{baseurl}/score` 。 例如： `https://<app-name>.azurewebsites.net/score` 。 下列 Python 程式碼示範如何將資料提交至 URL，並顯示回應：

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>後續步驟

* 瞭解如何在 [Linux 上的 App Service](/azure/app-service/containers/) 檔中設定您的 Web 應用程式。
* 深入瞭解如何 [在 Azure 中開始使用自動](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)調整規模。
* [在您的 Azure App Service 中使用 TLS/SSL 憑證](/azure/app-service/configure-ssl-certificate-in-code)。
* [設定您的 App Service 應用程式以使用 Azure Active Directory 登入](/azure/app-service/configure-authentication-provider-aad)。
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
