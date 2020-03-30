---
title: 使用 REST 管理 ML 資源
titleSuffix: Azure Machine Learning
description: 如何使用 REST API 創建、運行和刪除 Azure ML 資源
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580622"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>使用 REST 創建、運行和刪除 Azure ML 資源

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

有幾種方法可以管理 Azure ML 資源。 您可以使用[門戶](https://portal.azure.com/)、[命令列介面](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)或[Python SDK。](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 或者，您可以選擇 REST API。 REST API 以標準方式使用 HTTP 謂詞來創建、檢索、更新和刪除資源。 REST API 適用于任何可以發出 HTTP 要求的語言或工具。 REST 的直觀結構通常使其成為腳本環境和 MLOps 自動化的理想選擇。 

在本文中，您將學會如何：

> [!div class="checklist"]
> * 檢索授權權杖
> * 使用服務主體身份驗證創建格式正確的 REST 請求
> * 使用 GET 請求檢索有關 Azure ML 分層資源的資訊
> * 使用 PUT 和 POST 請求創建和修改資源
> * 使用 DELETE 請求清理資源 
> * 使用基於金鑰的授權對已部署的模型進行評分

## <a name="prerequisites"></a>Prerequisites

- 具有管理許可權的**Azure 訂閱**。 如果您沒有此類訂閱，請嘗試[免費或付費個人訂閱](https://aka.ms/AMLFree)
- [Azure 機器學習工作區](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- 管理 REST 請求使用服務主體身份驗證。 按照[設置 Azure 機器學習資源和工作流的身份驗證](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)中的步驟在工作區中創建服務主體
- **捲曲**實用程式。 **curl**程式在適用于 Linux 或任何 UNIX 分發的[Windows 子系統](https://aka.ms/wslinstall/)中可用。 在 PowerShell 中，**捲曲**是**Invoke-Web** `curl -d "key=val" -X POST uri`請求`Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`的別名，並變為 。 

## <a name="retrieve-a-service-principal-authentication-token"></a>檢索服務主體身份驗證權杖

管理 REST 請求使用 OAuth2 隱式流進行身份驗證。 此身份驗證流使用訂閱的服務主體提供的權杖。 要檢索此權杖，您需要：

- 租戶 ID（標識訂閱所屬的組織）
- 用戶端 ID（將與創建的權杖關聯）
- 您的客戶機密（應保護）

應具有這些值，從回應到創建服務主體，如[設置 Azure 機器學習資源和工作流的身份驗證](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)中所述。 如果您使用的是公司訂閱，則可能沒有創建服務主體的許可權。 在這種情況下，您應該使用[免費或付費的個人訂閱](https://aka.ms/AMLFree)。

要檢索權杖：

1. 開啟終端機視窗
1. 在命令列中輸入以下代碼
1. 將您自己的值替換為`{your-tenant-id}`和`{your-client-id}`。 `{your-client-secret}` 在本文中，由捲曲括弧包圍的字串是您必須用您自己的適當值替換的變數。
1. 執行命令

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

回應應提供一小時的訪問權杖：

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

記下權杖，因為您將使用它對所有後續管理請求進行身份驗證。 為此，您將在所有請求中設置授權標頭：

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

請注意，該值以字串"Bearer"開頭，包括添加權杖之前的單個空格。

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>獲取與訂閱關聯的資源組清單

要檢索與訂閱關聯的資源組清單，請運行：

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

在整個 Azure 中，許多 REST API 都發佈。 每個服務提供者都會按照自己的節奏更新其 API，但不會破壞現有程式。 服務提供者使用 參數`api-version`來確保相容性。 參數`api-version`因服務而異。 例如，對於機器學習服務，當前 API 版本為`2019-11-01`。 對於存儲帳戶，它是`2019-06-01`。 對於金鑰保存庫，它是`2019-09-01`。 所有 REST 調用都應`api-version`將參數設置為預期值。 即使 API 不斷發展，也可以依賴指定版本的語法和語義。 如果向沒有`api-version`參數的提供程式發送請求，回應將包含受支援的值的人工可讀清單。 

上述調用將導致表單的壓縮 JSON 回應： 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>深入瞭解工作區及其資源

若要檢索資源組中的工作區集，運行以下內容、替換`{your-subscription-id}`和`{your-resource-group}`： `{your-access-token}` 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

再次，您將收到一個 JSON 清單，這次包含一個清單，其中每個項都詳細說明了工作區：

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

要處理工作區中的資源，您需要從常規**management.azure.com**伺服器切換到特定于工作區位置的 REST API 伺服器。 請注意上述 JSON`discoveryUrl`回應中金鑰的值。 如果您獲取該 URL，您將收到類似這樣的回應：

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

`api`回應的值是用於其他請求的伺服器 URL。 例如，要列出實驗，請發送以下命令。 替換為`regional-api-server``api`回應的值（例如， `centralus.api.azureml.ms` 還像往常`your-subscription-id``your-access-token`一`your-resource-group`樣`your-workspace-name`替換 、和：

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

同樣，若要在工作區中檢索已註冊的模型，請發送：

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

請注意，要列出路徑在`history/v1.0`列出模型時開始的實驗，路徑從`modelmanagement/v1.0`開始。 REST API 分為多個操作組，每個組具有不同的路徑。 以下連結的 API 參考文檔列出了各種操作的操作、參數和回應代碼。

|區域|Path|參考資料|
|-|-|-|
|構件|工件/v2.0/|[REST API 參考](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|資料存放區|資料存儲/v1.0/|[REST API 參考](https://docs.microsoft.com/rest/api/azureml/datastores)|
|超參數微調|超磁碟機/v1.0/|[REST API 參考](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|模型|模型管理/v1.0/|[REST API 參考](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|執行記錄|執行/v1.0/ 和歷史/v1.0/|[REST API 參考](https://docs.microsoft.com/rest/api/azureml/runs)|

您可以使用以下一般模式流覽 REST API：

|URL 元件|範例|
|-|-|
| https://| |
| 區域-api 伺服器/ | centralus.api.azureml.ms/ |
| 操作路徑/ | 歷史記錄/v1.0/ |
| 訂閱/[您的訂閱 id]/ | 訂閱/abcde123-abab-abab-1234-0123456789abc/ |
| 資源組/[您的資源組]/ | 資源組/我的資源組/ |
| 供應商/運營供應商/ | 供應商/微軟.機器學習服務/ |
| 提供程式-資源路徑/ | 工作區/ML工作區/我的工作區/第一次嘗試/運行/1/ |
| 操作-終結點/ | 專案/中繼資料/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>使用 PUT 和 POST 請求創建和修改資源

除了使用 GET 謂詞進行資源檢索外，REST API 還支援創建訓練、部署和監視 ML 解決方案所需的所有資源。 

訓練和運行 ML 模型需要計算資源。 可以使用以下方式列出工作區的計算資源： 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

要創建或覆蓋指定的計算資源，將使用 PUT 請求。 在以下項中，除了現在熟悉的替換`your-subscription-id` `your-resource-group`、`your-workspace-name`和`your-access-token`， 和`your-compute-name`， 和`location`的值 中`vmSize` `vmPriority`， `scaleSettings` `adminUserName`、`adminUserPassword`和 。 如[機器學習計算 - 創建或更新 SDK 引用中的引用](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)中所述，以下命令將創建專用的單節點Standard_D1（基本 CPU 計算資源），該Standard_D1將在 30 分鐘後縮小規模：

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> 在 Windows 終端中，在發送 JSON 資料時，您可能需要轉義雙引號。 也就是說，文本（如`"location"`變為`\"location\"`）。 

成功的請求將得到回應`201 Created`，但請注意，此回應僅意味著預配過程已經開始。 您需要輪詢（或使用門戶）以確認其成功完成。

### <a name="create-an-experimental-run"></a>創建實驗運行

要在實驗中開始運行，您需要一個包含訓練腳本和相關檔的 ZIP 檔案夾，以及運行定義 JSON 檔。 ZIP 檔案夾的根目錄中必須包含 Python 條目檔。 例如，將一個瑣碎的 Python 程式（如下所示）壓縮到名為**train.zip 的**資料夾中。

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

將下一個程式碼片段另存為**定義.json**。 確認"腳本"值與您剛剛壓縮的 Python 檔的名稱匹配。 確認"目標"值與可用計算資源的名稱匹配。 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

使用`multipart/form-data`內容將這些檔發佈到伺服器：

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

成功的 POST 請求將生成`200 OK`狀態，回應正文包含創建的運行的識別碼：

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

您可以使用現在應該熟悉的 REST 模式監視運行：

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>刪除不再需要的資源

一些（但不是全部）資源支援 DELETE 謂詞。 在提交到 REST API 之前，請檢查[API 參考](https://docs.microsoft.com/rest/api/azureml/)，以便刪除用例。 例如，要刪除模型，可以使用：

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>使用 REST 對已部署的模型進行評分

雖然可以部署模型以便使用服務主體進行身份驗證，但大多數面向用戶端的部署都使用基於金鑰的身份驗證。 您可以在 Studio 的 **"終結點"** 選項卡中查找部署頁面中的相應金鑰。 同一位置將顯示終結點的評分 URI。 模型的輸入必須建模為 JSON 陣列，名為`data`：

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>使用 REST 創建工作區 

每個 Azure ML 工作區都依賴于其他四個 Azure 資源：啟用管理的容器註冊表、金鑰保存庫、應用程式見解資源和存儲帳戶。 在這些資源存在之前，無法創建工作區。 有關創建每個此類資源的詳細資訊，請參閱 REST API 參考。

要創建工作區，請發出類似于 下面的調用`management.azure.com`。 雖然此調用要求您設置大量變數，但它在結構上與本文討論的其他調用相同。 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

您應該接收回應`202 Accepted`，並在返回的標頭中接收`Location`URI。 您可以獲取此 URI 以獲取有關部署的資訊，包括如果其中一個從屬資源出現問題（例如，如果您忘記在容器註冊表上啟用管理員訪問），則提供有用的調試資訊。 

## <a name="troubleshooting"></a>疑難排解

### <a name="resource-provider-errors"></a>資來源提供者錯誤

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移動工作區

> [!WARNING]
> 不支援將 Azure 機器學習工作區移動到其他訂閱，或將所屬訂閱移動到新租戶。 這樣做可能會導致錯誤。

### <a name="deleting-the-azure-container-registry"></a>刪除 Azure 容器註冊表

Azure 機器學習工作區使用 Azure 容器註冊表 （ACR） 執行某些操作。 當 ACR 實例首次需要時，它將自動創建一個 ACR 實例。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>後續步驟

- 流覽完整的[AzureML REST API 引用](https://docs.microsoft.com/rest/api/azureml/)。
- 瞭解如何使用工作室&設計師[預測汽車價格與設計師（預覽）。](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score)
- [使用聚居筆記本](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)探索 Azure 機器學習。
