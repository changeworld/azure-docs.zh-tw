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
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370452"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>使用 IoT 中心消息路由將設備到雲的消息發送到不同的終結點

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

訊息路由可讓您以自動化、可調整且可靠的方式，將裝置的訊息傳送到雲端服務。 訊息路由可用於： 

* **傳送裝置遙測訊息以及事件** (也就是裝置生命週期事件和裝置對應項變更事件) 到內建端點和自訂端點。 深入了解[路由端點](#routing-endpoints)。

* 套用多種查詢**篩選資料再將資料傳送到眾多端點**。 訊息路由可讓您對訊息屬性和訊息本文，以及裝置對應項標記和裝置對應項屬性進行查詢。 進一步了解使用[訊息路由中的查詢](iot-hub-devguide-routing-query-syntax.md)。

IoT 中樞需要這些服務端點的寫入權限，才能將訊息路由傳送至工作。 如果您透過 Azure 入口網站設定您的端點，則會為您新增必要的權限。 請務必設定您的服務，以支援預期的輸送量。 例如，如果將事件中心用作自訂終結點，則必須配置該事件中心的**輸送量單位**，以便它能夠處理計畫通過 IoT 中心消息路由發送的事件的入口。 同樣，在將服務匯流排佇列用作終結點時，必須配置**最大大小**，以確保佇列可以保留所有資料入侵，直到消費者將其傳出。 當您第一次設定您的 IoT 解決方案時，您可能需要監視其他端點，然後對實際負載進行必要的調整。

IoT 中樞會針對所有裝置到雲端訊息定義[常見格式](iot-hub-devguide-messages-construct.md)，以在通訊協定之間提供互通性。 如果訊息符合多個指向相同端點的路由，則 IoT 中樞只會將訊息傳遞至該端點一次。 因此，您不需要在您的服務匯流排佇列或主題上設定重複資料刪除。 在分割佇列中，分割區同質性可保證訊息排序。 本教學課程可用來了解如何[設定訊息路由](tutorial-routing.md)。

## <a name="routing-endpoints"></a>路由端點

IoT 中樞都有與事件中樞相容的預設內建端點 (**訊息/事件**)。 您可以將訂用帳戶中的其他服務連結到 IoT 中樞，來建立要路由傳送訊息的目標[自訂端點](iot-hub-devguide-endpoints.md#custom-endpoints)。 

每條消息都路由到其路由查詢匹配的所有終結點。 換句話說，消息可以路由到多個終結點。

IoT 中樞目前支援下列服務做為自訂端點︰

### <a name="built-in-endpoint"></a>內建端點

您可以使用標準[事件中樞整合和 SDK](iot-hub-devguide-messages-read-builtin.md) 接收內建端點 (**訊息/事件**) 的裝置到雲端訊息。 創建路由後，資料將停止流向內置終結點，除非將路由創建到該終結點。

### <a name="azure-storage"></a>Azure 儲存體

IoT 中心可以將消息路由到兩個存儲服務 - [Azure Blob 存儲](../storage/blobs/storage-blobs-introduction.md)和 Azure[資料存儲第 2 代](../storage/blobs/data-lake-storage-introduction.md)（ADLS Gen2） 帳戶。 Azure 資料湖存儲帳戶是分層命名[空間](../storage/blobs/data-lake-storage-namespace.md)啟用的存儲帳戶，構建在 blob 存儲之上。 這兩個都使用 blob 進行存儲。

IoT 中心支援以[Apache Avro](https://avro.apache.org/)格式和 JSON 格式將資料寫入 Azure 存儲。 預設值為 AVRO。 編碼格式只能在配置 Blob 存儲終結點時設置。 無法編輯現有終結點的格式。 使用 JSON 編碼時，必須在消息[系統屬性](iot-hub-devguide-routing-query-syntax.md#system-properties)中將內容類型設置為**應用程式/json**和內容編碼到**UTF-8。** 這兩個值都是不區分大小寫的。 如果未設置內容編碼，則 IoT 中心將以基 64 編碼格式寫入消息。 您可以使用 IoT 中心創建或更新 REST API（特別是[路由存儲容器屬性](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties)、Azure 門戶[、Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)或[Azure PowerShell）](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0)選擇編碼格式。 下圖演示如何在 Azure 門戶中選擇編碼格式。

![Blob 存儲終結點編碼](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT 中心在批次處理達到一定大小或經過一定時間時，都會批次處理消息並將資料寫入存儲。 「IoT 中樞」預設會採用下列檔案命名慣例： 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

您可以使用任何檔案命名慣例，不過，必須使用所有列出的語彙基元。 如果沒有資料可供寫入，IoT 中樞會寫入空的 Blob。

我們建議列出 blob 或檔，然後反覆運算它們，以確保讀取所有 Blob 或檔，而無需對分區進行任何假設。 分割範圍可能會在 [Microsoft 起始的容錯移轉](iot-hub-ha-dr.md#microsoft-initiated-failover)或 IoT 中樞[手動容錯移轉](iot-hub-ha-dr.md#manual-failover)期間變更。 您可以使用[清單 Blobs API](https://docs.microsoft.com/rest/api/storageservices/list-blobs)枚舉檔案清單的 Blob 清單或[清單 ADLS Gen2 API。](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) 請參閱以下示例作為指南。

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

> [!NOTE]
> 如果您的存儲帳戶具有限制 IoT 中心連接的防火牆配置，請考慮使用[Microsoft 受信任的第一方異常](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing)（對於具有託管服務標識的 IoT 中心，在選定區域中可用）。

要創建 Azure 資料湖 Gen2 相容的存儲帳戶，請創建新的 V2 存儲帳戶，並在 **"高級**"選項卡上的 *"階層命名空間"* 欄位中選擇*啟用*，如下圖所示：

![選擇 Azure 日期湖第 2 代存儲](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>服務匯流排佇列和服務匯流排主題

做為 IoT 中樞端點的服務匯流排佇列和主題不能啟用 [工作階段]**** 或 [重複偵測]****。 如果已啟用其中一個選項，端點會在 Azure 入口網站中顯示為 [無法連線]****。

> [!NOTE]
> 如果服務匯流排資源具有限制 IoT 中心連接的防火牆配置，請考慮使用[Microsoft 受信任的第一方異常](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing)（對於具有託管服務標識的 IoT 中心，在選定區域中可用）。


### <a name="event-hubs"></a>事件中樞

除了內建事件中樞相容端點之外，您也可以將資料傳送至事件中樞類型的自訂端點。 

> [!NOTE]
> 如果事件中心資源具有限制 IoT 中心連接的防火牆配置，請考慮使用[Microsoft 受信任的第一方異常](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing)（對於具有託管服務標識的 IoT 中心，在選定區域中可用）。


## <a name="reading-data-that-has-been-routed"></a>讀取已傳送的資料

您可以遵循此[教學課程](tutorial-routing.md)設定路由。

下列教學課程可用來了解如何從端點讀取訊息。

* 從[內建端點](quickstart-send-telemetry-node.md)讀取

* 從 [Blob 儲存體](../storage/blobs/storage-blob-event-quickstart.md)讀取

* 從[事件中樞](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)讀取

* 從[服務匯流排佇列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)讀取

* 從[服務匯流排主題](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)讀取


## <a name="fallback-route"></a>後援路由

後援路由會將與任何現有路由查詢條件不符的所有訊息，傳送至與[事件中樞](/azure/event-hubs/)相容的內建事件中樞 (**訊息/事件**)。 如果訊息路由已開啟，您即可啟用後援路由功能。 創建路由後，資料將停止流向內置終結點，除非創建了到該終結點的路由。 如果內建端點沒有路由，而且後援路由已啟用，則與路由的任何查詢條件不符的訊息會傳送至內建端點。 此外，如果刪除所有的現有路由，必須啟用後援路由接收內建端點的所有資料。

您可以在 Azure 門戶>消息路由邊欄選項卡中啟用/禁用回退路由。 您也可以對於 [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) 使用 Azure 資源管理員來使用後援路由的自訂端點。

## <a name="non-telemetry-events"></a>非遙測事件

除了設備遙測之外，消息路由還支援發送設備孿生變更事件、設備生命週期事件和數位孿生變更事件（在公共預覽中）。 例如，如果隨同設定為**裝置對應項變更事件**的資料來源建立路由，IoT 中樞會將訊息傳送至包含裝置對應項變更的端點。 同樣，如果使用資料來源設置為**設備生命週期事件**創建路由，IoT 中心會發送一條消息，指示設備是被刪除還是已創建。 最後，作為[IoT 隨插即用公共預覽](../iot-pnp/overview-iot-plug-and-play.md)的一部分，開發人員可以創建資料來源設置為**數位孿生變更事件的**路由，並且 IoT 中心在設置或更改數位孿生[屬性](../iot-pnp/iot-plug-and-play-glossary.md)、替換[數位孿生](../iot-pnp/iot-plug-and-play-glossary.md)或基礎設備孿生髮生變更事件時發送消息。

[IoT 中心還與 Azure 事件網格集成](iot-hub-event-grid.md)，以發佈設備事件，以支援基於這些事件的即時集成和工作流自動化。 請參閱[訊息路由與事件格線之間的關鍵差異](iot-hub-event-grid-routing-comparison.md)了解何者最適合您的情況。

## <a name="testing-routes"></a>測試路由

您建立新的路由或編輯現有的路由時，應該使用範例訊息測試路由查詢。 您可以測試個別的路由，也可以一次測試所有路由，而且沒有任何訊息在測試期間傳送至端點。 Azure 門戶、Azure 資源管理器、Azure PowerShell 和 Azure CLI 可用於測試。 結果有助於確定示例消息是否與查詢匹配、消息是否與查詢匹配，或者由於示例消息或查詢語法不正確而無法運行測試。 若要深入了解，請參閱[測試路由](/rest/api/iothub/iothubresource/testroute)和[測試所有路由](/rest/api/iothub/iothubresource/testallroutes)。

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>訂購保證，至少一次交貨

IoT 中心消息路由保證已排序，並且至少一次將消息傳遞到端點。 這意味著可以複製消息，並且可以重新傳輸一系列消息，以紀念原始消息排序。 例如，如果原始消息順序為 [1，2，3，4]，則可以收到消息序列，如 [1，2，1，2，3，1，3，3，4]。 訂購保證是，如果您收到消息 [1]，它將始終跟隨 [2，3，4]。

對於處理消息重複，我們建議在源點（通常是設備或模組）的消息的應用程式屬性中標記唯一識別碼。 使用消息的服務可以使用此識別碼處理重複的消息。

## <a name="latency"></a>Latency

當您使用內建端點來路由傳送裝置到雲端的遙測訊息時，在建立第一個路由之後，端對端延遲會稍微增加。

在大多數情況下，延遲的平均增加小於 500 毫秒。 您可以使用**路由：訊息/事件的訊息延遲**或 **d2c.endpoints.latency.builtIn.events** IoT 中樞計量來監視延遲。 建立或刪除第一個之後的任何路由並不會影響端對端延遲。

## <a name="monitoring-and-troubleshooting"></a>監視與疑難排解

IoT 中心提供了與路由和終結點相關的多個指標，讓您大致瞭解中心運行狀況和發送的消息。 您可以結合多個計量的資訊，以識別問題的根本原因。 例如，使用指標**路由：丟棄的遙測消息**或**d2c.遙測.egress.fall，** 以標識在與任何路由上不匹配的查詢和回退路由禁用時丟棄的消息數。 [IoT 中樞計量](iot-hub-metrics.md)列出對於 IoT 中樞預設啟用的所有計量。

您可以使用 REST API[獲取終結點運行狀況](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)來獲取終結點的[運行狀況](iot-hub-devguide-endpoints.md#custom-endpoints)。 我們建議使用與路由消息延遲相關的[IoT 中心指標](iot-hub-metrics.md)來識別和調試終結點運行狀況死或不正常的錯誤。 例如，對於終結點類型事件中心，可以監視**d2c.終結點.延遲.事件中心**。 當 IoT 中心建立了最終一致的運行狀況時，不正常終結點的狀態將更新為正常。

使用 Azure 監視器[診斷設置](../iot-hub/iot-hub-monitor-resource-health.md)中的**路由**診斷日誌，可以跟蹤在評估 IoT 中心認為的路由查詢和終結點運行狀況期間發生的錯誤，例如終結點已死時。 這些診斷日誌可以發送到 Azure 監視器日誌、事件中心或 Azure 存儲進行自訂處理。

## <a name="next-steps"></a>後續步驟

* 如需了解如何建立訊息路由，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](tutorial-routing.md)。

* [如何傳送裝置到雲端的訊息](quickstart-send-telemetry-node.md)

* 如需您可以用來傳送裝置對雲端訊息之 SDK 的資訊，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。
