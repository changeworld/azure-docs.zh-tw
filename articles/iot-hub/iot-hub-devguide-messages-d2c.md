---
title: 了解 Azure IoT 中樞訊息路由 | Microsoft Docs
description: 開發人員指南 - 如何使用訊息路由來傳送裝置到雲端訊息。 包含關於傳送遙測和非遙測資料的資訊。
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 18a37731171be5894a1481fb35569c9c7cf307f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84790512"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

訊息路由可讓您以自動化、可調整且可靠的方式，將裝置的訊息傳送到雲端服務。 訊息路由可用於： 

* **傳送裝置遙測訊息以及事件** (也就是裝置生命週期事件和裝置對應項變更事件) 到內建端點和自訂端點。 深入瞭解[路由端點](#routing-endpoints)。

* 套用多種查詢**篩選資料再將資料傳送到眾多端點**。 訊息路由可讓您對訊息屬性和訊息本文，以及裝置對應項標記和裝置對應項屬性進行查詢。 深入瞭解如何[在訊息路由中使用查詢](iot-hub-devguide-routing-query-syntax.md)。

IoT 中樞需要這些服務端點的寫入權限，才能將訊息路由傳送至工作。 如果您透過 Azure 入口網站設定您的端點，則會為您新增必要的權限。 請務必設定您的服務，以支援預期的輸送量。 例如，如果您使用事件中樞做為自訂端點，您必須設定該事件中樞的**輸送量單位**，使其可以處理您計畫透過 IoT 中樞訊息路由傳送的事件輸入。 同樣地，使用服務匯流排佇列做為端點時，您必須設定**大小上限**以確保佇列可以保存所有資料輸入，直到取用者輸出為止。 當您第一次設定您的 IoT 解決方案時，您可能需要監視其他端點，然後對實際負載進行必要的調整。

IoT 中樞會針對所有裝置到雲端訊息定義[常見格式](iot-hub-devguide-messages-construct.md)，以在通訊協定之間提供互通性。 如果訊息符合多個指向相同端點的路由，則 IoT 中樞只會將訊息傳遞至該端點一次。 因此，您不需要在您的服務匯流排佇列或主題上設定重複資料刪除。 在分割佇列中，分割區同質性可保證訊息排序。 本教學課程可用來了解如何[設定訊息路由](tutorial-routing.md)。

## <a name="routing-endpoints"></a>路由端點

IoT 中樞都有與事件中樞相容的預設內建端點 (**訊息/事件**)。 您可以將訂用帳戶中的其他服務連結到 IoT 中樞，來建立要路由傳送訊息的目標[自訂端點](iot-hub-devguide-endpoints.md#custom-endpoints)。 

每個訊息都會路由傳送到其符合的路由查詢的所有端點。 換句話說，訊息可以路由傳送至多個端點。


如果您的自訂端點具有防火牆設定，請考慮使用 Microsoft 信任的第一方例外狀況，讓您的 IoT 中樞存取特定端點[Azure 儲存體](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing)、 [Azure 事件中樞](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing)和[Azure 服務匯流排](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing)。 這適用于具有[受控服務識別](./virtual-network-support.md)的 IoT 中樞選取區域。

IoT 中樞目前支援下列端點：

 - 內建端點
 - Azure 儲存體
 - 服務匯流排佇列和服務匯流排主題
 - 事件中樞

### <a name="built-in-endpoint"></a>內建端點

您可以使用標準[事件中樞整合和 SDK](iot-hub-devguide-messages-read-builtin.md) 接收內建端點 (**訊息/事件**) 的裝置到雲端訊息。 建立路由之後，除非建立該端點的路由，否則資料會停止流向內建端點。

### <a name="azure-storage"></a>Azure 儲存體

有兩個儲存體服務 IoT 中樞可以將訊息路由至-- [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)和[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) （ADLS Gen2）帳戶。 Azure Data Lake Storage 帳戶是建置於 blob 儲存體之上的已啟用[階層命名空間](../storage/blobs/data-lake-storage-namespace.md)的儲存體帳戶。 這兩個都會使用 blob 來儲存其儲存體。

IoT 中樞支援以[Apache Avro](https://avro.apache.org/)格式和 JSON 格式將資料寫入 Azure 儲存體。 預設值為 AVRO。 只有在設定 blob 儲存體端點時，才可以設定編碼格式。 無法編輯現有端點的格式。 使用 JSON 編碼時，您必須將 contentType 設定為**application/JSON** ，並在訊息[系統屬性](iot-hub-devguide-routing-query-syntax.md#system-properties)中 contentEncoding 為**utf-8** 。 這兩個值都不區分大小寫。 如果未設定內容編碼，則 IoT 中樞會以基底64編碼格式寫入訊息。 您可以使用 IoT 中樞建立或更新 REST API （特別是[RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties)、Azure 入口網站、 [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)或[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0)）來選取編碼格式。 下圖顯示如何在 Azure 入口網站中選取編碼格式。

![Blob 儲存體端點編碼](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT 中樞批次訊息，並在批次達到特定大小或經過一段特定時間時，將資料寫入儲存體。 「IoT 中樞」預設會採用下列檔案命名慣例： 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

您可以使用任何檔案命名慣例，不過，必須使用所有列出的語彙基元。 如果沒有資料可供寫入，IoT 中樞會寫入空的 Blob。

我們建議您列出 blob 或檔案，然後逐一查看它們，以確保讀取所有 blob 或檔案，而不會對磁碟分割做任何假設。 分割範圍可能會在 [Microsoft 起始的容錯移轉](iot-hub-ha-dr.md#microsoft-initiated-failover)或 IoT 中樞[手動容錯移轉](iot-hub-ha-dr.md#manual-failover)期間變更。 您可以使用[清單 BLOB API](https://docs.microsoft.com/rest/api/storageservices/list-blobs)來列舉檔案清單中的 Blob 或[清單 ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list)清單。 請參閱下列範例做為指導方針。

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

若要建立 Azure Data Lake Gen2 相容的儲存體帳戶，請建立新的 V2 儲存體帳戶，然後在 [ **Advanced** ] 索引標籤上的 [*階層命名空間*] 欄位中選取 [*已啟用*]，如下圖所示：

![選取 Azure Date Lake Gen2 儲存體](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>服務匯流排佇列和服務匯流排主題

做為 IoT 中樞端點的服務匯流排佇列和主題不能啟用 [工作階段]**** 或 [重複偵測]****。 如果已啟用其中一個選項，端點會在 Azure 入口網站中顯示為 [無法連線]****。

### <a name="event-hubs"></a>事件中樞

除了內建事件中樞相容端點之外，您也可以將資料傳送至事件中樞類型的自訂端點。 

## <a name="reading-data-that-has-been-routed"></a>讀取已傳送的資料

您可以遵循此[教學課程](tutorial-routing.md)設定路由。

下列教學課程可用來了解如何從端點讀取訊息。

* 從[內建端點](quickstart-send-telemetry-node.md)讀取

* 從 [Blob 儲存體](../storage/blobs/storage-blob-event-quickstart.md)讀取

* 從[事件中樞](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)讀取

* 從[服務匯流排的佇列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)讀取

* 從[服務匯流排主題](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)讀取


## <a name="fallback-route"></a>後援路由

後援路由會將與任何現有路由查詢條件不符的所有訊息，傳送至與[事件中樞](/azure/event-hubs/)相容的內建事件中樞 (**訊息/事件**)。 如果訊息路由已開啟，您即可啟用後援路由功能。 建立路由之後，除非建立該端點的路由，否則資料會停止流向內建端點。 如果內建端點沒有路由，而且後援路由已啟用，則與路由的任何查詢條件不符的訊息會傳送至內建端點。 此外，如果刪除所有的現有路由，必須啟用後援路由接收內建端點的所有資料。

您可以在 [Azure 入口網站 >訊息路由] 分頁中啟用/停用回退路由。 您也可以對於 [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) 使用 Azure 資源管理員來使用後援路由的自訂端點。

## <a name="non-telemetry-events"></a>非遙測事件

除了裝置遙測之外，訊息路由也可讓您傳送裝置對應項變更事件、裝置生命週期事件，以及數位對應項變更事件（處於公開預覽狀態）。 例如，如果隨同設定為**裝置對應項變更事件**的資料來源建立路由，IoT 中樞會將訊息傳送至包含裝置對應項變更的端點。 同樣地，如果使用設定為**裝置生命週期事件**的資料來源建立路由，IoT 中樞會傳送一則訊息，指出裝置是否已刪除或已建立。 最後，做為[IoT 隨插即用公開預覽](../iot-pnp/overview-iot-plug-and-play.md)的一部分，開發人員可以建立將資料來源設定為數字對應項**變更事件**的路由，並在每次設定或變更數位對應項[屬性](../iot-pnp/iot-plug-and-play-glossary.md)、[數位](../iot-pnp/iot-plug-and-play-glossary.md)對應項已被取代，或基礎裝置對應項發生變更事件時，IoT 中樞傳送訊息。

[IoT 中樞也會與 Azure 事件方格整合](iot-hub-event-grid.md)，以發佈裝置事件，以根據這些事件來支援即時整合和自動化工作流程。 請參閱[訊息路由與事件格線之間的關鍵差異](iot-hub-event-grid-routing-comparison.md)了解何者最適合您的情況。

## <a name="testing-routes"></a>測試路由

您建立新的路由或編輯現有的路由時，應該使用範例訊息測試路由查詢。 您可以測試個別的路由，也可以一次測試所有路由，而且沒有任何訊息在測試期間傳送至端點。 Azure 入口網站、Azure Resource Manager、Azure PowerShell 和 Azure CLI 都可以用來進行測試。 結果有助於識別範例訊息是否符合查詢、訊息是否不符合查詢，或因為範例訊息或查詢語法不正確而無法執行測試。 若要深入了解，請參閱[測試路由](/rest/api/iothub/iothubresource/testroute)和[測試所有路由](/rest/api/iothub/iothubresource/testallroutes)。

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>具有至少一次傳遞的順序保證

IoT 中樞訊息路由保證已排序，而且至少一次將訊息傳遞至端點。 這表示可能會有重複的訊息，而且可以重新傳輸一系列的訊息，以遵循原始訊息排序。 例如，如果原始訊息順序是 [1，2，3，4]，您可能會收到如 [1，2，1，2，3，1，2，3，4] 的訊息順序。 順序保證是，如果您收到訊息 [1]，則一律會接著 [2，3，4]。

若要處理訊息重複專案，我們建議您在訊息的應用程式屬性中，為來源（通常是裝置或模組）戳唯一的識別碼。 耗用訊息的服務可以使用此識別碼來處理重複的訊息。

## <a name="latency"></a>Latency

當您使用內建端點來路由傳送裝置到雲端的遙測訊息時，在建立第一個路由之後，端對端延遲會稍微增加。

在大部分情況下，延遲的平均增加小於500毫秒。 您可以使用**路由：訊息/事件的訊息延遲**或 **d2c.endpoints.latency.builtIn.events** IoT 中樞計量來監視延遲。 建立或刪除第一個之後的任何路由並不會影響端對端延遲。

## <a name="monitoring-and-troubleshooting"></a>監視與疑難排解

IoT 中樞提供數個與路由和端點相關的計量，讓您概述中樞的健全狀況和傳送的訊息。 [IoT 中樞計量](iot-hub-metrics.md)列出對於 IoT 中樞預設啟用的所有計量。 使用 [將診斷記錄**路由**在 Azure 監視器[診斷設定](../iot-hub/iot-hub-monitor-resource-health.md)] 中，您可以追蹤在評估路由查詢時所發生的錯誤，以及 IoT 中樞發現的端點健全狀況。 您可以使用 REST API[取得端點健全狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)來取得端點的[健全狀況狀態](iot-hub-devguide-endpoints.md#custom-endpoints)。 

如需更多詳細資料和支援以進行路由疑難排解，請使用[疑難排解指南來進行路由](troubleshoot-message-routing.md)。

## <a name="next-steps"></a>後續步驟

* 如需了解如何建立訊息路由，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](tutorial-routing.md)。

* [如何傳送裝置到雲端的訊息](quickstart-send-telemetry-node.md)

* 如需您可以用來傳送裝置對雲端訊息之 SDK 的資訊，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。
