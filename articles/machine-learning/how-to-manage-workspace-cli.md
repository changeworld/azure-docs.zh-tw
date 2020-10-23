---
title: 使用 Azure CLI 建立工作區
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure CLI 建立新的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: fd6fc3ee88d63c1d933d3405437ec1bf49e0432e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426357"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>使用 Azure CLI 建立 Azure Machine Learning 的工作區


在本文中，您將了解如何使用 Azure CLI 建立 Azure Machine Learning 工作區。 Azure CLI 提供用來管理 Azure 資源的命令。 CLI 的這個機器學習延伸模組會提供命令，以便使用 Azure Machine Learning 資源。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 如果您沒有訂用帳戶，則可[試用免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 若要從您的**本機環境**使用本文件中的 CLI 命令，您需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)。

    如果您使用 [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)，CLI 可透過瀏覽器存取，並在雲端運作。

## <a name="connect-the-cli-to-your-azure-subscription"></a>將 CLI 連接至您的 Azure 訂用帳戶

> [!IMPORTANT]
> 如果您是使用 Azure Cloud Shell，則可以跳過本節。 Cloud Shell 會使用您登入 Azure 訂閱的帳戶，自動驗證您的身分。

有數種方式可讓您從 CLI 向您的 Azure 訂閱進行驗證。 最簡單的方式是使用瀏覽器，以互動方式進行驗證。 若要以互動方式進行驗證，請開啟命令列或終端機，並使用下列命令：

```azurecli-interactive
az login
```

如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入登入頁面。 否則，您需要開啟瀏覽器，並遵循命令列上的指示。 這些指示需要瀏覽至 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，並輸入授權碼。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

如需其他驗證方法，請參閱[使用 Azure CLI 登入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)。

## <a name="install-the-machine-learning-extension"></a>安裝機器學習延伸模組

若要安裝機器學習延伸模組，請使用下列命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>建立工作區

Azure Machine Learning 工作區依賴下列 Azure 服務或實體：

> [!IMPORTANT]
> 如果未指定現有的 Azure 服務，則會在工作區建立期間自動建立一個。 請務必指定資源群組。 附加您自己的儲存體帳戶時，請確定它符合下列準則：
>
> * 儲存體帳戶 _不_ 是 premium 帳戶 (Premium_LRS 和 Premium_GRS) 
> * 啟用 Azure Blob 和 Azure 檔案功能
> * 階層命名空間 (ADLS Gen 2) 已停用
>
> 這些需求僅適用于工作區使用的 _預設_ 儲存體帳戶。

| 服務 | 用來指定現有執行個體的參數 |
| ---- | ---- |
| **Azure 資源群組** | `-g <resource-group-name>`
| **Azure 儲存體帳戶** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure 金鑰保存庫** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>建立資源群組

Azure Machine Learning 工作區必須在資源群組內建立。 您可以使用現有的資源群組，或建立一個新的群組。 若要「建立新的資源群組」，請使用下列命令。 將 `<resource-group-name>` 取代成要用於此資源群組的名稱。 將 `<location>` 取代成要用於此資源群組的 Azure 區域：

> [!TIP]
> 建議選取可使用 Azure Machine Learning 的區域。 如需資訊，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)。

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

如需使用資源群組的詳細資訊，請參閱 [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest&preserve-view=true)。

### <a name="automatically-create-required-resources"></a>自動建立所需的資源

若要建立會在其中__自動建立服務__的新工作區，請使用下列命令：

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

### <a name="virtual-network-and-private-endpoint"></a>虛擬網路和私人端點

> [!IMPORTANT]
> Azure Government 區域或 Azure 中國的世紀區域無法使用具有 private link 的 Azure Machine Learning 工作區。

如果您想要將工作區的存取限制為虛擬網路，您可以使用下列參數：

* `--pe-name`：已建立之私用端點的名稱。
* `--pe-auto-approval`：是否應自動核准工作區的私人端點連接。
* `--pe-resource-group`：要在其中建立私人端點的資源群組。 必須是包含虛擬網路的相同群組。
* `--pe-vnet-name`：要在其中建立私人端點的現有虛擬網路。
* `--pe-subnet-name`：要在其中建立私人端點的子網名稱。 預設值是 `default`。

如需有關搭配您的工作區使用私人端點和虛擬網路的詳細資訊，請參閱 [虛擬網路隔離和隱私權總覽](how-to-network-security-overview.md)。

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>客戶管理的金鑰和高業務影響工作區

根據預設，工作區的計量和中繼資料會儲存在 Microsoft 所維護的 Azure Cosmos DB 實例中。 這項資料會使用 Microsoft 管理的金鑰進行加密。 

您可以使用 [提供您自己的金鑰]，而不是使用 Microsoft 管理的金鑰。 這麼做會建立 Azure Cosmos DB 實例，以在您的 Azure 訂用帳戶中儲存計量和中繼資料。 使用 `--cmk-keyvault` 參數來指定包含金鑰的 Azure Key Vault，並 `--resource-cmk-uri` 指定保存庫中金鑰的 URL。

