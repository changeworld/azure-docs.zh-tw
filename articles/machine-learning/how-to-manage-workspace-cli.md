---
title: 使用 Azure CLI 建立工作區
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure CLI 建立新的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 9a7d0b75140c50df61ff63f350e5b312a6a684c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617788"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>使用 Azure CLI 建立 Azure Machine Learning 的工作區
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何使用 Azure CLI 建立 Azure Machine Learning 工作區。 Azure CLI 提供用來管理 Azure 資源的命令。 CLI 的機器學習擴充功能會提供命令，以使用 Azure Machine Learning 資源。

## <a name="prerequisites"></a>先決條件

* **Azure 訂**用帳戶。 如果您沒有帳戶，請試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 若要在您的**本機環境**中使用本檔中的 CLI 命令，您需要[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

    如果您使用[Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)，CLI 就會透過瀏覽器存取，並存在於雲端中。

## <a name="connect-the-cli-to-your-azure-subscription"></a>將 CLI 連接至您的 Azure 訂用帳戶

> [!IMPORTANT]
> 如果您使用 Azure Cloud Shell，可以略過本節。 Cloud shell 會使用您登入 Azure 訂用帳戶的帳戶來自動驗證您的身份。

有數種方式可讓您從 CLI 向您的 Azure 訂用帳戶進行驗證。 最基本的是以互動方式使用瀏覽器進行驗證。 若要以互動方式進行驗證，請開啟命令列或終端機，然後使用下列命令：

```azurecli-interactive
az login
```

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器，並遵循命令列上的指示。 這些指示包含流覽[https://aka.ms/devicelogin](https://aka.ms/devicelogin)和輸入授權碼。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

如需其他驗證方法，請參閱[使用 Azure CLI 登入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

## <a name="install-the-machine-learning-extension"></a>安裝機器學習擴充功能

若要安裝機器學習擴充功能，請使用下列命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>建立工作區

Azure Machine Learning 工作區會依賴下列 Azure 服務或實體：

> [!IMPORTANT]
> 如果您未指定現有的 Azure 服務，則會在建立工作區時自動建立一個。 您一定要指定資源群組。

| Service | 用來指定現有實例的參數 |
| ---- | ---- |
| **Azure 資源群組** | `-g <resource-group-name>`
| **Azure 儲存體帳戶** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure 金鑰保存庫** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>建立資源群組

Azure Machine Learning 工作區必須建立在資源群組內。 您可以使用現有的資源群組，或建立一個新的群組。 若要__建立新的資源群組__，請使用下列命令。 取代`<resource-group-name>`為要用於此資源群組的名稱。 將`<location>`取代為要用於此資源群組的 Azure 區域：

> [!TIP]
> 您應該選取可使用 Azure Machine Learning 的區域。 如需相關資訊，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

此命令的回應類似下列 JSON：

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

如需使用資源群組的詳細資訊，請參閱[az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)。

### <a name="automatically-create-required-resources"></a>自動建立必要的資源

若要建立__自動建立服務__的新工作區，請使用下列命令：

> [!TIP]
> 本節中的命令會建立基本版本工作區。 若要建立企業工作區，請`--sku enterprise`使用參數搭配`az ml workspace create`命令。 如需 Azure Machine Learning 版本的詳細資訊，請參閱[什麼是 Azure Machine Learning](overview-what-is-azure-ml.md#sku)。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> 工作區名稱不區分大小寫。

此命令的輸出類似下列 JSON：

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

若要建立使用現有資源的工作區，您必須提供資源的識別碼。 使用下列命令來取得服務的識別碼：

> [!IMPORTANT]
> 您不需要指定所有現有的資源。 您可以指定一或多個。 例如，您可以指定現有的儲存體帳戶，而工作區將會建立其他資源。

+ **Azure 儲存體帳戶**：`az storage account show --name <storage-account-name> --query "id"`

    此命令的回應類似下列文字，而是儲存體帳戶的識別碼：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure 應用程式深入**解析：

    1. 安裝 application insights 延伸模組：

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. 取得應用程式深入解析服務的識別碼：

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        此命令的回應類似下列文字，而是 application insights 服務的識別碼：

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**：`az keyvault show --name <key-vault-name> --query "ID"`

    此命令的回應類似于下列文字，而是金鑰保存庫的識別碼：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**：`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    此命令的回應類似下列文字，而是容器登錄的識別碼：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > 容器登錄必須先啟用系統[管理員帳戶](/azure/container-registry/container-registry-authentication#admin-account)，才能搭配 Azure Machine Learning 工作區使用。

當您有想要用於工作區的資源識別碼之後，請使用基底`az workspace create -w <workspace-name> -g <resource-group-name>`命令，並新增現有資源的參數和識別碼（s）。 例如，下列命令會建立使用現有容器登錄的工作區：

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

此命令的輸出類似下列 JSON：

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

若要列出 Azure 訂用帳戶的所有工作區，請使用下列命令：

```azurecli-interactive
az ml workspace list
```

此命令的輸出類似下列 JSON：

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

如需詳細資訊，請參閱[az ml workspace list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list)檔。

## <a name="get-workspace-information"></a>取得工作區資訊

若要取得工作區的相關資訊，請使用下列命令：

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

此命令的輸出類似下列 JSON：

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

如需詳細資訊，請參閱[az ml workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show)檔。

## <a name="update-a-workspace"></a>更新工作區

若要更新工作區，請使用下列命令：

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

此命令的輸出類似下列 JSON：

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

如需詳細資訊，請參閱[az ml workspace update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)檔。

## <a name="share-a-workspace-with-another-user"></a>與其他使用者共用工作區

若要與您訂用帳戶上的其他使用者共用工作區，請使用下列命令：

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

如需有關使用 Azure Machine Learning 的角色型存取控制（RBAC）的詳細資訊，請參閱[管理使用者和角色](how-to-assign-roles.md)。

如需詳細資訊，請參閱[az ml workspace share](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share)檔。

## <a name="sync-keys-for-dependent-resources"></a>相依資源的同步金鑰

如果您變更工作區所使用的其中一個資源的存取金鑰，請使用下列命令來同步處理新的金鑰與工作區：

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

如需變更金鑰的詳細資訊，請參閱[重新產生儲存體存取金鑰](how-to-change-storage-access-key.md)。

如需詳細資訊，請參閱[az ml workspace 同步金鑰](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)檔。

## <a name="delete-a-workspace"></a>刪除工作區

若要在不再需要工作區之後將其刪除，請使用下列命令：

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> 刪除工作區並不會刪除工作區所使用的應用程式深入解析、儲存體帳戶、金鑰保存庫或容器登錄。

您也可以刪除資源群組，這會刪除工作區和資源群組中的所有其他 Azure 資源。 若要刪除資源群組，請使用下列命令：

```azurecli-interactive
az group delete -g <resource-group-name>
```

如需詳細資訊，請參閱[az ml workspace delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete)檔。

## <a name="troubleshooting"></a>疑難排解

### <a name="resource-provider-errors"></a>資源提供者錯誤

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移動工作區

> [!WARNING]
> 不支援將您的 Azure Machine Learning 工作區移至不同的訂用帳戶，或將擁有的訂用帳戶移至新的租使用者。 這麼做可能會導致錯誤。

### <a name="deleting-the-azure-container-registry"></a>刪除 Azure Container Registry

Azure Machine Learning 工作區會使用 Azure Container Registry （ACR）進行某些作業。 當 ACR 實例第一次需要時，它會自動建立。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>後續步驟

如需機器學習服務之 Azure CLI 擴充功能的詳細資訊，請參閱[az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)檔。
