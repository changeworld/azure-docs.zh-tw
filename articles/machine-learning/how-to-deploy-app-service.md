---
title: 將 ml 模型部署到 Azure 應用服務（預覽）
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習將模型部署到 Azure 應用服務中的 Web 應用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 3e6cfde20d9f4d56af836e06b0c9a84010dea47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282812"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>將機器學習模型部署到 Azure 應用服務（預覽）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何在 Azure 應用服務中將 Azure 機器學習中的模型部署為 Web 應用。

> [!IMPORTANT]
> 雖然 Azure 機器學習和 Azure 應用服務通常可用，但從機器學習服務到應用服務部署模型的能力處於預覽階段。

使用 Azure 機器學習，可以從經過訓練的機器學習模型創建 Docker 映射。 此映射包含一個 Web 服務，該服務接收資料，將其提交到模型，然後返回回應。 Azure 應用服務可用於部署映射，並提供以下功能：

* 增強安全性的高級[身份驗證](/azure/app-service/configure-authentication-provider-aad)。 身份驗證方法包括 Azure 活動目錄和多因素身份驗證。
* [無需重新部署即可自動縮放](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)。
* [TLS 支援](/azure/app-service/configure-ssl-certificate-in-code)用戶端和服務之間的安全通信。

有關 Azure 應用服務提供的功能的詳細資訊，請參閱[應用服務概述](/azure/app-service/overview)。

> [!IMPORTANT]
> 如果需要能夠記錄與已部署模型一起使用的評分資料或評分結果，則應將其部署到 Azure Kubernetes 服務。 有關詳細資訊，請參閱[收集有關生產模型的資料](how-to-enable-data-collection.md)。

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

    > [!IMPORTANT]
    > Azure 機器學習 SDK 不為 Web 服務訪問資料存儲或資料集提供一種方式。 如果需要部署的模型來訪問存儲在部署外部的資料（如在 Azure 存儲帳戶中），則必須使用相關的 SDK 開發自訂代碼解決方案。 例如[，Python 的 Azure 存儲 SDK。](https://github.com/Azure/azure-storage-python)
    >
    > 另一種可能適用于您的方案的替代方法是[批次處理預測](how-to-use-parallel-run-step.md)，在評分時提供對資料存儲的訪問。

    有關條目腳本的詳細資訊，請參閱使用[Azure 機器學習部署模型](how-to-deploy-and-where.md)。

* **依賴項**，如運行條目腳本或模型所需的説明器腳本或 Python/Conda 包

這些實體封裝在__推理配置__中。 推斷設定會參考輸入指令碼和其他相依性。

> [!IMPORTANT]
> 創建用於 Azure 應用服務的推理配置時，必須使用[環境](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py)物件。 請注意，如果要定義自訂環境，則必須將版本>= 1.0.45 的版本\azureml 預設值添加為點依賴項。 此套件包含將模型裝載為 Web 服務所需的功能。 下面的示例演示了創建環境物件並將其與推理配置一起使用：
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

有關環境的詳細資訊，請參閱[創建和管理用於培訓和部署的環境](how-to-use-environments.md)。

有關推理配置的詳細資訊，請參閱使用[Azure 機器學習部署模型](how-to-deploy-and-where.md)。

> [!IMPORTANT]
> 部署到 Azure 應用服務時，不需要創建__部署配置__。

## <a name="create-the-image"></a>建立映像

要創建部署到 Azure 應用服務的 Docker 映射，請使用[Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)。 以下程式碼片段演示如何從模型和推理配置生成新映射：

> [!NOTE]
> 程式碼片段假定它包含`model`已註冊的模型，`inference_config`並且包含推理環境的配置。 有關詳細資訊，請參閱使用[Azure 機器學習部署模型](how-to-deploy-and-where.md)。

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

此時`show_output=True`將顯示 Docker 生成過程的輸出。 該過程完成後，映射已在工作區的 Azure 容器註冊表中創建。 生成映射後，將顯示 Azure 容器註冊表中的位置。 返回的位置以格式`<acrinstance>.azurecr.io/package:<imagename>`。 例如： `myml08024f78fd10.azurecr.io/package:20190827151241` 。

> [!IMPORTANT]
> 保存位置資訊，就像部署映射時使用時一樣。

## <a name="deploy-image-as-a-web-app"></a>將映射部署為 Web 應用

1. 使用以下命令獲取包含映射的 Azure 容器註冊表的登錄憑據。 替換為`<acrinstance>`以前從`package.location`返回的 e 值。

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

    在此示例中，使用__基本__定價層 （`--sku B1`。

    > [!IMPORTANT]
    > Azure 機器學習創建的圖像使用 Linux，因此必須使用 參數`--is-linux`。

1. 要創建 Web 應用，請使用以下命令。 替換為`<app-name>`要使用的名稱。 替換`<acrinstance>`和`<imagename>`返回`package.location`前面的值：

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    此命令返回類似于以下 JSON 文檔的資訊：

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
    > 此時，已創建 Web 應用。 但是，由於您尚未向包含映射的 Azure 容器註冊表提供憑據，因此 Web 應用不處於活動狀態。 在下一步中，您將提供容器註冊表的身份驗證資訊。

1. 要向 Web 應用提供訪問容器註冊表所需的憑據，請使用以下命令。 替換為`<app-name>`要使用的名稱。 替換`<acrinstance>``<imagename>`和 返回`package.location`前面的值。 `<password>`替換`<username>`之前檢索的 ACR 登錄資訊：

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

此時，Web 應用開始載入圖像。

> [!IMPORTANT]
> 載入映射可能需要幾分鐘時間。 要監視進度，請使用以下命令：
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> 載入映射且網站處於活動狀態後，日誌將顯示一條消息，指出`Container <container name> for site <app-name> initialized successfully and is ready to serve requests`。

部署映射後，可以使用以下命令查找主機名稱：

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

此命令返回類似于以下主機名稱的資訊 - `<app-name>.azurewebsites.net`。 將此值用作服務__的基本 URL__的一部分。

## <a name="use-the-web-app"></a>使用 Web 應用

將請求傳遞到模型的 Web 服務位於`{baseurl}/score`。 例如： `https://<app-name>.azurewebsites.net/score` 。 以下 Python 代碼演示如何將資料提交到 URL 並顯示回應：

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

* 瞭解如何在 Linux 文檔[上的應用服務](/azure/app-service/containers/)中配置 Web 應用。
* 瞭解有關在 Azure[中開始使用自動縮放進行](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)縮放中展開的更多詳細資訊。
* [在 Azure 應用服務中使用 TLS/SSL 憑證](/azure/app-service/configure-ssl-certificate-in-code)。
* [將應用服務應用配置為使用 Azure 活動目錄登錄](/azure/app-service/configure-authentication-provider-aad)。
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
