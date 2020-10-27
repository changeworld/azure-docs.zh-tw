---
title: 使用 Azure Cache for Redis 將機器學習模型部署到 Azure Functions
description: 在本文中，您會使用 Azure Cache for Redis 實例，將 Azure Machine Learning 中的模型部署為 Azure Functions 中的函數應用程式。 Azure Cache for Redis 的效能和可調整性，與 Azure Machine Learning 模型配對時，您的應用程式可享有低延遲和高輸送量。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d9731455edf0afbe4c0768ae40a51316ac71ad94
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537570"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>使用 Azure Cache for Redis 將機器學習模型部署到 Azure Functions 

在本文中，您會使用 Azure Cache for Redis 實例，將 Azure Machine Learning 中的模型部署為 Azure Functions 中的函數應用程式。  

Azure Cache for Redis 的效能和可調整性，與 Azure Machine Learning 模型配對時，您的應用程式可享有低延遲和高輸送量。 快取特別有説明的幾個案例是在推斷資料時，以及實際的模型推斷結果。 在任一案例中，中繼資料或結果都會儲存在記憶體中，進而提升效能。 

> [!NOTE]
> 雖然 Azure Machine Learning 和 Azure Functions 都已正式推出，但從適用于函式的 Machine Learning 服務封裝模型的功能目前為預覽狀態。  
>

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)。
* Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立工作區](../machine-learning/how-to-manage-workspace.md) 文章。
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。
* 在您的工作區中註冊的已定型機器學習模型。 如果您沒有模型，請使用 [影像分類教學課程：定型模型](../machine-learning/tutorial-train-models-with-aml.md) 來定型和註冊模型。

> [!IMPORTANT]
> 本文中的程式碼片段假設您已設定下列變數：
>
> * `ws` -您的 Azure Machine Learning 工作區。
> * `model` -即將部署的註冊模型。
> * `inference_config` -模型的推斷設定。
>
> 如需有關設定這些變數的詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](../machine-learning/how-to-deploy-and-where.md)。

## <a name="create-an-azure-cache-for-redis-instance"></a>建立 Azure Cache for Redis 執行個體 
您將能夠使用任何基本、標準或高階快取實例，將機器學習模型部署至 Azure Functions。 若要建立快取實例，請遵循下列步驟。  

1. 移至 Azure 入口網站首頁，或開啟側邊欄功能表，然後選取 [ **建立資源** ]。 
   
1. 在 [新增]  頁面上選取 [資料庫]  ，然後選取 [Azure Cache for Redis]  。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="選取 Azure Cache for Redis。":::
   
