---
title: 使用事件處理器主機接收事件 - Azure 事件中樞 | Microsoft Docs
description: 本文將說明 Azure 事件中樞內的事件處理器主機；此主機可簡化檢查點、租用和平行事件讀取的管理。
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/10/2020
ms.author: shvija
ms.openlocfilehash: 485f51e45e342ca28d54d609fd975bef5b204f7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80372236"
---
# <a name="event-processor-host"></a>事件處理器主機
> [!NOTE]
> 本文適用于舊版的 Azure 事件中樞 SDK。 若要瞭解如何將您的程式碼遷移至較新版本的 SDK，請參閱這些遷移指南。 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [JAVA 腳本](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> 此外，請參閱[平衡應用程式多個實例之間的分割區負載](event-processor-balance-partition-load.md)。

Azure 事件中樞是能以低成本串流數百萬個事件的強大遙測擷取服務。 本文將說明如何使用「事件處理器主機」**(EPH) 來取用內嵌事件；事件處理器主機是智慧型取用者代理程式，可簡化檢查點、租用和平行事件讀取器的管理。  

分割取用者概念是針對事件中樞調整大小的關鍵。 與[競爭取用者](https://msdn.microsoft.com/library/dn568101.aspx)模式相比，分割取用者模式可藉由消除爭奪瓶頸，以及加速端對端平行處理原則來具有高度縮放能力。

## <a name="home-security-scenario"></a>住家安全案例

針對範例案例，請設想有一家監視 100,000 戶家庭的住家安全公司。 每隔一分鐘，此公司就會從每個家庭中安裝的各種感應器取得資料，這些感應器包括動作偵測器、門窗開啟感應器、玻璃破壞偵測器等。 該公司會提供網站給住戶，讓他們可近乎即時地監視房子內的活動。

每個感應器會將資料推送至事件中樞。 事件中樞已設定 16 個分割區。 在取用的最終階段，您會需要一種機制來讀取這些事件、合併這些事件 (篩選、彙總等等)，以及將彙總資料備份至儲存體 blob，使其可接著投射至方便使用的網頁。

## <a name="write-the-consumer-application"></a>撰寫取用者應用程式

在分散式環境中設計取用者時，案例必須處理下列需求：

1. **縮放：** 建立多個取用者，而每個取用者都會負責從幾個事件中樞分割區讀取資料。
2. **負載平衡：** 以動態方式增加或減少取用者。 例如，對每個家庭新增感應器類型 (例如一氧化碳偵測器) 時，事件數目就會增加。 在此情況下，操作人員會增加取用者執行個體的數目。 然後，取用者集區可以重新平衡他們擁有的分割區數目，讓新增的取用者共同分攤負載。
3. **在失敗時無縫接續：** 如果取用者 (**取用者 A**) 發生失敗 (例如，裝載取用者的虛擬機器突然損毀)，則其他取用者必須可接手**取用者 A** 負責的分割區並繼續作業。 此外，接續點 (稱為「檢查點」** 或「位移」**) 應剛好位在**取用者 A** 失敗的位置，或在此稍微前面一點的位置。
4. **取用事件：** 在前面三點處理取用者管理的同時，必須有程式碼來取用事件並對其做些有益處的事；例如，彙總並上傳至 blob 儲存體。

您無須為此建置自己的解決方案，事件中樞會透過 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 介面和 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 類別提供這項功能。

## <a name="ieventprocessor-interface"></a>IEventProcessor 介面

首先，取用的應用程式會實作 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 介面，這有四種方法：[OpenAsync、CloseAsync、ProcessErrorAsync 和 ProcessEventsAsnyc](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods)。 此介面包含實際程式碼，可用來取用事件中樞傳送的事件。 下列程式碼會示範簡單的實作：

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

接下來，具現化 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 執行個體。 根據不同的多載，在建構函式中建立 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 執行個體時會使用下列參數：

- **hostName：** 每個取用者執行個體的名稱。 **EventProcessorHost**的每個實例在取用者群組內都必須有此變數的唯一值，因此請不要將此值硬編碼。
- **eventHubPath：** 事件中樞的名稱。
- **consumerGroupName：** 事件中樞會使用 **$Default** 作為預設的取用者群組名稱，但最佳做法是針對特定處理層面來建立取用者群組。
- **eventHubConnectionString：** 事件中樞的連接字串，可從 Azure 入口網站擷取此項目。 此連接字串應有事件中樞上的**接聽**權限。
- **storageConnectionString：** 用於內部資源管理的儲存體帳戶。

最後，取用者會向事件中樞服務註冊 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 執行個體。 使用 EventProcessorHost 的執行個體來註冊事件處理器類別，會開始處理事件。 註冊作業會指示事件中樞服務，以預期取用者應用程式會從服務的分割區取用某些事件，以及在其推送事件以進行取用時，叫用 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 實作程式碼。 


### <a name="example"></a>範例

例如，想像一下有 5 個專門用來取用事件的虛擬機器 (VM)，而每個 VM 中都有簡單的主控台應用程式，用來執行實際的取用工作。 每個主控台應用程式接著會建立一個 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 執行個體，並向事件中樞服務註冊此執行個體。

在此範例案例中，我們假設將 16 個分割區配置給 5 個 **EventProcessorHost** 執行個體。 有些 **EventProcessorHost** 執行個體擁有的分割區可能比其他執行個體多一些。 **EventProcessorHost** 執行個體會為所擁有的每個分割區，各建立一個 `SimpleEventProcessor` 類別的執行個體。 因此，整體來說會有 16 個 `SimpleEventProcessor` 執行個體，每個分割區各獲派一個。

下列清單是此範例的摘要：

- 16 個事件中樞分割區。
- 5 個 VM，每個 VM 中有 1 個取用者應用程式 (例如 Consumer.exe)。
- 5 個由 Consumer.exe 註冊的 EPH 執行個體，每個 VM 中各 1 個。
- 5 個 EPH 執行個體建立的 16 個 `SimpleEventProcessor` 物件。
- 1 個 Consumer.exe 應用程式可包含 4 個 `SimpleEventProcessor` 物件，因為 1 個 EPH 執行個體可擁有 4 個分割區。

## <a name="partition-ownership-tracking"></a>分割區的擁有權追蹤

EPH 執行個體 (或取用者) 的分割區擁有權可透過 Azure 儲存體帳戶來追蹤，此帳戶就是為了追蹤而提供。 您可以將追蹤視覺化為簡單的資料表，如下所示。 您可以藉由在提供的儲存體帳戶下檢查 blob，來查看實際的實作：

| **取用者群組名稱** | **資料分割識別碼** | **主機名稱 (擁有者)** | **取得租用 (或擁有權) 的時間** | **分割區中的位移 (檢查點)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Consumer\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Consumer\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Consumer\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Consumer\_VM3 | 2018-04-15T01:22:56 | 976 |

在這裡，每一部主機都會在特定時間 (租用期間) 內擁有分割區的擁有權。 如果主機失敗 (VM 關機)，則租用就會到期。 其他主機會嘗試取得此分割區的擁有權，而其中一部主機會成功。 此程序會對具有新擁有者的分割區重設租用。 如此一來，每一次就只有一個讀取器可讀取取用者群組內的任何指定分割區。

## <a name="receive-messages"></a>接收訊息

對 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 執行的每次呼叫都會提供事件集合。 您必須負責處理這些事件。 如果您想要確定處理器主機會處理每個訊息至少一次，則必須自行撰寫持續重試程式碼。 但請留意有害訊息。

建議您快速完成事情；也就是處理的作業愈少愈好。 若不是，請使用取用者群組。 如果您需要寫入儲存體並進行某些路由，最好使用兩個取用者群組，並分別執行兩個[IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)的執行。

在處理期間的某個時間點，您可能會想追蹤已讀取並完成的事項。 如果您必須重新啟動讀取作業，追蹤就很重要，這可讓您不用回到串流的開頭。 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 會使用「檢查點」** 來簡化此追蹤。 檢查點是指定取用者群組中指定分割區的一個位置或位移，而且您確信您已處理該點上的訊息。 在 **EventProcessorHost** 中標記檢查點會透過 [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) 物件上的 [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) 方法來完成。 這項作業通常會在 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 方法內完成，但也可以在 [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) 中完成。

## <a name="checkpointing"></a>檢查點

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) 方法有兩個多載：第一個沒有任何參數，會在 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 傳回的集合內，對最高的事件位移設立檢查點。 此位移是「高水位」標記；它會假設您在呼叫此方法時已處理了所有最近事件。 如果您是以此情況來使用此方法，請注意，您應在其他事件處理程式碼傳回後再呼叫它。 第二個多載可讓您對檢查點指定 [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) 執行個體。 此方法可讓您對檢查點使用不同類型的水位線。 透過此水位線，您可以實作「低水位」標記：您確定已處理的最低順序事件。 此多載可使位移管理具有彈性。

執行檢查點時，具有分割區專屬資訊 (特別是位移) 的 JSON 檔案會寫入建構函式中提供給 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 的儲存體帳戶。 檔案會持續更新。 請務必從各方面考慮檢查點的設立，不建議您在每個訊息上設立檢查點。 用於檢查點的儲存體帳戶可能不會處理此負載，但更重要的是，透過檢查點來檢查每一個事件即表明這是排入佇列的訊息模式，因此，比起事件中樞，服務匯流排佇列可能是較好的選擇。 事件中樞背後的構想是，在大型規模中您能取得「至少一次」的傳遞。 藉由讓您的下游系統具有等冪性，即可輕鬆地從失敗中復原，或以接收多次的相同事件重新啟動該結果。

## <a name="thread-safety-and-processor-instances"></a>執行緒安全性和處理器執行個體

根據預設，[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 為安全執行緒，並且會以同步方式進行與 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 執行個體相關的動作。 當事件抵達分割區時，**IEventProcessor** 執行個體上會針對該分割區呼叫 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)，並且針對此分割區封鎖對 **ProcessEventsAsync** 的進一步呼叫。 後續訊息和對 **ProcessEventsAsync** 的呼叫會在幕後排入佇列，因為在背景中，訊息幫浦會繼續在其他執行緒上執行。 此執行緒安全性不需要安全執行緒集合，並可大幅提升效能。

## <a name="shut-down-gracefully"></a>正常關機

最後，[EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) 會對所有分割區讀取器啟用正常關機，而且應一律在關閉 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 執行個體時呼叫此方法。 若沒有這樣做，可能會造成啟動 **EventProcessorHost** 的其他執行個體時發生延遲，因為租用到期和 Epoch 衝突。 本文的[epoch](#epoch)一節會詳細討論 epoch 管理。 

## <a name="lease-management"></a>租用管理
使用 EventProcessorHost 的執行個體來註冊事件處理器類別，會開始處理事件。 主機執行個體會在事件中樞的某些分割區取得租用，而且可能從其他主機執行個體抓取一些，最終在所有主機執行個體的分割區平均分佈。 對於每個租用的分割區，主機執行個體會依提供的事件處理器類別各建立一個執行個體，然後從該分割區中接收事件，並將其傳遞到事件處理器執行個體。 隨著更多執行個體的新增及更多租用的抓取，EventProcessorHost 最終會在所有取用者之間平衡負載。

如先前所述，追蹤資料表可大幅簡化 [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) 自動縮放的本質。 當 **EventProcessorHost** 執行個體啟動時，它會盡可能取得多個租用，並開始讀取事件。 當租用即將到期時，**EventProcessorHost** 會嘗試透過放置保留來更新租用。 如果租用可更新，處理器就會繼續讀取，但如果不能更新，讀取器就會關閉並呼叫 [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync)。 **CloseAsync** 是執行該分割區所有最後清除作業的好時機。

**EventProcessorHost** 包含 [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) 屬性。 此屬性可讓您控制租用管理。 請在註冊 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 實作前，設定這些選項。

## <a name="control-event-processor-host-options"></a>控制事件處理器主機選項

此外，[RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) 的一個多載會採用 [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) 物件作為參數。 使用此參數來控制 [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) 本身的行為。 [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) 會定義四個屬性與一個事件：

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize)：您想要在 [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 引動過程中收到的集合大小上限。 這個大小沒有最小值，只有最大的大小。 如果會收到較少的訊息，**ProcessEventsAsync** 會執行可用的數量。
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount)：基礎 AMQP 通道使用的值，用來決定用戶端應收到多少訊息的上限。 此值應大於或等於 [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize)。
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout)：如果此參數為**true**，則在分割區上接收事件的基礎呼叫時，會呼叫[ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) 。這個方法適用于在非作用中的資料分割期間，採取以時間為基礎的動作。
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider)：可設定函式指標或 lambda 運算式，對其進行呼叫即可在讀取器開始讀取分割區時提供初始位移。 如果沒有指定此位移，讀取器會從最舊的事件開始，除非提供給 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 建構函式的儲存體帳戶中已儲存具有位移的 JSON 檔案。 當您想變更讀取器啟動的行為時，此方法十分實用。 叫用此方法時，物件參數會包含正在啟動讀取器的分割區識別碼。
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs)：可讓您接收 [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) 中發生的任何基礎例外狀況通知。 如果事情無法如預期般進行，此事件就是開始查看的適當位置。

