---
title: 管理端點和路由
titleSuffix: Azure Digital Twins
description: 瞭解如何設定和管理 Azure 數位 Twins 資料的端點和事件路由。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 923ae652872246916b2a4c5e8be95871983dbe95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559836"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>管理 Azure 數位 Twins 中的端點和路由

在 Azure 數位 Twins 中，您可以將[事件通知](how-to-interpret-event-data.md)路由傳送至下游服務，或連接到計算資源。 這是藉由先設定可接收事件的**端點**來完成，後面接著[**事件路由**](concepts-route-events.md)，以指定 Azure 數位 Twins 所產生的哪些事件會傳遞給哪些端點。

支援的端點類型包括：
* [事件中樞](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)

如需不同端點的詳細資訊，請參閱在[Azure 訊息服務之間進行選擇](https://docs.microsoft.com/azure/event-grid/compare-messaging-services)。

端點和路由是使用[**EventRoutes api**](how-to-use-apis-sdks.md)、 [.Net （c #） SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或[Azure 數位 Twins CLI](how-to-use-cli.md)來管理。 它們也可以透過[Azure 入口網站](https://portal.azure.com)來管理。

> [!NOTE]
> 透過 Azure 入口網站管理事件路由目前僅適用于公司網域帳戶上的 Azure 使用者。 
>
>如果您使用個人[Microsoft 帳戶（MSA）](https://account.microsoft.com/account/Account)（例如帳戶）， @outlook.com 請使用 Azure 數位 TWINS api 或 CLI 來管理事件路由，如這篇文章中所述。

## <a name="create-an-endpoint-for-azure-digital-twins"></a>建立 Azure 數位 Twins 的端點

若要將端點連結至 Azure 數位 Twins，您要用於端點的事件中樞、事件方格主題或服務匯流排必須已經存在。 

下列範例示範如何使用 Azure CLI 建立事件方格主題：

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> 若要輸出可傳遞至 Azure CLI 命令中的 Azure 區域名稱清單，請執行此命令：
> ```azurecli
> az account list-locations -o table
> ```

建立主題之後，您可以使用下列命令將它連結至 Azure 數位 Twins：

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

這會讓「事件方格」主題以引數所指定的名稱，在 Azure 數位 Twins 內提供 `--endpoint-name` 。 您通常會使用該名稱做為**事件路由**的目標，您將在下一節中使用 Azure 數位 TWINS 服務 API 來建立。

事件中樞和服務匯流排端點都有對等的命令：

* 新增服務匯流排主題端點（需要預先建立的服務匯流排資源）
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* 新增事件中樞端點（需要預先建立的事件中樞資源）
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>使用 Api 管理事件路由

若要實際將資料從 Azure 數位 Twins 傳送至端點，您必須定義事件路由。 Azure 數位 Twins **EventRoutes api**可讓開發人員連接整個系統和下游服務的事件流程。 如需深入瞭解事件路由，請參閱[概念：路由 Azure 數位 Twins 事件](concepts-route-events.md)。

本文中的範例會使用 c # SDK。

事件路由是使用資料平面 Api 所定義。 路由定義可以包含下列元素：
* 您想要使用的路由識別碼
* 您想要使用的端點名稱
* 定義哪些事件要傳送至端點的篩選器 

如果沒有路由識別碼，則不會在 Azure 數位 Twins 外路由傳送任何訊息。 如果有路由識別碼，且篩選為，則 `true` 所有訊息都會路由傳送至端點。 如果有路由識別碼，而且新增了不同的篩選準則，則會根據篩選準則來篩選訊息。

一個路由應允許選取多個通知和事件種類。 

`CreateEventRoute`是用來新增事件路由的 SDK 呼叫。 以下是其使用方式的範例：

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> 所有 SDK 函數都是以同步和非同步版本提供。

### <a name="event-route-sample-code"></a>事件路由範例程式碼

下列程式碼範例說明如何建立、列出和刪除事件路由：
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>篩選事件

在沒有篩選的情況下，端點會接收來自 Azure 數位 Twins 的各種事件：
* 使用 Azure 數位 Twins 服務 API 的[數位 twins](concepts-twins-graph.md)所引發的遙測
* 對應項屬性變更通知，針對 Azure 數位 Twins 實例中的任何對應項的屬性變更引發
* 建立或刪除 twins 或關聯性時引發的生命週期事件
* 新增或刪除 Azure 數位 Twins 實例[中設定的模型時](concepts-models.md)，所引發的模型變更事件

您可以藉由將篩選準則新增至端點來限制傳送的事件。

若要新增篩選準則，您可以使用 PUT 要求，對*HTTPs：//{YourHost}/EventRoutes/myNewRoute？ api 版本 = 2020-05-31-preview*搭配下列主體。。

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

以下是支援的路由篩選。

| 篩選名稱 | Description | 篩選架構 | 支援的值 | 
| --- | --- | --- | --- |
| 類型 | 流經數位對應項實例的[事件種類](./concepts-route-events.md#types-of-event-messages) | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 來源 | Azure 數位 Twins 實例的名稱 | `"filter" : "source = '<hostname>'"`|  **針對通知**：`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **針對遙測**：`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| 主體 | 上述事件來源內容中事件的描述 | `"filter": " subject = '<subject>'"` | **通知**：主旨為`<twinid>` <br> 或主體的 URI 格式，其可由多個部分或識別碼唯一識別：<br>`<twinid>/relationships/<relationshipid>`<br> **針對遙測**：主體是元件路徑（如果遙測是從對應項元件發出），例如 `comp1.comp2` 。 如果未從元件發出遙測，則其 [主旨] 欄位會是空的。 |
| 資料結構描述 | DTDL 模型識別碼 | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **針對遙測**：資料結構描述是對應項或發出遙測之元件的模型識別碼。 <br>**通知**：不支援資料架構|
| 內容類型 | 資料值的內容類型 | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| 規格版本 | 您所使用之事件架構的版本 | `"filter": "specversion = '<version>'"` | 必須是 `1.0`。 這表示 CloudEvents 架構版本1。0 |
| True/False | 允許建立不含篩選的路由，或停用路由 | `"filter" : "<true/false>"` | `true`= 路由已啟用，沒有篩選 <br> `false`= 路由已停用 |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

您也可以使用下列作業來合併篩選：

| 篩選名稱 | 篩選架構 | 範例 | 
| --- | --- | --- |
| 和/或 | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| 和/或 | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| 嵌套的作業 | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> 在預覽期間，只支援字串相等（=、！ =）。

當您執行或更新篩選時，變更可能需要幾分鐘的時間才會反映在資料管線中。

## <a name="manage-endpoints-and-routes-with-cli"></a>使用 CLI 管理端點和路由

您也可以使用 Azure 數位 Twins CLI 來管理端點和路由。 您可以在[如何：使用 Azure 數位 TWINS CLI](how-to-use-cli.md)中找到這些命令。

## <a name="monitor-event-routes"></a>監視事件路由

您可以在[Azure 入口網站](https://portal.azure.com/)中查看路由計量，例如計數、延遲和失敗率。 

從入口網站首頁中，搜尋您的 Azure 數位 Twins 實例以提取其詳細資料。 從 Azure 數位 Twins 實例的功能表中選取 [**計量**] 選項，以顯示 [*計量*] 頁面。

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="Azure 入口網站中 Azure 數位 Twins 實例的 [計量] 頁面":::

從這裡，您可以查看實例的計量，並建立自訂的視圖。

## <a name="next-steps"></a>後續步驟

閱讀您可以接收的不同事件訊息類型：
* [如何：解讀事件資料](how-to-interpret-event-data.md)