1. 在 [新的 Redis 快取]  頁面上，設定新快取的設定。
   
   | 設定      | 建議的值  | 描述 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名稱** | 輸入全域唯一名稱。 | 快取名稱必須是 1 到 63 個字元的字串，且只能包含數字、字母或連字號。 名稱的開頭和結尾必須是數字或字母，且不可包含連續的連字號。 您的快取執行個體 *主機名稱* 將是 *\<DNS name>.redis.cache.windows.net* 。 | 
   | **訂用帳戶** | 下拉並選取您的訂用帳戶。 | 這個新的 Azure Cache for Redis 執行個體建立所在的訂用帳戶。 | 
   | **資源群組** | 下拉並選取資源群組，或選取 [新建]  並輸入新的資源群組名稱。 | 用來建立快取和其他資源的資源群組名稱。 將所有的應用程式資源放在一個資源群組中，您將可輕鬆地一併管理或刪除這些資源。 | 
   | **位置** | 下拉並選取位置。 | 選取其他將使用快取的服務附近的[區域](https://azure.microsoft.com/regions/)。 |
   | **定價層** | 下拉並選取 [定價層](https://azure.microsoft.com/pricing/details/cache/)。 |  快取的可用大小、效能和功能取決於定價層。 如需詳細資訊，請參閱 [Azure Cache for Redis 概觀](cache-overview.md)。 |

1. 選取 [網路] 索引標籤，或按一下頁面底部的 [網路] 按鈕。

1. 在 [網路功能] 索引標籤中，選取您的連線方法。

1. 選取頁面底部的 [下一步:進階] 索引標籤，或按一下頁面底部的 [下一步:進階] 按鈕。

1. 在基本或標準快取執行個體的 [進階] 索引標籤中，如果您想要啟用非 TLS 連接埠，請選取啟用切換。

1. 在高階快取執行個體的 [進階] 索引標籤中，設定非 TLS 連接埠、叢集和資料持續性的設定。

1. 選取頁面底部的 [下一步:標記] 索引標籤，或按一下頁面底部的 [下一步:標記] 按鈕。

1. 在 [標記] 索引標籤中，如果您想要分類資源，可以選擇性地輸入名稱和值。 

1. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 索引標籤，其中 Azure 會驗證您的組態。

1. 出現綠色的「通過驗證」訊息之後，請選取 [建立]。

建立快取需要一些時間。 您可以在 Azure Cache for Redis 的 [概觀] 頁面上監視進度。 當 [狀態] 顯示為 [執行中] 時，表示快取已可供使用。 

## <a name="prepare-for-deployment"></a>準備開始部署

在部署之前，您必須定義以 web 服務的形式執行模型所需的內容。 下列清單描述部署所需的核心專案：

* __輸入腳本__ 。 此腳本會接受要求、使用模型來評分要求，並傳回結果。

    > [!IMPORTANT]
    > 專案腳本為您的模型所特有;它必須瞭解傳入要求資料的格式、您的模型所預期的資料格式，以及傳回給用戶端的資料格式。
    >
    > 如果要求資料的格式不能供您的模型使用，腳本就可以將它轉換成可接受的格式。 它也可以在將回應傳回給用戶端之前，先將它轉換。
    >
    > 依預設，封裝函式時，會將輸入視為文字。 如果您有興趣針對 Blob 觸發程式) 使用輸入 (的原始位元組，您應該使用 [AMLRequest 來接受原始資料](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data)。

針對 run 函式，請確定它會連接到 Redis 端點。

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

如需輸入腳本的詳細資訊，請參閱 [定義評分程式碼。](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script)

* 執行輸入腳本或模型所 **需的相依性，例如** helper 腳本或 Python/Conda 套件

這些實體會封裝成 __推斷__ 設定。 推斷設定會參考輸入指令碼和其他相依性。

> [!IMPORTANT]
> 建立用於 Azure Functions 的推斷設定時，您必須使用 [環境](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) 物件。 請注意，如果您要定義自訂環境，您必須將 >= 1.0.45 版 azureml-defaults 版的 azureml 預設值新增為 pip 相依性。 此套件包含將模型裝載為 Web 服務所需的功能。 下列範例示範如何建立環境物件，並將它與推斷設定搭配使用：
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
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

如需環境的詳細資訊，請參閱 [建立和管理用於定型和部署的環境](../machine-learning/how-to-use-environments.md)。

如需有關推斷設定的詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration)。

> [!IMPORTANT]
> 部署至函式時，您不需要建立 __部署__ 設定。

## <a name="install-the-sdk-preview-package-for-functions-support"></a>安裝 SDK preview 套件以取得函數支援

若要建立 Azure Functions 的封裝，您必須安裝 SDK preview 套件。

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>建立映像

若要建立部署至 Azure Functions 的 Docker 映射，請使用 [contrib](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) ，或您想要使用之觸發程式的特定套件函式。 下列程式碼片段示範如何使用來自模型和推斷設定的 HTTP 觸發程式來建立新的封裝：

> [!NOTE]
> 程式碼片段假設 `model` 包含已註冊的模型，且其中 `inference_config` 包含推斷環境的設定。 如需詳細資訊，請參閱 [使用 Azure Machine Learning 部署模型](../machine-learning/how-to-deploy-and-where.md)。

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

若 `show_output=True` 為，則會顯示 Docker 組建進程的輸出。 程式完成後，就會在您工作區的 Azure Container Registry 中建立映射。 建立映射之後，就會顯示 Azure Container Registry 中的位置。 傳回的位置格式為 `<acrinstance>.azurecr.io/package@sha256:<imagename>` 。

> [!NOTE]
> 函數的封裝目前支援 HTTP 觸發程式、Blob 觸發程式和服務匯流排觸發程式。 如需觸發程式的詳細資訊，請參閱 [Azure Functions](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns)系結。

> [!IMPORTANT]
> 儲存位置資訊，因為它會在部署映射時使用。

## <a name="deploy-image-as-a-web-app"></a>將映射部署為 web 應用程式

1. 使用下列命令來取得包含映射之 Azure Container Registry 的登入認證。 取代 `<myacr>` 為先前從傳回的值 `package.location` ： 

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
    > 此時已建立函數應用程式。 不過，因為您尚未提供 HTTP 觸發程式的連接字串，或包含該映射的 Azure Container Registry 的認證，所以函式應用程式不是使用中狀態。 在接下來的步驟中，您會提供容器登錄的連接字串和驗證資訊。 

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

## <a name="test-azure-function-http-trigger"></a>測試 Azure Function HTTP 觸發程式 

我們現在會執行並測試 Azure 函數 HTTP 觸發程式。

1. 在 Azure 入口網站中，移至您的 Azure 函數應用程式。
1. 在 [開發人員] 底下，選取 [程式 **代碼 + 測試** ]。 
1. 選取右側的 [ **輸入** ] 索引標籤。 
1. 按一下 [ **執行** ] 按鈕以測試 AZURE 函數 HTTP 觸發程式。 

您現在已使用 Azure Cache for Redis 實例，成功地將模型從 Azure Machine Learning 部署為函數應用程式。 若要深入瞭解 Azure Cache for Redis，請流覽至下一節中的連結。

## <a name="clean-up-resources"></a>清除資源

如果您準備繼續進行下一個教學課程，則可以保留在本快速入門中所建立的資源，並重複加以使用。

否則，如果您已完成快速入門，您可以刪除在本快速入門中建立的 Azure 資源，以避免產生費用。 

> [!IMPORTANT]
> 刪除資源群組是無法回復的動作。 當您刪除資源群組時，其中包含的所有資源都將永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 如果您是在包含有需要保留之資源的現有資源群組內，建立用來裝載此範例的資源，則可以從每個資源各自的刀鋒視窗中個別刪除每個資源，而不必刪除正個資源群組。

### <a name="to-delete-a-resource-group"></a>刪除資源群組

1. 登入  。

2. 在 [依名稱篩選...]  方塊中，並輸入您的資源群組名稱。 在資源群組的結果清單中，選取  。

系統將會要求您確認是否刪除資源群組。 輸入您的資源群組名稱以進行確認，然後選取 [刪除]  。

不久後，系統便會刪除該資源群組及其所有的資源。

## <a name="next-steps"></a>後續步驟 

* 深入瞭解 [Azure Cache for Redis](./cache-overview.md)
* 瞭解如何在 [函數](../azure-functions/functions-create-function-linux-custom-image.md) 檔中設定函數應用程式。
* [API 參考](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) 
* 建立 [使用 Azure Cache for Redis 的 Python 應用程式](./cache-python-get-started.md)