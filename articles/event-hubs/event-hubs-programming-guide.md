---
title: .NET 程式設計手冊-Azure 事件中樞 (舊版) |Microsoft Docs
description: 本文提供有關如何使用 Azure .NET SDK 為「Azure 事件中樞」撰寫程式碼的資訊。
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f95abe3668bb400d84e354c3bca9eac289c5795
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108682"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>Azure 事件中樞 (舊版 EventHubs 套件的 .NET 程式設計指南) 
本文會討論一些使用 Azure 事件中樞來撰寫程式碼的常見案例。 它假設使用者對事件中樞已有初步了解。 如需事件中樞的概念概觀，請參閱 [事件中樞概觀](./event-hubs-about.md)。

> [!WARNING]
> 本指南適用于 **EventHubs** 套件的舊版本。 建議您 [遷移](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) 程式碼以使用最新的 [EventHubs](event-hubs-dotnet-standard-getstarted-send.md) 套件。  


## <a name="event-publishers"></a>事件發佈者

您可以使用 HTTP POST 或透過 AMQP 1.0 連線，將事件傳送到事件中樞。 使用選擇取決於應用的特定案例。 AMQP 1.0 連線是以服務匯流排中的代理連線形式計量，其較適合經常出現大量訊息且需要低延遲的案例，因為它們可提供持續的傳訊通道。

在使用 .NET 受控 API 時，用於將資料發佈到事件中樞的主要建構是 [EventHubClient][] 和 [EventData][] 類別。 [>eventhubclient][] 會提供將事件傳送到事件中樞的 AMQP 通道。 [EventData][] 類別代表事件，可用來將訊息發佈到事件中樞。 這個類別包含主體、某些中繼資料 (屬性) 和標頭資訊 (SystemProperties 有關事件的) 。 當 [EventData][] 物件通過事件中樞時，系統會為它新增其他屬性。

