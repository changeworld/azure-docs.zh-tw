---
title: 使用 Azure CLI 建立工作區
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure CLI 創建新的 Azure 機器學習工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 9a7d0b75140c50df61ff63f350e5b312a6a684c7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617788"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>使用 Azure CLI 為 Azure 機器學習建立工作區
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中,您將瞭解如何使用 Azure CLI 創建 Azure 機器學習工作區。 Azure CLI 提供用於管理 Azure 資源的命令。 到 CLI 的機器學習擴展提供了用於使用 Azure 機器學習資源的命令。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂閱**。 如果沒有,請嘗試[Azure 機器學習的免費或付費版本](https://aka.ms/AMLFree)。

* 要從**本地環境**使用此文件中的 CLI 指令,需要 Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

    如果使用 Azure[雲外殼](https://azure.microsoft.com//features/cloud-shell/),則 CLI 將透過瀏覽器訪問並生活在雲中。

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

## <a name="install-the-machine-learning-extension"></a>安裝機器學習延伸

要安裝機器學習擴展,請使用以下命令:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>建立工作區

Azure 機器學習工作區依賴於以下 Azure 服務或實體:

> [!IMPORTANT]
> 如果不指定現有的 Azure 服務,將在工作區創建期間自動創建一個服務。 必須始終指定資源組。

| 服務 | 指定現有實體的參數 |
| ---- | ---- |
| **Azure 資源群組** | `-g <resource-group-name>`
| **Azure 儲存體帳戶** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure 金鑰保存庫** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>建立資源群組

必須在資源組中創建 Azure 機器學習工作區。 您可以使用現有的資源群組，或建立一個新的群組。 要__建立新的資源組__,請使用以下命令。 取代為`<resource-group-name>`用於此資源組的名稱。 取代為`<location>`要用於此資源群組的 Azure 區域:

> [!TIP]
> 應選擇 Azure 機器學習可用的區域。 有關詳細資訊,請參閱[按區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

此命令的回應類似於以下 JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

有關使用資源群組的詳細資訊,請參閱 az[群組](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)。

### <a name="automatically-create-required-resources"></a>自動建立所需資源

要建立__自動建立服務__的新工作區,請使用以下指令:

> [!TIP]
> 本節中的命令創建基本版本工作區。 要創建企業工作區,請使用該`--sku enterprise`命令`az ml workspace create`的 開關。 有關 Azure 機器學習版本的詳細資訊,請參閱什麼是[Azure 機器學習](overview-what-is-azure-ml.md#sku)。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> 工作區名稱不區分大小寫。

此指令的輸出類似於以下 JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>使用現有的資源

要創建使用現有資源的工作區,必須提供資源的 ID。 使用以下指令取得服務的識別碼:

> [!IMPORTANT]
> 您不必指定所有現有資源。 您可以指定一個或多個。 例如,您可以指定現有存儲帳戶,工作區將創建其他資源。

+ **Azure 儲存帳號**:`az storage account show --name <storage-account-name> --query "id"`

    此指令的回應類似於以下文字,是儲存帳戶的 ID:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure 應用程式見解**:

    1. 安裝應用程式的解延伸:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. 取得應用程式洞察服務的識別碼:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        此指令的回應類似於以下文字,是應用程式見解服務的 ID:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure 金鑰保存 :**`az keyvault show --name <key-vault-name> --query "ID"`

    此指令的回應類似於以下文字,是金鑰保管庫的 ID:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure 容器註冊表**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    此指令的回應類似於以下文字,是容器註冊表的 ID:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > 容器註冊表必須啟用[管理帳戶](/azure/container-registry/container-registry-authentication#admin-account),然後才能與 Azure 機器學習工作區一起使用。

獲得要與工作區一起使用的資源的 ID 後,請`az workspace create -w <workspace-name> -g <resource-group-name>`使用基本 命令併為現有資源添加參數和 ID。 例如,以下命令建立一個工作區,該工作區使用現有的容器註冊表:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

此指令的輸出類似於以下 JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>列出工作區

要列出 Azure 訂閱的所有工作區,請使用以下命令:

```azurecli-interactive
az ml workspace list
```

此指令的輸出類似於以下 JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

有關詳細資訊,請參閱 az [ml 工作區列表](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list)文檔。

## <a name="get-workspace-information"></a>取得工作區資訊

要取得有關工作區的資訊,請使用以下命令:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

此指令的輸出類似於以下 JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

有關詳細資訊,請參閱 az [ml 工作區顯示](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show)文檔。

## <a name="update-a-workspace"></a>更新工作區

要更新工作區,請使用以下命令:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

此指令的輸出類似於以下 JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

有關詳細資訊,請參閱 az [ml 工作區更新](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)文檔。

## <a name="share-a-workspace-with-another-user"></a>與其他使用者共用工作區

要與訂閱上的其他使用者共用工作區,請使用以下命令:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

有關 Azure 機器學習基於角色的存取控制 (RBAC) 的詳細資訊,請參閱[管理使用者和角色](how-to-assign-roles.md)。

有關詳細資訊,請參閱 az [ml 工作區共享](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share)文檔。

## <a name="sync-keys-for-dependent-resources"></a>從資源的同步鍵

如果更改工作區使用的資源之一的存取鍵,請使用以下命令將新鍵與工作區同步:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

有關變更金鑰的詳細資訊,請參閱[重新產生儲存權限取金鑰](how-to-change-storage-access-key.md)。

有關詳細資訊,請參閱 az [ml 工作區同步鍵](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)文檔。

## <a name="delete-a-workspace"></a>刪除工作區

要在不再需要工作區後刪除工作區,請使用以下命令:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> 刪除工作區不會刪除工作區使用的應用程式見解、存儲帳戶、密鑰保管庫或容器註冊表。

還可以刪除資源組,該資源組將刪除工作區和資源組中的所有其他 Azure 資源。 要刪除資源群組,請使用以下指令:

```azurecli-interactive
az group delete -g <resource-group-name>
```

有關詳細資訊,請參閱 az [ml 工作區刪除](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete)文檔。

## <a name="troubleshooting"></a>疑難排解

### <a name="resource-provider-errors"></a>資源提供程式錯誤

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移動工作區

> [!WARNING]
> 不支援將 Azure 機器學習工作區移動到其他訂閱,或將所屬訂閱移動到新租戶。 這樣做可能會導致錯誤。

### <a name="deleting-the-azure-container-registry"></a>移除 Azure 容器註冊表

Azure 機器學習工作區使用 Azure 容器註冊表 (ACR) 執行某些操作。 當 ACR 實體首次需要時,它將自動建立 ACR 實例。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>後續步驟

有關用於機器學習的 Azure CLI 擴充的詳細資訊,請參閱[az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)文檔。
