---
title: 可用性和一致性 - Azure 事件中樞 | Microsoft Docs
description: 如何使用分割區，以便透過 Azure 事件中樞提供最大數量的可用性和一致性。
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882190"
---
# <a name="availability-and-consistency-in-event-hubs"></a>事件中樞的可用性和一致性
本文提供 Azure 事件中樞所支援的可用性和一致性的相關資訊。 

## <a name="availability"></a>可用性
Azure 事件中樞將個別機器的災難性失敗風險，或甚至是跨資料中心內的多個失敗網域的整個叢集中的整個機架。 它會實行透明的失敗偵測和容錯移轉機制，讓服務繼續在保證的服務層級內運作，而且通常不會在發生這類失敗時明顯中斷。 如果已使用 [可用性區域](../availability-zones/az-overview.md)的啟用選項來建立事件中樞命名空間，則中斷風險會進一步分配至三個實體分隔的設備，而且服務會有足夠的容量保留，以立即處理整個設備的完整、重大損失。 如需詳細資訊，請參閱 [Azure 事件中樞-異地災難](event-hubs-geo-dr.md)復原。

當用戶端應用程式將事件傳送至事件中樞時，事件會自動分散到事件中樞的資料分割之間。 如果某個分割區因為某些原因而無法使用，則會在其餘的分割區之間散發事件。 此行為可讓運作時間達到最長。 如果是需要最長時間的使用案例，則建議使用此模型，而不是將事件傳送到特定的資料分割。 如需詳細資訊，請參閱[分割區](event-hubs-scalability.md#partitions)。

## <a name="consistency"></a>一致性
在某些案例中，事件的順序可能相當重要。 例如，您可能想要讓後端系統在刪除命令之前先處理更新命令。 在此案例中，用戶端應用程式會將事件傳送至特定的資料分割，以便保留順序。 當取用者應用程式從分割區取用這些事件時，會依序讀取它們。 

使用這個組態時，請記住，如果作為您傳送目的地的特定分割區無法使用，您將會收到錯誤回應。 相較之下，如果您沒有單一分割區的親和性，事件中樞服務會將您的事件傳送至下一個可用的磁碟分割。

有一個既可確保排序又可讓運作時間達到最長的可能解決方案，就是在您的事件處理應用程式中彙總事件。 完成此動作最簡單的方式，就是使用自訂序號屬性來為您的事件加上戳記。

在此案例中，生產者用戶端會將事件傳送至事件中樞的其中一個可用分割區，並從您的應用程式設定對應的序號。 這個解決方案會要求您的處理應用程式保留狀態，但會為您的傳送者提供一個更可能可供使用的端點。

## <a name="appendix"></a>附錄

### <a name="net-examples"></a>.NET 範例

#### <a name="send-events-to-a-specific-partition"></a>將事件傳送到特定的分割區
請在事件上設定分割區索引鍵，或使用 `PartitionSender` 物件 (如果您使用的是舊版的 Microsoft Azure 訊息庫) 只將事件傳送到特定的資料分割。 這麼做可確保在從分割區讀取這些事件時，會依序讀取它們。 

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

### <a name="set-a-sequence-number"></a>設定序號
下列範例會使用自訂序號屬性來為您的事件加上戳記。 

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
