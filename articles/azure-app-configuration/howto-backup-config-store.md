---
title: 自動備份 Azure 應用程式組態存放區的索引鍵/值
description: 瞭解如何設定應用程式設定存放區之間的索引鍵/值自動備份。
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: b48adfdfda4b3e120b2246e67a70000d25c25f3a
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737090"
---
# <a name="back-up-app-configuration-stores-automatically"></a>自動備份應用程式設定存放區

在本文中，您將瞭解如何設定從主要 Azure 應用程式組態存放區到次要存放區的索引鍵/值自動備份。 自動備份會使用 Azure 事件方格與應用程式設定的整合。 

設定自動備份之後，應用程式設定會將事件發佈至 Azure 事件方格，以針對設定存放區中的索引鍵/值所做的任何變更。 事件方格支援各種不同的 Azure 服務，使用者可以從這些服務訂閱索引鍵/值建立、更新或刪除時發出的事件。

## <a name="overview"></a>概觀

在本文中，您將使用 Azure 佇列儲存體從事件方格接收事件，並使用 Azure Functions 的計時器觸發程式，以批次方式處理佇列中的事件。 

根據事件觸發函式時，它會從主要應用程式設定存放區中提取已變更之索引鍵的最新值，並據此更新次要存放區。 這項設定可協助合併在一小段時間內發生的多項變更，以避免對您的應用程式設定存放區發出過多的要求。

