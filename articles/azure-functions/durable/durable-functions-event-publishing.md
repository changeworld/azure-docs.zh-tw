---
title: 發佈至 Azure 事件方格 (預覽) 的 Durable Functions
description: 了解如何針對 Durable Functions 設定自動 Azure 事件方格發佈。
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: c0106f3754e0cdcbf1f295fbe3f1b5def8dc3ca1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83124223"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>發佈至 Azure 事件方格 (預覽) 的 Durable Functions

本文說明如何設定 Durable Functions，將協調流程生命週期事件 (例如已建立、已完成和失敗) 發佈至自訂 [Azure 事件方格主題](https://docs.microsoft.com/azure/event-grid/overview)。

以下是適合使用此功能的一些案例：

* **類似 DevOps 案例的藍色/綠色部署**：您可能想知道在實作[並存部署策略](durable-functions-versioning.md#side-by-side-deployments)前是否有任何工作正在執行中。

* **進階監視和診斷支援**：您可以在已針對查詢最佳化的外部存放區 (例如 Azure SQL Database 或 Azure Cosmos DB) 中，追蹤協調流程狀態資訊。

* **長時間執行的背景活動**：如果您使用 Durable Functions 進行長時間執行的背景活動，這項功能可協助您知道目前的狀態。

## <a name="prerequisites"></a>Prerequisites

* 在您的 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)。
* 安裝 [Azure 儲存體模擬器](../../storage/common/storage-use-emulator.md) (僅限 Windows)，或使用現有的 Azure 儲存體帳戶。
* 安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 或使用 [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>建立自訂事件方格主題

從 Durable Functions 建立可供傳送事件的事件方格主題。 下列指示說明如何使用 Azure CLI 建立主題。 您也可以[使用 PowerShell](../../event-grid/custom-event-quickstart-powershell.md) 或使用 [Azure 入口網站](../../event-grid/custom-event-quickstart-portal.md)來建立主題。

### <a name="create-a-resource-group"></a>建立資源群組

使用 `az group create` 命令建立資源群組。 Azure 事件方格目前不支援所有區域。 如需支援區域的相關資訊，請參閱 [Azure 事件方格概觀](../../event-grid/overview.md)。

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>建立自訂主題

事件方格主題會提供使用者定義的端點，作為您發佈事件的目的地。 以主題的唯一名稱取代 `<topic_name>`。 主題名稱必須是唯一的，因為它會變成 DNS 項目。

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>取得端點和金鑰

取得主題的端點。 使用您所選的名稱取代 `<topic_name>`。

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

取得主題金鑰。 使用您所選的名稱取代 `<topic_name>`。

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

您現在可以將事件傳送至主題。

## <a name="configure-event-grid-publishing"></a>設定 Azure 事件方格發佈

在 Durable Functions 專案中，尋找 `host.json` 檔案。

### <a name="durable-functions-1x"></a>Durable Functions 1.x

在 `durableTask` 屬性中新增 `eventGridTopicEndpoint` 和 `eventGridKeySettingName`。

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2.x

將 `notifications` 區段新增至檔案的 `durableTask` 屬性，並以您選擇的名稱取代 `<topic_name>`。 如果 `durableTask` 或 `extensions` 屬性不存在，請加以建立，如下列範例所示：

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

您可以在 [host.json 文件](../functions-host-json.md#durabletask)中找到可能的 Azure 事件方格設定屬性。 設定 `host.json` 檔案之後，您的函式應用程式會將生命週期事件傳送至事件方格主題。 此動作會於您在本機和 Azure 中執行函式應用程式時開始。

在函式應用程式和 `local.settings.json` 中設定主題索引鍵的應用程式設定。 以下 JSON 是本機偵錯的 `local.settings.json` 範例。 以主題索引鍵取代 `<topic_key>`。  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

如果您使用[儲存體模擬器](../../storage/common/storage-use-emulator.md) (僅限 Windows)，請確定其運作正常。 在執行之前，最好先執行 `AzureStorageEmulator.exe clear all` 命令。

如果您使用現有的 Azure 儲存體帳戶，請使用其連接字串取代 `local.settings.json` 中的 `UseDevelopmentStorage=true`。

## <a name="create-functions-that-listen-for-events"></a>建立可接聽事件的函式

使用 Azure 入口網站，建立另一個函式應用程式來接聽 Durable Functions 應用程式所發佈的事件。 函式的位置最好在與事件方格主題的相同區域。

### <a name="create-an-event-grid-trigger-function"></a>建立事件方格觸發程序函式

1. 在您的函式應用程式下，選取 [函數]，然後選取 [+ 新增]。 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="在 Azure 入口網站中新增函式。" border="true":::

1. 搜尋 [事件方格]，然後選取 **Azure 事件方格觸發程序**範本。 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="在 Azure 入口網站中選取事件方格觸發程式範本。" border="true":::

1. 將新的觸發程式命名，然後選取 [建立函式]。

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="在 Azure 入口網站中將事件方格觸發程序命名。" border="true":::


    隨即建立具有下列程式碼的函式：

    # <a name="c-script"></a>[C# 指令碼](#tab/csharp-script)

    ```csharp
    #r "Newtonsoft.Json"
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(JObject eventGridEvent, ILogger log)
    {
        log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
    }
    ```

   # <a name="javascript"></a>[JavaScript](#tab/javascript)

   ```javascript
   module.exports = async function(context, eventGridEvent) {
       context.log(typeof eventGridEvent);
       context.log(eventGridEvent);
   }
   ```

---

### <a name="add-an-event-grid-subscription"></a>新增事件方格訂用帳戶

您現在可以為所建立的事件方格主題新增事件方格訂用帳戶。 如需詳細資訊，請參閱 [Azure 事件方格概念](https://docs.microsoft.com/azure/event-grid/concepts)。

1. 在您的新函式中，選取 [整合]，然後選取 [事件方格觸發程式 (eventGridEvent)]。 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="選取事件方格觸發程序連結。" border="true":::

1. 選取 [建立事件方格描述]。

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="建立事件方格訂用帳戶。" border="true":::

1. 為您的事件訂用帳戶命名，並選取 [事件方格主題] 主題類型。 

1. 選取訂用帳戶。 然後，選取您為事件方格主題所建立的資源群組和資源。 

1. 選取 [建立]。

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="建立事件格線訂用帳戶。" border="true":::

您現在已經準備好接收生命週期事件。

## <a name="run-durable-functions-app-to-send-the-events"></a>執行 Durable Functions 應用程式以傳送事件

在您稍早設定的 Durable Functions 專案中，開始在本機電腦上進行偵錯並啟動協調流程。 應用程式會將 Durable Functions 生命週期事件發佈到事件方格。 確認事件方格會在 Azure 入口網站中檢查其記錄，以觸發您所建立的接聽程式函式。

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>事件結構描述

下列清單說明生命週期事件結構描述︰

* **`id`** ：事件方格事件的唯一識別碼。
* **`subject`** ：事件主體的路徑。 第 1 課：建立 Windows Azure 儲存體物件`durable/orchestrator/{orchestrationRuntimeStatus}`。 `{orchestrationRuntimeStatus}` 會是 `Running`、`Completed`、`Failed` 和 `Terminated`。  
* **`data`** ：Durable Functions 專屬參數。
  * **`hubName`** ：[TaskHub](durable-functions-task-hubs.md) 名稱。
  * **`functionName`** ：協調器函式名稱。
  * **`instanceId`** ：Durable Functions 執行個體識別碼。
  * **`reason`** ：與追蹤事件相關聯的其他資料。 如需詳細資訊，請參閱 [Durable Functions 中的診斷 (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** ：協調流程執行階段狀態。 執行中、已完成、失敗、已取消。
* **`eventType`** ："orchestratorEvent"
* **`eventTime`** ：事件時間 (UTC)。
* **`dataVersion`** ：生命週期事件結構描述的版本。
* **`metadataVersion`** ：中繼資料的版本。
* **`topic`** ：事件方格主題資源。

## <a name="how-to-test-locally"></a>本機測試方式

若要在本機測試，請參閱 [Azure Functions 事件方格觸發程序本機偵錯](../functions-debug-event-grid-trigger-local.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Durable Functions 中的執行個體管理](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [了解 Durable Functions 中的版本控制](durable-functions-versioning.md)
