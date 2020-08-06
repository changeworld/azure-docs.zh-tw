---
title: '管理端點和路由 (Api 和 CLI) '
titleSuffix: Azure Digital Twins
description: 瞭解如何設定和管理 Azure 數位 Twins 資料的端點和事件路由。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9274e147bcaec4b3e63a6720e369946d64e94628
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809879"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>在 Azure 數位 Twins 中管理端點和路由 (Api 和 CLI) 

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

在 Azure 數位 Twins 中，您可以將[事件通知](how-to-interpret-event-data.md)路由傳送至下游服務或連線的計算資源。 這是藉由先設定可接收事件的**端點**來完成。 接著，您可以建立[**事件路由**](concepts-route-events.md)，以指定 Azure 數位 Twins 所產生的哪些事件會傳遞給哪些端點。

您可以使用[EventRoutes api](how-to-use-apis-sdks.md)、 [.Net (c # ) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)或[Azure 數位 Twins CLI](how-to-use-cli.md)來管理端點和路由。 這篇文章會逐步引導您完成透過這些機制建立端點和路由的流程。

它們也可以透過[Azure 入口網站](https://portal.azure.com)來管理。 如需使用入口網站的文章版本，請參閱[*如何： (入口網站) 管理端點和路由*](how-to-manage-routes-portal.md)。

## <a name="prerequisites"></a>Prerequisites

* 您將需要一個**Azure 帳戶** (您可以在[這裡](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免費設定一個) 
* 您將需要 azure 訂用帳戶中的**Azure 數位 Twins 實例**。 如果您還沒有實例，您可以使用[*如何：設定實例和驗證*](how-to-set-up-instance-scripted.md)中的步驟來建立一個。 請備妥安裝程式中的下列值，以供本文稍後使用：
    - 執行個體名稱
    - 資源群組
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>建立 Azure 數位 Twins 的端點

以下是您可以為您的實例建立的支援端點類型：
* [Event Grid](../event-grid/overview.md)
* [事件中樞](../event-hubs/event-hubs-about.md)
* [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)

如需不同端點類型的詳細資訊，請參閱在[*Azure 訊息服務之間進行選擇*](../event-grid/compare-messaging-services.md)。

若要將端點連結至 Azure 數位 Twins，您所使用的事件方格主題、事件中樞或服務匯流排必須已經存在。 

### <a name="create-an-event-grid-endpoint"></a>建立事件方格端點

下列範例示範如何使用 Azure CLI 建立事件方格型別端點。 您可以使用[Azure Cloud Shell](https://shell.azure.com)，或[在本機安裝 CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

首先，建立事件方格主題。 您可以使用下列命令，或流覽 [事件方格*自訂事件*] 快速入門的[[*建立自訂主題*] 區段](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)，以更詳細地查看步驟。

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

現在，事件方格主題會以引數所指定的名稱，以 Azure 數位 Twins 內的端點形式提供 `--endpoint-name` 。 您通常會使用該名稱作為**事件路由**的目標，您[稍後會在本文中](#event-routes-with-apis-and-the-c-sdk)使用 AZURE 數位 Twins 服務 API 來建立。

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>建立事件中樞或服務匯流排端點

建立事件中樞或服務匯流排端點的程式類似于上面顯示的事件方格進程。

首先，建立您將用來做為端點的資源。 以下是必要的：
* 服務匯流排：_服務匯流排命名空間_，_服務匯流排主題_，_授權規則_
* 事件中樞：_事件中樞命名空間_、_事件中樞_、_授權規則_

然後，使用下列命令在 Azure 數位 Twins 中建立端點： 

* 新增服務匯流排主題端點 (需要預先建立的服務匯流排資源) 
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* 新增事件中樞端點 (需要預先建立的事件中樞資源) 
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="event-routes-with-apis-and-the-c-sdk"></a> (Api 和 c # SDK 的事件路由) 

若要實際將資料從 Azure 數位 Twins 傳送至端點，您必須定義**事件路由**。 Azure 數位 Twins **EventRoutes api**可讓開發人員連接整個系統和下游服務的事件流程。 如需深入瞭解事件路由，請參閱[*概念：路由 Azure 數位 Twins 事件*](concepts-route-events.md)。

本節中的範例會使用 c # SDK。

必要條件 **：您**必須先建立端點，如本文稍早所述，才能繼續建立路由。 當您的端點設定完成後，您可以繼續建立事件路由。

>[!NOTE]
>如果您最近已部署端點，請先確認它們已完成部署，**再**嘗試將它們用於新的事件路由。 如果路由部署因為端點尚未就緒而失敗，請等候幾分鐘，然後再試一次。
>
> 如果您要編寫此流程的腳本，您可能需要在等候時間2-3 分鐘內建立端點服務完成部署，再繼續進行路由設定。

### <a name="create-an-event-route"></a>建立事件路由

事件路由是使用資料平面 Api 所定義。 

路由定義可以包含下列元素：
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

您可以將端點的**篩選準則**新增至事件路由，以限制傳送的事件。

若要新增篩選準則，您可以使用 PUT 要求，對*HTTPs：//{YourHost}/EventRoutes/myNewRoute？ api 版本 = 2020-05-31-preview*搭配下列主體。。

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

以下是支援的路由篩選。 使用 [*篩選文字架構*] 欄位中的詳細資料來取代 `<filter-text>` 上述要求主體中的預留位置。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>使用 CLI 管理端點和路由

您也可以使用 Azure 數位 Twins CLI 來管理端點和路由。 如需有關使用 CLI 的詳細資訊，以及可用的命令，請參閱[*如何：使用 Azure 數位 TWINS CLI*](how-to-use-cli.md)。

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>後續步驟

閱讀您可以接收的不同事件訊息類型：
* [*如何：解讀事件資料*](how-to-interpret-event-data.md)
