---
title: 從 Azure 應用程式組態存放區自動備份金鑰值
description: 瞭解如何在應用程式組態存放區之間設定自動備份金鑰值
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: 6dd485adb71bf05be6499f2fc18572e8a28357d7
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209400"
---
# <a name="backup-app-configuration-stores-automatically"></a>備份應用程式組態自動儲存

在本文中，您將瞭解如何設定從主要應用程式組態存放區到次要存放區的金鑰值自動備份。 它會利用 Azure 事件方格與應用程式組態的整合。 設定好之後，應用程式組態會將事件發佈至事件方格，以進行對設定存放區中的索引鍵/值所做的任何變更。 「事件方格」支援各種不同的 Azure 服務，使用者可從中訂閱在建立、更新或刪除索引鍵/值時發出的事件。

## <a name="overview"></a>概觀

在本教學課程中，您將使用 Azure 儲存體佇列來接收事件方格中的事件，並使用 Azure Functions 的計時器觸發程式，以批次方式處理儲存體佇列中的事件。 觸發時，此函式會根據事件來提取已從主要應用程式組態存放區變更的最新索引鍵值，並據以更新次要存放區。 此設定可協助將多項變更結合在一次備份作業的短時間內發生，並避免對您的應用程式組態存放區發出過多的要求。

