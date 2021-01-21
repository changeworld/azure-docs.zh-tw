---
title: 可用性和一致性 - Azure 事件中樞 | Microsoft Docs
description: 如何使用分割區，以便透過 Azure 事件中樞提供最大數量的可用性和一致性。
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b97d76f29ee8b7e44373c865baa09ba5ea4dd23
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631914"
---
# <a name="availability-and-consistency-in-event-hubs"></a>事件中樞的可用性和一致性

## <a name="overview"></a>概觀
「Azure 事件中樞」會使用[資料分割模型](event-hubs-scalability.md#partitions)，來提升單一事件中樞內的可用性與平行處理。 例如，如果事件中樞有四個分割區，而其中一個分割區在負載平衡作業中從一部伺服器移到另一部伺服器，則您仍可從其他三個分割區進行傳送及接收。 此外，擁有更多分割區可讓您使用更多並行讀取器來處理資料，進而改善您的彙總輸送量。 了解分散式系統中分割和排序的含意是解決方案設計的重要層面。

若要協助說明訂購和可用性之間的取捨，請參閱 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)，也稱為 Brewer 的定理。 這個理論討論一致性、可用性及分割區容錯之間的選擇。 它指出對於由網路分割的系統，一定會在一致性和可用性之間有所取捨。

Brewer 的理論會定義一致性和可用性，如下所示：
* 分割區容錯：即使發生分割區失敗，資料處理系統還是能夠繼續處理資料。
* 可用性：非失敗節點會在合理的時間 (不含錯誤或逾時) 內傳回合理的回應。
* 一致性：保證讀取會傳回指定用戶端的最新寫入。

## <a name="partition-tolerance"></a>分割區容錯
「事件中樞」是建置在已分割的資料模型上。 您可以在安裝時設定事件中樞中的分割區數目，但之後即無法變更此值。 由於您必須將分割區與事件中樞搭配使用，因此必須決定應用程式相關的可用性和一致性。

## <a name="availability"></a>可用性
開始使用事件中樞的最簡單方式是使用預設行為。 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[EventHubs (5.0.0 或更新版本) ](#tab/latest)
如果您建立新的 **[EventHubProducerClient](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient)** 物件並使用 **[SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync)** 方法，則會在事件中樞的分割區之間自動散發您的事件。 此行為可讓運作時間達到最長。

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[EventHubs (4.1.0 或更早的版本) ](#tab/old)
如果您建立新的 **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** 物件並使用 **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** 方法，系統就會在事件中樞的分割區之間自動分配事件。 此行為可讓運作時間達到最長。

---

針對需要最長運作時間的使用案例，建議使用此模型。

## <a name="consistency"></a>一致性
在某些案例中，事件的順序可能相當重要。 例如，您可能想要讓後端系統在刪除命令之前先處理更新命令。 在此情況下，您可以在事件上設定分割區索引鍵，或使用 `PartitionSender` 物件 (如果您使用的是舊版的 Microsoft Azure 訊息程式庫) 只將事件傳送到特定的分割區。 這麼做可確保在從分割區讀取這些事件時，會依序讀取它們。 

如果您使用較新的 **EventHubs** 程式庫，請參閱將 [程式碼從 PartitionSender 遷移至 EventHubProducerClient，以將事件發佈至分割](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition)區。

#### <a name="azuremessagingeventhubs-500-or-later"></a>[EventHubs (5.0.0 或更新版本) ](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[EventHubs (4.1.0 或更早的版本) ](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

使用這個組態時，請記住，如果作為您傳送目的地的特定分割區無法使用，您將會收到錯誤回應。 相較之下，如果您不傾向使用單一分割區，「事件中樞」服務就會將事件傳送至下一個可用的分割區。

有一個既可確保排序又可讓運作時間達到最長的可能解決方案，就是在您的事件處理應用程式中彙總事件。 達到此目的的最簡單方式，就是為您的事件標上自訂序號屬性戳記。 下列程式碼顯示一個範例：

#### <a name="azuremessagingeventhubs-500-or-later"></a>[EventHubs (5.0.0 或更新版本) ](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[EventHubs (4.1.0 或更早的版本) ](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

這個範例會將事件傳送到事件中樞的其中一個可用分割區，然後從您的應用程式設定對應的序號。 這個解決方案會要求您的處理應用程式保留狀態，但會為您的傳送者提供一個更可能可供使用的端點。

## <a name="next-steps"></a>後續步驟
您可以造訪下列連結以深入了解事件中樞︰

* [事件中樞服務概觀](./event-hubs-about.md)
* [建立事件中樞](event-hubs-create.md)