## <a name="epoch"></a>Epoch

以下是 receive epoch 的運作方式：

### <a name="with-epoch"></a>使用 Epoch
Epoch 是服務所使用的唯一識別碼（epoch 值），用以強制執行分割/租用擁有權。 您可以使用[CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet)方法來建立 Epoch 型接收者。 這個方法會建立以 Epoch 為基礎的接收者。 會針對指定取用者群組中的特定事件中樞分割區建立接收器。

Epoch 功能可讓使用者在任何時間點，確保取用者群組上只有一個接收者，並具有下列規則：

- 如果取用者群組上沒有任何現有的接收者，則使用者可以建立具有任何 epoch 值的接收者。
- 如果有一個具有 epoch 值 e1 的接收者，而且建立了一個具有 epoch 值 e2 的新接收者，其中 e1 <= e2，則具有 e1 的接收者將會自動中斷連線，並成功建立具有 e2 的接收者。
- 如果有一個具有 epoch 值 e1 的接收器，而且建立了一個具有 epoch 值 e2 的新接收者，其中 e1 > e2，則建立 e2 的會失敗並出現錯誤：具有 epoch e1 的接收者已經存在。

### <a name="no-epoch"></a>無 Epoch
您可以使用[CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet)方法來建立非 Epoch 的接收者。 

