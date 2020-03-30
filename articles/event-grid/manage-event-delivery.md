---
title: 死信和重試策略 - Azure 事件網格
description: 說明如何為事件方格自訂事件傳遞選項。 設定無效信件目的地，然後指定要重試傳遞的時間長度。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794027"
---
# <a name="dead-letter-and-retry-policies"></a>無效信件與重試原則

當建立事件訂用帳戶時，您可以自訂事件傳遞的設定。 本文說明如何設定無效信件位置及自訂重試設定。 如需這些功能的相關資訊，請參閱[事件方格訊息傳遞和重試](delivery-and-retry.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>設定無效信件位置

若要設定無效信件位置，您必須要有儲存體帳戶用以保存無法傳遞至端點的事件。 範例會取得現有儲存體帳戶的資源識別碼。 它們會建立事件訂閱，使用該儲存體帳戶中的容器作為無效信件端點。

> [!NOTE]
> - 在運行本文中的命令之前，在存儲中創建存儲帳戶和 Blob 容器。
> - 事件網格服務在此容器中創建 Blob。 blob 的名稱將具有事件網格訂閱的名稱，以及大寫中的所有字母。 例如，如果訂閱的名稱為"我的 Blob-訂閱"，則死信 Blob 的名稱將具有 MY-BLOB-訂閱（myblob 容器/MY-BLOB-訂閱/2019/8/8/5/111111111-1111-1111-1111111.json）。 此行為旨在防止 Azure 服務之間在大小寫處理中的差異。


### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

若要關閉無效信件處理，請重新執行命令以建立事件訂閱，但是不提供 `deadletter-endpoint` 的值。 您不需要刪除事件訂閱。

> [!NOTE]
> 如果您在本機電腦上使用 Azure CLI，請使用 Azure CLI 2.0.56 版或更新版本。 如需有關如何安裝最新版 Azure CLI 的指示，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

若要關閉無效信件處理，請重新執行命令以建立事件訂閱，但是不提供 `DeadLetterEndpoint` 的值。 您不需要刪除事件訂閱。

> [!NOTE]
> 如果您在本機電腦上使用 Azure Poweshell，請使用 Azure Poweshell 1.1.0 版或更新版本。 從 [Azure 下載](https://azure.microsoft.com/downloads/)中下載並安裝最新的 Azure PowerShell。

## <a name="set-retry-policy"></a>設定重試原則

當建立事件方格訂用帳戶時，您可以設定事件方格應該嘗試傳遞事件的時間長度。 依預設，事件方格會嘗試 24 小時 (1440 分鐘) 或 30 次。 您可以為事件方格訂用帳戶設定上述任一值。 事件存留時間的值必須是介於 1 和 1440 的整數。 重試次數上限的值必須是介於 1 到 30 之間的整數。

您無法設定[重試排程](delivery-and-retry.md#retry-schedule-and-duration)。

### <a name="azure-cli"></a>Azure CLI

若要將事件存留時間設定為 1440 分鐘以外的值，請使用：

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

若要將重試次數上限設定為 30 以外的值，請使用：

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

如果您同時設定 `event-ttl` 和 `max-deliver-attempts`，事件方格就會使用前者作為到期條件，來判斷何時停止事件傳遞。

### <a name="powershell"></a>PowerShell

若要將事件存留時間設定為 1440 分鐘以外的值，請使用：

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

若要將重試次數上限設定為 30 以外的值，請使用：

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

如果您同時設定 `EventTtl` 和 `MaxDeliveryAttempt`，事件方格就會使用前者作為到期條件，來判斷何時停止事件傳遞。

## <a name="next-steps"></a>後續步驟

* 如需使用 Azure 函式應用程式來處理無效信件事件的範例應用程式，請參閱[適用於 .NET 的 Azure 事件方格無效信件範例](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/) \(英文\)。
* 如需事件傳遞和重試的相關資訊，請參閱[事件格線訊息傳遞與重試](delivery-and-retry.md)。
* 有關事件網格的簡介，請參閱[有關事件網格](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