> [!IMPORTANT]
> 使用 `--cmk-keyvault` 和參數之前 `--resource-cmk-uri` ，您必須先執行下列動作：
>
> 1. 使用訂用帳戶的參與者許可權，在身分識別和存取管理) 中授權 __Machine Learning 應用程式__ (。
> 1. 遵循 [設定客戶管理的金鑰](/azure/cosmos-db/how-to-setup-cmk) 的步驟：
>     * 註冊 Azure Cosmos DB 提供者
>     * 建立並設定 Azure Key Vault
>     * 產生金鑰
>
>     您不需要手動建立 Azure Cosmos DB 實例，系統會在工作區建立期間為您建立一個實例。 此 Azure Cosmos DB 實例會根據此模式使用名稱，在個別的資源群組中建立： `<your-resource-group-name>_<GUID>` 。
>
> 建立工作區之後，就無法變更此設定。 如果您刪除工作區所使用的 Azure Cosmos DB，也必須刪除正在使用該工作區的工作區。

若要限制 Microsoft 在您的工作區上收集的資料，請使用 `--hbi-workspace` 參數。 

> [!IMPORTANT]
> 只有在建立工作區時，才能選取 [高業務衝擊]。 建立工作區之後，就無法變更此設定。

如需客戶管理的金鑰和高業務影響工作區的詳細資訊，請參閱 [Azure Machine Learning 的企業安全性](concept-enterprise-security.md#encryption-at-rest)。

### <a name="use-existing-resources"></a>使用現有的資源

若要建立使用現有資源的工作區，則必須提供資源的識別碼。 請使用下列命令來取得服務的識別碼：

> [!IMPORTANT]
> 您不需要指定所有現有的資源。 您可指定一或多個資源。 例如，您可指定現有的儲存體帳戶，且工作區將會建立其他資源。

+ **Azure 儲存體帳戶**：`az storage account show --name <storage-account-name> --query "id"`

    此命令的回應類似下列文字，這是儲存體帳戶的識別碼：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > 如果您想要使用現有的 Azure 儲存體帳戶，它不能是 premium 帳戶 (Premium_LRS 和 Premium_GRS) 。 它也不能有階層命名空間 (用於 Azure Data Lake Storage Gen2) 。 使用工作區的 _預設_ 儲存體帳戶並不支援 premium 儲存體或階層命名空間。 您可以搭配使用 premium 儲存體或階層命名空間與 _非預設_ 儲存體帳戶。

+ **Azure Application Insights**：

    1. 安裝 Application Insights 延伸模組：

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. 取得 Application Insights 服務的識別碼：

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        此命令的回應類似下列文字，這是 Application Insights 服務的識別碼：

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**：`az keyvault show --name <key-vault-name> --query "ID"`

    此命令的回應類似下列文字，這是金鑰保存庫的識別碼：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**：`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    此命令的回應類似下列文字，這是容器登錄的識別碼：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > 容器登錄必須啟用[管理帳戶](/azure/container-registry/container-registry-authentication#admin-account)，才能搭配 Azure Machine Learning 工作區使用。

當有想要用於工作區的資源識別碼之後，請使用基底 `az workspace create -w <workspace-name> -g <resource-group-name>` 命令，並新增現有資源的參數和識別碼。 例如，下列命令會建立使用現有容器登錄的工作區：

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

如需詳細資訊，請參閱 [az ml workspace list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-list) 文件。

## <a name="get-workspace-information"></a>取得工作區資訊

若要取得工作區的資訊，請使用下列命令：

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

如需詳細資訊，請參閱 [az ml workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-show) 文件。

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

如需詳細資訊，請參閱 [az ml workspace update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-update) 文件。

## <a name="share-a-workspace-with-another-user"></a>與其他使用者共用工作區

若要與訂用帳戶上的其他使用者共用工作區，請使用下列命令：

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

如需 Azure 角色型存取控制 (Azure RBAC) 搭配 Azure Machine Learning 的詳細資訊，請參閱 [管理使用者和角色](how-to-assign-roles.md)。

如需詳細資訊，請參閱 [az ml workspace share](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-share) 文件。

## <a name="sync-keys-for-dependent-resources"></a>同步相依資源的金鑰

如果您變更了工作區所使用的其中一項資源的存取金鑰，工作區大約需要一小時的時間來同步處理至新的金鑰。 若要強制工作區立即同步處理新的金鑰，請使用下列命令：

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

如需變更金鑰的詳細資訊，請參閱[重新產生儲存體存取金鑰](how-to-change-storage-access-key.md)。

如需詳細資訊，請參閱 [az ml workspace sync-keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-sync-keys) 文件。

## <a name="delete-a-workspace"></a>刪除工作區

若要刪除不再需要的工作區，請使用下列命令：

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> 刪除工作區不會刪除工作區所使用的 Application Insights、儲存體帳戶、金鑰保存庫或容器登錄。

您也可以刪除資源群組，這會刪除資源群組中的工作區和所有其他 Azure 資源。 若要刪除資源群組，請使用下列命令：

```azurecli-interactive
az group delete -g <resource-group-name>
```

如需詳細資訊，請參閱 [az ml workspace delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-delete) 文件。

## <a name="troubleshooting"></a>疑難排解

### <a name="resource-provider-errors"></a>資源提供者錯誤

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移動工作區

> [!WARNING]
> 不支援將 Azure Machine Learning 工作區移至不同的訂用帳戶，或將擁有的訂用帳戶移至新租用戶。 這麼做可能會導致錯誤。

### <a name="deleting-the-azure-container-registry"></a>刪除 Azure Container Registry

Azure Machine Learning 工作區會使用 Azure Container Registry (ACR) 進行某些作業。 它會在第一次需要 ACR 執行個體時自動建立一個。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 機器學習延伸模組的詳細資訊，請參閱 [az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest&preserve-view=true) 文件。
