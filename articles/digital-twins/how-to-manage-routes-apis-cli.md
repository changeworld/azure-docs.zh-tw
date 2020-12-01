---
title: " (Api 和 CLI) 管理端點和路由"
titleSuffix: Azure Digital Twins
description: 瞭解如何設定和管理 Azure 數位 Twins 資料的端點和事件路由。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fc260736a740362db2c19730afc93dd4f3d22c2e
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435398"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>管理 Azure 數位 Twins 中的端點和路由 (Api 和 CLI) 

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

在 Azure 數位 Twins 中，您可以將 [事件通知](how-to-interpret-event-data.md) 路由傳送至下游服務或已連線的計算資源。 這是藉由先設定可接收事件的 **端點** 來完成。 然後，您可以建立  [**事件路由**](concepts-route-events.md) ，以指定 Azure 數位 Twins 所產生的哪些事件會傳遞給哪些端點。

本文將逐步引導您完成使用 [事件路由 api](/rest/api/digital-twins/dataplane/eventroutes)、 [.Net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)和 [Azure 數位 Twins CLI](how-to-use-cli.md)建立端點和路由的流程。

或者，您也可以使用 [Azure 入口網站](https://portal.azure.com)來管理端點和路由。 如需使用入口網站的文章版本，請參閱 [*如何： (入口網站) 管理端點和路由*](how-to-manage-routes-portal.md)。

## <a name="prerequisites"></a>Prerequisites

* 您將需要 **Azure 帳戶** (您可以在 [這裡](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 免費設定一個帳戶) 
* 您將需要 azure 訂用帳戶中的 **Azure 數位 Twins 實例** 。 如果您還沒有實例，可以使用 how [*to：設定實例和驗證*](how-to-set-up-instance-cli.md)中的步驟來建立一個實例。 設定中的下列值可方便用於本文稍後：
    - 執行個體名稱
    - 資源群組
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>建立 Azure 數位 Twins 的端點

以下是您可以為您的實例建立的支援端點類型：
* [Event Grid](../event-grid/overview.md)
* [事件中樞](../event-hubs/event-hubs-about.md)
* [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)

如需不同端點類型的詳細資訊，請參閱在 [*Azure 訊息服務之間選擇*](../event-grid/compare-messaging-services.md)。

若要將端點連結至 Azure 數位 Twins，您要用於端點的事件方格主題、事件中樞或服務匯流排必須已經存在。 

### <a name="create-an-event-grid-endpoint"></a>建立事件方格端點

下列範例示範如何使用 Azure CLI 建立事件方格型別端點。 您可以使用 [Azure Cloud Shell](https://shell.azure.com)，或 [在本機安裝 CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。

首先，建立事件方格主題。 您可以使用下列命令，或是造訪事件方格 *自訂事件* 快速入門的 [[*建立自訂主題*] 區段](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)，更詳細地查看步驟。

```azurecli-interactive
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> 若要輸出可傳遞至 Azure CLI 命令中的 Azure 區域名稱清單，請執行此命令：
> ```azurecli-interactive
> az account list-locations -o table
> ```

建立主題之後，您可以使用下列 [Azure 數位 TWINS CLI 命令](how-to-use-cli.md)將它連結至 Azure 數位 Twins：

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

現在，事件方格主題可作為 Azure 數位 Twins 內部的端點，在以引數指定的名稱下 `--endpoint-name` 。 您通常會使用該名稱做為 **事件路由** 的目標，您 [稍後會在本文中](#create-an-event-route) 使用 AZURE 數位 Twins 服務 API 來建立。

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>建立事件中樞或服務匯流排端點

建立事件中樞或服務匯流排端點的程式類似于上面所示的事件方格處理常式。

首先，建立您將作為端點使用的資源。 以下是所需的內容：
* 服務匯流排： _服務匯流排命名空間_、 _服務匯流排主題_、 _授權規則_
* 事件中樞： _事件中樞命名空間_、 _事件中樞_、 _授權規則_

然後，使用下列命令在 Azure 數位 Twins 中建立端點： 

* 新增服務匯流排主題端點 (需要預先建立的服務匯流排資源) 
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* 新增事件中樞端點 (需要預先建立的事件中樞資源) 
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

### <a name="create-an-endpoint-with-dead-lettering"></a>使用無效信件建立端點

當端點無法在某段時間內或在嘗試傳遞事件一段特定的次數之後傳遞事件時，它可以將未傳遞的事件傳送至儲存體帳戶。 此處理程式稱為無效 **信件。**

若要深入瞭解無效信件，請參閱 [*概念：事件路由*](concepts-route-events.md#dead-letter-events)。

#### <a name="set-up-storage-resources"></a>設定儲存體資源

在設定寄不出的信件位置之前，您必須擁有在 Azure 帳戶中設定[容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)的[儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)。 您稍後會在建立端點時，提供此容器的 URL。
寄不出的信件是以具有 [SAS 權杖](../storage/common/storage-sas-overview.md)的容器 URL 提供。 該權杖只需要 `write` 儲存體帳戶內目的地容器的許可權。 完整格式的 URL 將採用下列格式： `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

請遵循下列步驟，在您的 Azure 帳戶中設定這些儲存體資源，以準備在下一節中設定端點連接。

1. 遵循 [這篇文章](../storage/common/storage-account-create.md?tabs=azure-portal) 來建立儲存體帳戶，並儲存儲存體帳戶名稱，以供稍後使用。
2. 使用 [這篇文章](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) 建立容器，並儲存容器名稱，以便稍後在設定容器與端點之間的連接時使用。
3. 接下來，為您的儲存體帳戶建立 SAS 權杖。 首先，在 [Azure 入口網站](https://ms.portal.azure.com/#home) 中流覽至您的儲存體帳戶， (您可以使用入口網站搜尋列) 來依名稱尋找它。
4. 在 [儲存體帳戶] 頁面中，選擇左側導覽列中的 [ _共用存取_ 簽章] 連結，以選取產生 SAS 權杖的正確許可權。
5. 針對 _允許的服務_ 和 _允許的資源類型_，請選取您想要的設定。 您必須在每個類別中至少選取一個方塊。 針對 [允許的許可權]，選擇 [ **寫入** ] (如果要) ，您也可以選取其他許可權。
請視需要設定其餘設定。
6. 然後，選取 [ _產生 sas 與連接字串_ ] 按鈕以產生 sas 權杖。 這將會在相同頁面底部的設定選項底下產生數個 SAS 和連接字串值。 向下滾動以查看值，並使用 [複製到剪貼簿] 圖示複製 **SAS 權杖** 值。 將其儲存以供稍後使用。

:::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token.png" alt-text="Azure 入口網站中的 [儲存體帳戶] 頁面，其中顯示用來產生 SAS 權杖的所有設定選項。" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token.png":::

:::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="複製要在寄不出的信件秘密中使用的 SAS 權杖。" lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::

#### <a name="configure-the-endpoint"></a>設定端點

死信端點是使用 Azure Resource Manager Api 所建立。 建立端點時，請使用 [Azure Resource Manager api 檔](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) 來填滿必要的要求參數。 此外，將新增 `deadLetterSecret` 至要求 **主體** 中的屬性物件，其中包含適用于儲存體帳戶的容器 URL 和 SAS 權杖。
      
```json
{
  "properties": {
    "endpointType": "EventGrid",
    "TopicEndpoint": "https://contosoGrid.westus2-1.eventgrid.azure.net/api/events",
    "accessKey1": "xxxxxxxxxxx",
    "accessKey2": "xxxxxxxxxxx",
    "deadLetterSecret":"https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>"
  }
}
```
如需有關結構化此要求的詳細資訊，請參閱 Azure 數位 Twins REST API 檔： [端點-DigitalTwinsEndpoint CreateOrUpdate](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)。

### <a name="message-storage-schema"></a>訊息儲存架構

死信訊息將會以下列格式儲存在您的儲存體帳戶中：

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

寄不出的信件訊息將符合原本要傳遞給原始端點的原始事件的架構。

以下是對應項 [建立通知](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications)的寄不出信件訊息範例：

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>建立事件路由

若要實際將資料從 Azure 數位 Twins 傳送至端點，您必須定義 **事件路由**。 Azure 數位 Twins **EventRoutes api** 可讓開發人員連接整個系統和下游服務的事件流程。 深入瞭解事件路由的 [*概念：路由傳送 Azure 數位 Twins 事件*](concepts-route-events.md)。

本章節中的範例會使用 [.net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)。

必要條件 **：您** 必須如本文稍早所述建立端點，才能繼續建立路由。 當您的端點完成設定之後，您可以繼續建立事件路由。

> [!NOTE]
> 如果您最近部署了端點，請先確認它們已完成部署， **再** 嘗試將它們用於新的事件路由。 如果路由部署因為端點未就緒而失敗，請稍候幾分鐘，然後再試一次。
>
> 如果您正在編寫此流程的腳本，您可能會想要在等候結束之前，先在2-3 分鐘內建立端點服務完成部署的等候時間，然後再繼續進行路由設定。

### <a name="creation-code-with-apis-and-the-c-sdk"></a>使用 Api 和 c # SDK 建立程式碼

事件路由是使用 [資料平面 api](how-to-use-apis-sdks.md#overview-data-plane-apis)定義。 

路由定義可以包含下列元素：
* 您要使用的路由名稱
* 您要使用的端點名稱
* 定義要傳送至端點之事件的篩選準則 

如果沒有路由名稱，則不會在 Azure 數位 Twins 外部路由傳送任何訊息。 如果有路由名稱且篩選為 `true` ，則會將所有訊息路由傳送至端點。 如果有已加入的路由名稱和不同的篩選準則，則會根據篩選準則來篩選訊息。

一個路由應該會允許選取多個通知和事件種類。 

`CreateOrReplaceEventRouteAsync` 是用來新增事件路由的 SDK 呼叫。 以下是其使用方式的範例：

```csharp
string eventFilter = "$eventType = 'DigitalTwinTelemetryMessages' or $eventType = 'DigitalTwinLifecycleNotification'";
var er = new DigitalTwinsEventRoute("<your-endpointName>", eventFilter);
await CreateOrReplaceEventRouteAsync(client, "routeName", er);
```
    
> [!TIP]
> 所有 SDK 函式都有同步和非同步版本。

### <a name="event-route-sample-code"></a>事件路由範例程式碼

下列範例方法顯示如何建立、列出和刪除事件路由：
```csharp
private async static Task CreateEventRoute(DigitalTwinsClient client, String routeName, DigitalTwinsEventRoute er)
{
  try
  {
    Console.WriteLine("Create a route: testRoute1");
            
    // Make a filter that passes everything
    er.Filter = "true";
    await client.CreateOrReplaceEventRouteAsync(routeName, er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable<DigitalTwinsEventRoute> result = client.GetEventRoutes();
    foreach (DigitalTwinsEventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointName} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (DigitalTwinsEventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
  }
    catch (RequestFailedException e)
    {
        Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
    }
  }
```

## <a name="filter-events"></a>篩選事件

若未篩選，端點會從 Azure 數位 Twins 接收各種事件：
* 使用 Azure 數位 Twins 服務 API 的 [數位 twins](concepts-twins-graph.md) 所引發的遙測
* 對應項屬性變更通知，會在 Azure 數位 Twins 實例中任何對應項的屬性變更時引發
* 生命週期事件，在 twins 或關聯性建立或刪除時引發

您可以將端點的 **篩選** 新增至事件路由，以限制傳送的事件。

若要加入篩選，您可以使用 PUT 要求至 *HTTPs：//{您的-twins-hostname}/eventRoutes/{event-route-name}？ api-version = 2020-10-31* ，具有下列主體：

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 
以下是支援的路由篩選。 使用 *篩選文字架構* 資料行中的詳細資料來取代 `<filter-text>` 上述要求主體中的預留位置。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>使用 CLI 管理端點和路由

您也可以使用 Azure 數位 Twins CLI 來管理端點和路由。 如需使用 CLI 和可用命令的詳細資訊，請參閱作法 [*：使用 Azure 數位 TWINS CLI*](how-to-use-cli.md)。

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>後續步驟

瞭解您可以接收的不同事件訊息類型：
* [*How to：解讀事件資料*](how-to-interpret-event-data.md)
