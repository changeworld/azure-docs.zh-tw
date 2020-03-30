---
title: 發佈至 Azure 事件方格 (預覽) 的 Durable Functions
description: 了解如何針對 Durable Functions 設定自動 Azure 事件方格發佈。
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 52ffcd4eb81936ffcfa61580288c60bd59ffb744
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249752"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>發佈至 Azure 事件方格 (預覽) 的 Durable Functions

本文說明如何設定 Durable Functions，將協調流程生命週期事件 (例如已建立、已完成和失敗) 發佈至自訂 [Azure 事件方格主題](https://docs.microsoft.com/azure/event-grid/overview)。

以下是適合使用此功能的一些案例：

* **DevOps 方案（如藍色/綠色部署**）：在實施[並行部署策略](durable-functions-versioning.md#side-by-side-deployments)之前，您可能需要瞭解是否有任何任務正在運行。

* **高級監視和診斷支援**：您可以跟蹤針對查詢（如 Azure SQL 資料庫或 Azure Cosmos DB）優化的外部存儲中的業務流程狀態資訊。

* **長時間執行的背景活動**：如果您使用 Durable Functions 進行長時間執行的背景活動，這項功能可協助您知道目前的狀態。

## <a name="prerequisites"></a>Prerequisites

* 在持久函數專案中安裝[Microsoft.Azure.Web 作業.擴展.持久任務](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)。
* 安裝[Azure 存儲模擬程式](../../storage/common/storage-use-emulator.md)（僅限 Windows）或使用現有的 Azure 存儲帳戶。
* 安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 或使用 [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>建立自訂事件方格主題

從 Durable Functions 建立可供傳送事件的事件方格主題。 下列指示說明如何使用 Azure CLI 建立主題。 也可以[通過使用 PowerShell](../../event-grid/custom-event-quickstart-powershell.md)或使用[Azure 門戶](../../event-grid/custom-event-quickstart-portal.md)來執行此操作。

### <a name="create-a-resource-group"></a>建立資源群組

使用 `az group create` 命令建立資源群組。 目前，Azure 事件網格不支援所有區域。 有關支援哪些區域的資訊，請參閱 Azure[事件網格概述](../../event-grid/overview.md)。

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

## <a name="configure-event-grid-publishing"></a>配置事件網格發佈

在 Durable Functions 專案中，尋找 `host.json` 檔案。

在 `durableTask` 屬性中新增 `eventGridTopicEndpoint` 和 `eventGridKeySettingName`。

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

可能的 Azure 事件網格配置屬性可以在[主機.json 文檔中](../functions-host-json.md#durabletask)找到。 配置`host.json`檔後，函數應用會向事件網格主題發送生命週期事件。 當您在本地和 Azure 中運行函數應用時，這一點有效。

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

如果使用[存儲模擬器](../../storage/common/storage-use-emulator.md)（僅限 Windows），請確保它正常工作。 在執行之前，最好先執行 `AzureStorageEmulator.exe clear all` 命令。

如果使用現有的 Azure 存儲帳戶，請將其連接`UseDevelopmentStorage=true`字串`local.settings.json`替換。

## <a name="create-functions-that-listen-for-events"></a>建立可接聽事件的函式

使用 Azure 門戶，創建另一個函數應用來偵聽持久功能應用發佈的事件。 最好將其定位在事件網格主題的同一區域中。

### <a name="create-an-event-grid-trigger-function"></a>建立事件方格觸發程序函式

建立可接收生命週期事件的函式。 選取 [自訂函式]****。

![選取 [建立自訂函式]。](./media/durable-functions-event-publishing/functions-portal.png)

選擇事件網格觸發器，然後選擇一種語言。

![選取事件方格觸發程序。](./media/durable-functions-event-publishing/eventgrid-trigger.png)

輸入函式名稱，然後選取 `Create`。

![選取事件方格觸發程序。](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

隨即建立具有下列程式碼的函式：

# <a name="c-script"></a>[C# 腳本](#tab/csharp-script)

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

# <a name="javascript"></a>[JAVAscript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

選取 `Add Event Grid Subscription`。 此作業會為您建立的事件方格主題新增事件方格訂用帳戶。 如需詳細資訊，請參閱 [Azure 事件方格概念](https://docs.microsoft.com/azure/event-grid/concepts)。

![選取事件方格觸發程序連結。](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

針對 [主題類型] **** 選取 `Event Grid Topics`。 選取您為事件方格主題所建立的資源群組。 然後選取事件方格主題的執行個體。 按 `Create`。

![建立事件格線訂用帳戶。](./media/durable-functions-event-publishing/eventsubscription.png)

您現在已經準備好接收生命週期事件。

## <a name="run-durable-functions-app-to-send-the-events"></a>運行持久功能應用以發送事件

在前面配置的"持久功能"專案中，在本地電腦上開始調試並開始業務流程。 該應用程式將持久函數生命週期事件發佈到事件網格。 通過在 Azure 門戶中檢查事件網格的日誌，驗證事件網格是否觸發您創建的攔截器函數。

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

* **`id`**：事件網格事件的唯一識別碼。
* **`subject`**： 事件主題的路徑。 `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` 會是 `Running`、`Completed`、`Failed` 和 `Terminated`。  
* **`data`**：持久函數特定參數。
  * **`hubName`**：[任務中心](durable-functions-task-hubs.md)名稱。
  * **`functionName`**：協調器函數名稱。
  * **`instanceId`**：持久函數實例Id。
  * **`reason`**：與跟蹤事件關聯的其他資料。 如需詳細資訊，請參閱 [Durable Functions 中的診斷 (Azure Functions)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**：業務流程運行時狀態。 執行中、已完成、失敗、已取消。
* **`eventType`**："協調器事件"
* **`eventTime`**：事件時間 （UTC）。
* **`dataVersion`**：生命週期事件架構的版本。
* **`metadataVersion`**：中繼資料的版本。
* **`topic`**：事件網格主題資源。

## <a name="how-to-test-locally"></a>本機測試方式

要在本地進行測試，請閱讀[Azure 函數事件網格觸發本地調試](../functions-debug-event-grid-trigger-local.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解 Durable Functions 中的執行個體管理](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [了解 Durable Functions 中的版本控制](durable-functions-versioning.md)