在某些情況下，串流處理中的使用者會想要在單一取用者群組上建立多個接收者。 為了支援這類案例，我們確實能夠建立不含 epoch 的接收者，在此情況下，我們允許取用者群組最多5個並行接收者。

### <a name="mixed-mode"></a>混合模式
我們不建議您在使用 epoch 建立接收器的情況下使用應用程式，然後在同一個取用者群組上切換為 [無 epoch] 或 [反之亦然]。 不過，當此行為發生時，服務會使用下列規則來處理它：

- 如果已經使用 epoch e1 建立接收器，並主動接收事件，而且建立了新的接收者，但沒有 epoch，則建立新的接收者將會失敗。 Epoch 接收器一律會優先于系統中。
- 如果已經使用 epoch e1 建立接收器，並中斷連線，而且在新的 MessagingFactory 上建立了新的接收者，而沒有 epoch，則建立新的接收者將會成功。 這裡有一點要注意的是，我們的系統會在 ~ 10 分鐘後偵測到「接收者中斷連線」。
- 如果建立的一或多個接收者沒有 epoch，而且使用 epoch e1 建立了新的接收者，則所有舊的接收者都會中斷連線。


> [!NOTE]
> 對於使用 epoch 的應用程式，建議使用不同的取用者群組，並針對不使用 epoch 來避免錯誤。 


## <a name="next-steps"></a>後續步驟

現在您已熟悉事件處理器主機，請參閱下列文章以深入了解事件中樞：

- 開始使用事件中心
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [事件中樞程式設計指南](event-hubs-programming-guide.md)
* [事件中樞的可用性和一致性](event-hubs-availability-and-consistency.md)
* [事件中樞常見問題集](event-hubs-faq.md)
* [GitHub 上的事件中樞範例](https://github.com/Azure/azure-event-hubs/tree/master/samples)