## <a name="get-started"></a>開始使用
[Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet 套件中會提供支援事件中樞的 .NET 類別。 您可以使用 Visual Studio 方案總管，或 Visual Studio 中的[套件管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)進行安裝。 若要這樣做，請在 [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) 視窗中發出下列命令：

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>建立事件中樞

您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來建立事件中樞。 如需詳細資訊，請參閱[使用 Azure 入口網站來建立事件中樞命名空間和事件中樞](event-hubs-create.md)。

## <a name="create-an-event-hubs-client"></a>建立事件中樞用戶端

與事件中樞互動的主要類別是 [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]。 您可以使用 [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) 方法來具現化這個類別，如以下範例所示：

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>將事件傳送到事件中樞

您可藉由建立 [EventHubClient][] 執行個體並透過 [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) 方法以方同步方式傳送它，將事件傳送到事件中樞。 這個方法會採用單一 [EventData][] 執行個體參數，然後以非同步方式將它傳送到事件中樞。

## <a name="event-serialization"></a>事件序列化

[EventData][] 類別具有[兩個多載建構函式](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor)，它們會採用代表事件資料承載的各種參數 (位元組或位元組陣列)。 在搭配使用 JSON 和 [EventData][]時，您可以使用 **Encoding.UTF8.GetBytes()** 來擷取 JSON 編碼字串的位元組陣列。 例如：

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>分割區索引鍵

> [!NOTE]
> 如果您不熟悉資料分割，請參閱 [這篇文章](event-hubs-features.md#partitions)。 

傳送事件資料時，您可以指定雜湊值，以產生分割區指派。 您可使用 [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) 屬性來指定分割區。 不過，使用分割區的決策暗示可用性與一致性之間的選擇。 

### <a name="availability-considerations"></a>可用性考量

您可以選擇使用資料分割索引鍵，至於是否要使用，請您慎重考慮。 如果您在發佈事件時未指定資料分割索引鍵，系統會使用循環配置資源指派。 在許多情況下，如果事件的順序很重要，您最好選擇使用資料分割索引鍵。 當您使用資料分割索引鍵時，這些資料分割必須在單一節點上可供使用，但經過一段時間後，節點有可能會發生中斷，例如在計算節點重新開機及修補時。 因此，如果您設定了資料分割識別碼，但該資料分割因為某些緣故變得無法使用，您就無法嘗試存取該資料分割中的資料。 如果您最重視的是高可用性，請勿指定資料分割索引鍵，因為在該情況下，事件會使用先前所述的循環配置資源模型傳送到資料分割。 在此案例中，您必須在可用性 (沒有資料分割識別碼) 和一致性 (將事件繫結至資料分割識別碼) 之間做出明確抉擇。

另一項考量是對處理事件的延遲進行處理。 有時候，捨棄資料並重試會比試著跟上處理腳步來得好，因為後者可能會導致下游處理延遲得更久。 例如，拿股票行情即時看板來說，等待完整的最新資料會比較好，但在即時聊天或 VOIP 的案例中，您會寧願先收到資料，即便資料並不完整也沒關係。

有鑑於上述可用性考量，您可能會在這些案例中選擇下列其中一個錯誤處理策略︰

- 停止 (停止讀取事件中樞，直到情況獲得修正)
- 捨棄 (訊息並不重要，將其卸除)
- 重試 (視情況重試訊息)

如需可用性和一致性之間利弊得失的詳細資訊與討論，請參閱[事件中樞的可用性和一致性](event-hubs-availability-and-consistency.md)。 

## <a name="batch-event-send-operations"></a>批次事件傳送作業

分批傳送事件可以協助增加輸送量。 您可使用 [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API 來建立批次，資料物件稍後可針對 [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) 呼叫新增至該批次。

單一批次不能超過每個事件 1 MB 的限制。 此外，批次中的每個訊息都會使用相同的身分識別。 確保批次未超過最大事件大小是傳送者的責任。 如果超過，則會產生「傳送」錯誤。 您可以使用協助程式方法 [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) 以確保批次不超過 1 MB。 您會從 [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API 取得空 [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch)，然後使用 [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) 新增事件來建立批次。 

## <a name="send-asynchronously-and-send-at-scale"></a>以非同步方式傳送和大規模傳送

您可以用非同步方式將事件傳送到事件中樞。 以非同步方式傳送會增加用戶端傳送事件的速率。 [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) 會傳回 [Task](/dotnet/api/system.threading.tasks.task?view=netcore-3.1) 物件。 您可以在用戶端上使用 [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) 類別來控制用戶端重試選項。

## <a name="event-consumers"></a>事件取用者
[EventProcessorHost][] 類別能處理來自事件中樞的資料。 在 .NET 平台上建置事件讀取器時，您應該使用這項實作。 [EventProcessorHost][] 能為事件處理器實作提供安全執行緒、多處理序、安全的執行階段環境，進而提供檢查點和資料分割租用管理。

若要使用 [EventProcessorHost][] 類別，您可以實作 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)。 這個介面包含四個方法：

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

若要啟動事件處理，請具現化 [EventProcessorHost][]，並為您的事件中樞提供適當的參數。 例如：

> [!NOTE]
> EventProcessorHost 和其相關類別是在 **EventHubs. 處理器** 套件中提供。 遵循本文中的指示[，或在](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package)[封裝管理員主控台](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)視窗中發出下列命令，將套件新增至您的 Visual Studio 專案： `Install-Package Microsoft.Azure.EventHubs.Processor` 。

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

然後，呼叫 [>registereventprocessorasync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) 以向執行時間註冊您的 [>ieventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 執行：

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

此時，主機會嘗試使用「窮盡」演算法來取得事件中樞內每個資料分割的租用。 這些租用會延續一段指定時間，然後必須更新。 本案例中，當新節點上線時，背景工作執行個體會保留租用，而每當取得更多租用的嘗試發生時，負載會隨著在節點之間轉移。

![事件處理器主機](./media/event-hubs-programming-guide/IC759863.png)

經過一段時間後，均衡的局面將會出現。 此動態功能可讓您將 CPU 架構自動調整套用至消費者，以便進行向上和向下調整。 由於事件中樞沒有直接的訊息計數概念，因此平均 CPU 使用率通常是測量後端或消費者規模最合適的機制。 如果發佈者發佈的事件數量開始超出消費者的處理能力，消費者上增加的 CPU 可用來引發背景工作執行個體計數自動調整。

[EventProcessorHost][] 類別還能實作以 Azure 儲存體為基礎的檢查點機制。 這項機制能儲存每個磁碟分割的位移，方便各個消費者判斷前一個消費者的最後一個檢查點。 由於資料分割會透過租用在節點之間轉換，因此這是能促進負載移位的同步處理機制。

## <a name="publisher-revocation"></a>發佈者撤銷

除了事件處理器主機的 advanced 執行時間功能，事件中樞服務還會啟用 [發行者撤銷](/rest/api/eventhub/revoke-publisher) ，以防止特定發行者將事件傳送至事件中樞。 當發行者權杖遭到洩露，或軟體更新造成發佈者出現不當行為時，這些功能很有用。 在這些情況下，您可以封鎖發佈者 SAS 權杖中的發佈者身分識別，避免它們發佈事件。

> [!NOTE]
> 目前只有 REST API 支援這項功能 ([發行者撤銷](/rest/api/eventhub/revoke-publisher)) 。


## <a name="next-steps"></a>後續步驟

若要深入了解事件中樞案例，請造訪下列連結：

* [事件中樞 API 概觀](./event-hubs-samples.md)
* [什麼是事件中樞](./event-hubs-about.md)
* [事件中樞的可用性和一致性](event-hubs-availability-and-consistency.md)
* [事件處理器主機 API 參考](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