![此圖顯示應用程式設定存放區備份的架構。](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>資源佈建

備份應用程式設定存放區背後的動機，是在不同的 Azure 區域中使用多個設定存放區，以增加應用程式的異地復原能力。 為了達成此目的，您的主要和次要存放區應位於不同的 Azure 區域。 本教學課程中建立的所有其他資源都可以布建在您選擇的任何區域中。 這是因為如果主要區域已關閉，則在主要區域再次可供存取之前，將不會有任何新的備份。

在本教學課程中，您將在區域中建立次要存放區 `centralus` ，並在該區域中建立所有其他資源 `westus` 。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/)。 
- 包含 Azure 開發工作負載的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)。
- [.NET Core SDK](https://dotnet.microsoft.com/download)。
- Azure CLI (2.3.1 或更新版本) 的最新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 如果您是使用 Azure CLI，就必須先使用登入 `az login` 。 您可以選擇性地使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure 資源的邏輯集合。

使用 [az group create](/cli/azure/group) 命令來建立資源群組。

下列範例會在 `westus` 位置建立名為 `<resource_group_name>` 的資源群組。  以資源群組的唯一名稱取代 `<resource_group_name>`。

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>建立應用程式設定存放區

在不同區域中建立您的主要和次要應用程式設定存放區。
`<primary_appconfig_name>`將和取代 `<secondary_appconfig_name>` 為您設定存放區的唯一名稱。 每個存放區名稱都必須是唯一的，因為它是用來做為 DNS 名稱。

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

## <a name="create-a-queue"></a>建立佇列

建立儲存體帳戶和佇列，以接收事件方格所發佈的事件。

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>訂閱您的應用程式設定存放區事件

您可以從主要應用程式設定存放區訂閱這兩個事件：

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

下列命令會針對傳送至佇列的兩個事件建立事件方格訂用帳戶。 端點類型會設定為 `storagequeue` ，而且端點會設定為佇列識別碼。 `<event_subscription_name>`以您選擇的事件訂用帳戶名稱取代。

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

## <a name="create-functions-for-handling-events-from-queue-storage"></a>建立函式以處理佇列儲存體中的事件

### <a name="set-up-with-ready-to-use-functions"></a>以立即可用的函式進行設定

在本文中，您將會使用具有下列屬性的 c # 函式：
- 執行時間堆疊 .NET Core 3。1
- Azure Functions 執行階段版本3。x
- 每隔10分鐘由計時器觸發的函式

為了讓您更輕鬆地開始備份資料，我們已 [測試併發布](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) 可供您使用的函式，而不需要對程式碼進行任何變更。 從 Visual Studio 下載專案檔，並 [將其發佈至您自己的 Azure 函數應用程式](../azure-functions/functions-develop-vs.md#publish-to-azure)。

> [!IMPORTANT]
> 請勿對您下載的程式碼中的環境變數進行任何變更。 您將在下一節中建立必要的應用程式設定。
>

### <a name="build-your-own-function"></a>建立您自己的函式

如果稍早提供的範例程式碼不符合您的需求，您也可以建立自己的函式。 您的函數必須能夠執行下列工作，才能完成備份：
- 定期讀取佇列的內容，以查看它是否包含來自事件方格的任何通知。 請參閱 [儲存體佇列 SDK](../storage/queues/storage-quickstart-queues-dotnet.md) 以取得執行詳細資料。
- 如果您的佇列包含事件 [方格的事件通知](./concept-app-configuration-event.md?branch=pr-en-us-112982#event-schema)，請 `<key, label>` 從事件訊息中解壓縮所有唯一的資訊。 Key 和 label 的組合是主要存放區中索引鍵/值變更的唯一識別碼。
- 從主要存放區讀取所有設定。 只更新在佇列中有對應事件的次要存放區中的設定。 從存在於佇列但主要存放區中的次要存放區刪除所有設定。 您可以使用 [應用程式設定 SDK](https://github.com/Azure/AppConfiguration#sdks) ，以程式設計方式存取您的設定存放區。
- 如果在處理期間沒有例外狀況，請從佇列中刪除訊息。
- 根據您的需求來執行錯誤處理。 請參閱上述的程式碼範例，以查看您可能想要處理的一些常見例外狀況。

若要深入瞭解如何建立函式，請參閱： [在 Azure 中建立由計時器觸發的函數](../azure-functions/functions-create-scheduled-function.md) ，並 [使用 Visual Studio 開發 Azure Functions](../azure-functions/functions-develop-vs.md)。


> [!IMPORTANT]
> 使用您的最佳判斷來，根據您對主要設定存放區進行變更的頻率來選擇計時器排程。 執行函數通常可能會對您的存放區進行節流要求。
>


## <a name="create-function-app-settings"></a>建立函數應用程式設定

如果您使用我們所提供的函式，您需要函數應用程式中的下列應用程式設定：
- `PrimaryStoreEndpoint`：主要應用程式設定存放區的端點。 例如 `https://{primary_appconfig_name}.azconfig.io`。
- `SecondaryStoreEndpoint`：次要應用程式設定存放區的端點。 例如 `https://{secondary_appconfig_name}.azconfig.io`。
- `StorageQueueUri`：佇列 URI。 例如 `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`。

下列命令會在您的函數應用程式中建立必要的應用程式設定。 以函式應用程式的名稱取代 `<function_app_name>`。

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>將存取權授與函數應用程式的受控識別

使用下列命令或 [Azure 入口網站](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) 為您的函數應用程式新增系統指派的受控識別。

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> 若要執行必要的資源建立和角色管理，您的帳戶需要 `Owner` 適當範圍內的許可權， (您的訂用帳戶或資源群組) 。 如果您需要有關角色指派的協助，請瞭解 [如何使用 Azure 入口網站來新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-portal.md)。

使用下列命令或 [Azure 入口網站](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) ，將函數應用程式的受控識別存取權授與您的應用程式設定存放區。 使用這些角色：
- `App Configuration Data Reader`在主要應用程式設定存放區中指派角色。
- `App Configuration Data Owner`在次要應用程式設定存放區中指派角色。

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

使用下列命令或 [Azure 入口網站](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) ，將您的函式應用程式存取權授與您的佇列的受控識別。 指派 `Storage Queue Data Contributor` 佇列中的角色。

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>觸發應用程式組態事件

若要測試一切運作正常，您可以建立、更新或刪除主要存放區中的索引鍵/值。 在計時器觸發程式 Azure Functions 之後，您應該會在次要存放區中自動看到這項變更。

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

您已觸發此事件。 在幾分鐘內，事件方格會將事件通知傳送至您的佇列。 *下次排程執行您* 的函式之後，請查看次要存放區中的設定，以查看其是否包含主要存放區中更新的索引鍵/值。

> [!NOTE]
> 您可以在測試和疑難排解期間 [手動觸發](../azure-functions/functions-manually-run-non-http.md) 函式，而不需等候排程的計時器觸發程式。

確定備份函式順利執行之後，您可以看到金鑰現在存在於您的次要存放區中。

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

如果您在次要存放區中看不到新的設定：

- 請確定在您于主要存放區中建立設定 *之後* ，已觸發備份功能。
- 事件方格有可能無法及時將事件通知傳送至佇列。 檢查您的佇列是否仍包含來自主要存放區的事件通知。 如果有的話，請再次觸發 backup 函數。
- 檢查 [Azure Functions 記錄](../azure-functions/functions-create-scheduled-function.md#test-the-function) 中是否有任何錯誤或警告。
- 使用 [Azure 入口網站](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) ，以確保 Azure 函數應用程式包含 Azure Functions 嘗試讀取的應用程式設定的正確值。
- 您也可以使用 [Azure 應用程式 Insights](../azure-functions/functions-monitoring.md?tabs=cmd)來設定 Azure Functions 的監視和警示。 


## <a name="clean-up-resources"></a>清除資源
如果您打算繼續使用此應用程式設定和事件訂用帳戶，請勿清除在本文中建立的資源。 如果您不打算繼續，請使用下列命令來刪除在本文中建立的資源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何設定金鑰值的自動備份，請深入瞭解如何增加應用程式的異地復原：

- [復原和災害復原](concept-disaster-recovery.md)