![應用程式組態存放區備份架構](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>資源佈建

備份應用程式組態存放區背後的動機是在不同的 Azure 區域中使用多個設定存放區，以增加應用程式的地理彈性。 若要達到此目的，您的主要和次要存放區應位於不同的 Azure 區域。 本教學課程中建立的所有其他資源都可以在您選擇的任何區域中布建。 這是因為如果主要區域已關閉，在主要區域可供存取之前，將不會有任何新的備份。

在本教學課程中，您將會在區域 `centralus` 和區域中的所有其他資源中建立次要存放區 `westus` 。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶-[免費建立一個](https://azure.microsoft.com/free/)。 您可以選擇性地使用 Azure Cloud Shell。
- 使用 Azure 開發工作負載[Visual Studio 2019](https://visualstudio.microsoft.com/vs) 。
- 下載並安裝 [.NET Core SDK](https://dotnet.microsoft.com/download)。
- 最新版的 Azure CLI (2.3.1 或更新版本) 。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 如果您使用 Azure CLI，則必須先使用登入 `az login` 。 您可以選擇性地使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure 資源的邏輯集合。

使用 [az group create](/cli/azure/group) 命令來建立資源群組。

下列範例會在 `westus` 位置建立名為 `<resource_group_name>` 的資源群組。  以資源群組的唯一名稱取代 `<resource_group_name>`。

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>建立應用程式組態存放區

在不同區域中建立您的主要和次要應用程式組態存放區。
 `<primary_appconfig_name>`將和取代 `<secondary_appconfig_name>` 為您設定存放區的唯一名稱。 存放區名稱必須是唯一的，因為它是用來做為 DNS 名稱。

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-azure-storage-queue"></a>建立 Azure 儲存體的佇列

建立儲存體帳戶和儲存體佇列，以接收事件方格所發佈的事件。

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>訂閱您的應用程式組態 store 事件

您訂閱主要應用程式組態存放區中的這兩個事件：

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

下列命令會針對傳送至儲存體佇列的兩個事件建立事件方格訂用帳戶，其中端點類型設定為， `storagequeue` 而端點設定為佇列識別碼。 `<event_subscription_name>`以您選擇的事件訂用帳戶名稱取代。

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>建立 Azure Functions 來處理儲存體佇列中的事件

### <a name="setup-with-ready-to-use-azure-functions"></a>使用立即可用的 Azure Functions 進行安裝

在本教學課程中，您將使用具有下列屬性的 c # Azure Functions：
- 執行時間堆疊 .NET Core 3。1
- Azure Functions 執行時間3.x 版
- 計時器每隔10分鐘觸發一次的函式

為了讓您更輕鬆地開始備份資料，我們已測試併發布[Azure Functions](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) ，您可以在不變更程式碼的情況下使用。 下載專案檔案，並[從 Visual Studio 將其發佈至您自己的 Azure 函數應用程式。](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> 請勿對您已下載的程式碼中的環境變數進行任何變更。 您將會在下一節中建立必要的應用程式設定。
>

### <a name="build-your-own-azure-functions"></a>建立您自己的 Azure Functions

如果上述提供的範例程式碼不符合您的需求，您也可以建立自己的 Azure Functions。 您的函式必須能夠執行下列工作，才能完成備份：
- 定期讀取儲存體佇列的內容，以查看它是否包含來自事件方格的任何通知。 如需執行詳細資料，請參閱[儲存體佇列 SDK](/azure/storage/queues/storage-quickstart-queues-dotnet) 。
- 如果您的儲存體佇列包含事件[方格中的事件通知](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema)，請從事件訊息中解壓縮所有唯一的 <金鑰、標籤>。 [索引鍵] 和 [標籤] 組合是主要存放區中索引鍵/值變更的唯一識別碼。
- 從主要存放區讀取所有設定。 僅更新次要存放區中在儲存體佇列中具有對應事件的設定。 刪除儲存體佇列中存在但不在主要存放區中的次要存放區中的所有設定。 您可以利用[應用程式組態 SDK](https://github.com/Azure/AppConfiguration#sdks) ，以程式設計方式存取您的設定存放區。
- 如果在處理期間沒有任何例外狀況，請從儲存體佇列中刪除訊息。
- 請務必根據您的需求來執行錯誤處理。 您可以參考上述程式碼範例，以查看您可能想要處理的一些常見例外狀況。

若要深入瞭解如何建立 Azure Functions，請參閱：[在 Azure 中建立由計時器觸發的](/azure/azure-functions/functions-create-scheduled-function)函式，並[使用 Visual Studio 開發 Azure Functions](/azure/azure-functions/functions-develop-vs)。


> [!IMPORTANT]
> 使用您的最佳 judgement，根據您對主要設定存放區進行變更的頻率來選擇計時器排程。 請記住，執行此功能太頻繁可能會導致您的存放區的節流要求。
>


## <a name="create-azure-function-app-settings"></a>建立 Azure 函數應用程式設定

如果您使用的是我們提供的 Azure Functions，您的 Azure 函數應用程式中需要下列應用程式設定：
- `PrimaryStoreEndpoint`：主要應用程式組態存放區的端點。 例如， `https://{primary_appconfig_name}.azconfig.io`
- `SecondaryStoreEndpoint`：次要應用程式組態存放區的端點。 例如， `https://{secondary_appconfig_name}.azconfig.io`
- `StorageQueueUri`：儲存體佇列 URI。 例如， `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`

下列命令會在您的 Azure 函數應用程式中建立必要的應用程式設定。 `<function_app_name>`將取代為您的 Azure 函數應用程式名稱。

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>授與 Azure 函數應用程式受控識別的存取權

使用下列命令或[Azure 入口網站](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)，為您的 Azure 函數應用程式新增系統指派的受控識別。

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> 若要執行必要的資源建立和角色管理，您的帳戶需要有 `'Owner'` 適當範圍的許可權， (您的訂用帳戶或資源群組) 。 如果您需要角色指派的協助，請瞭解[如何使用 Azure 入口網站新增或移除 Azure 角色指派](/azure/role-based-access-control/role-assignments-portal)。

使用下列命令或[Azure 入口網站](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration)，將您 Azure 函數應用程式存取權的受控識別授與您的應用程式組態存放區。
- 指派 `App Configuration Data Reader` 主要應用程式組態存放區中的角色。
- 指派 `App Configuration Data Owner` 次要應用程式組態存放區中的角色。

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

使用下列命令或[Azure 入口網站](/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal)，將 Azure 函數應用程式存取權的受控識別授與儲存體佇列。 
- 指派 `Storage Queue Data Contributor` 儲存體佇列中的角色。

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>觸發應用程式組態事件

若要測試所有專案是否正常運作，您可以建立/更新/刪除主要存放區中的金鑰值。 當計時器觸發 Azure Functions 之後，您應該會在次要存放區中自動看到這項變更幾秒鐘。

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

您已觸發事件，而在幾分鐘的事件方格中，會將事件通知傳送到您的 Azure 儲存體佇列。 ***下次排程執行 Azure Functions 之後***，請查看次要存放區中的設定，以查看它是否包含來自主要存放區的更新金鑰值。

> [!NOTE]
> 您可以在測試和疑難排解期間[手動觸發您的 Azure Functions](/azure/azure-functions/functions-manually-run-non-http) ，而不需要等候排程的計時器觸發程式。

確定備份功能順利執行之後，您可以看到金鑰現在已出現在次要存放區中。

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>疑難排解

如果您在次要存放區中沒有看到新的設定：

- 請確定在您于主要存放區中建立設定***後***，已觸發備份功能。
- 事件方格有可能無法及時將事件通知傳送到儲存體佇列。 檢查您的儲存體佇列是否仍包含來自主要存放區的事件通知，如果有的話，請再次觸發備份功能。
- 檢查[Azure Functions 記錄](/azure/azure-functions/functions-create-scheduled-function#test-the-function)中是否有任何錯誤或警告。
- 使用[Azure 入口網站](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal)，確保 Azure 函數應用程式包含 Azure Functions 嘗試讀取之應用程式設定的正確值。
- 您也可以使用[Azure 應用程式 Insights](/azure/azure-functions/functions-monitoring?tabs=cmd)來設定 Azure Functions 的監視和警示。 


## <a name="clean-up-resources"></a>清除資源
如果您打算繼續使用此應用程式組態和事件訂用帳戶，請勿清除在本文中建立的資源。 如果您不打算繼續，請使用下列命令來刪除您在本文建立的資源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何設定金鑰值的自動備份，請深入瞭解如何增加應用程式的地理復原：

- [復原和災害復原](concept-disaster-recovery.md)
