---
title: 事件複寫工作模式-Azure 事件中樞 |Microsoft Docs
description: 本文提供執行特定事件複寫工作模式的詳細指引
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 370c0f2d5c5c591668aa2dadf0512760a4a9b2f5
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663603"
---
# <a name="event-replication-tasks-patterns"></a>事件複寫工作模式

[同盟總覽](event-hubs-federation-overview.md)和複寫器函式[概述](event-hubs-federation-replicator-functions.md)將說明和複寫工作的基本要素，並建議您先熟悉這些專案，再繼續閱讀本文。

在本文中，我們會詳細說明 [總覽] 區段中已醒目提示之數個模式的執行指南。

## <a name="replication"></a>複寫

複寫模式會將事件從某個事件中樞複製到下一個事件中樞，或從事件中樞複製到其他目的地，例如服務匯流排佇列。 這些事件會轉送，而不會對事件承載進行任何修改。

事件 [中樞之間的事件](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) 複寫以及 [事件中樞與服務匯流排範例之間的事件](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) 複寫涵蓋了此模式的實作為。

### <a name="streams-and-order-preservation"></a>資料流程和順序保留

複寫（透過 Azure Functions 或 Azure 串流分析）不會確保將來源事件中樞的精確1:1 複製品建立至目標事件中樞，但會著重于保留應用程式所需之事件的相對順序。 應用程式會使用相同的分割區索引鍵來群組相關事件來進行通訊，而 [事件中樞則會在相同的資料分割中依序排列具有相同分割索引鍵的訊息](event-hubs-features.md#partitions)。

> [!IMPORTANT] 
> 「位移」資訊對每個事件中樞而言都是唯一的，而且相同事件的位移會在事件中樞實例之間有所不同。 若要找出複製的事件資料流程中的位置，請使用以時間為基礎的位移，並參考 [已傳播服務指派的中繼資料](#service-assigned-metadata)。
>
> 以時間為基礎的位移會在特定時間點開始您的接收者：
> - *EventPosition. FromStart ( # B1* -再次讀取所有保留的資料。
> - *EventPosition. FromEnd ( # B1* -讀取連接時的所有新資料。
> - *EventPosition. FromEnqueuedTime (dateTime)* -從指定的日期和時間開始的所有資料。
>
> 在 EventProcessor 中，您可以透過 EventProcessorOptions 上的 InitialOffsetProvider 設定位置。 使用其他接收者 Api 時，此位置會透過此函式傳遞。 


以 Azure Functions 為基礎的指南中所 [提供的範例形式提供](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) 的預先建立複寫函數協助程式，可確保將具有相同分割區索引鍵的事件串流，以原始資料流程和相同分割區索引鍵的批次提交到目標事件中樞。

如果來源和目標事件中樞的分割區計數相同，則目標中的所有資料流程都會對應到與來源相同的資料分割。
如果分割區計數不同，這對下列內容中所述的一些進一步模式很重要，則對應會不同，但資料流程一律會保持在一起並依序保持在一起。

在目標分割區中，屬於不同資料流程或獨立事件（沒有分割區索引鍵）之事件的相對順序，可能永遠與來源分割區不同。

### <a name="service-assigned-metadata"></a>服務指派的中繼資料

從來源事件中樞取得的事件之服務指派的中繼資料、原始的排入佇列時間、序號和位移，都會由目標事件中樞內新的服務指派值取代，但是在我們的範例中 [提供的協助](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)程式函式、原始值會保留在使用者屬性中： `repl-enqueue-time` (ISO8601 字串) 、 `repl-sequence` ， `repl-offset` 。

這些屬性的類型為字串，而且包含各自原始屬性的 stringified 值。 如果多次轉送事件，則會將即時來源的服務指派中繼資料附加至已存在的屬性，並以分號分隔值。

### <a name="failover"></a>容錯移轉

如果您使用複寫來進行嚴重損壞修復，以防止事件中樞服務中的區域可用性事件或網路中斷，任何這類失敗案例都需要執行從某個事件中樞到下一個事件中樞的容錯移轉，告知生產者和/或取用者使用次要端點。

在所有容錯移轉案例中，假設命名空間的必要元素都是結構相同的，這表示事件中樞和取用者群組的名稱相同，而且共用存取簽章規則和/或角色型存取控制規則會以相同方式設定。 您可以遵循 [移動命名空間的指引](move-across-regions.md) ，並省略清除步驟，來建立 (，並更新) 次要命名空間。

若要強制生產者和取用者切換，您必須將有關要使用哪個命名空間的資訊提供給可在容易存取及更新的位置中進行查閱。 如果生產者或取用者遇到頻繁或持續性的錯誤，則應查閱該位置並調整其設定。 有許多方式可共用該設定，但我們在下列兩個方法中指出： DNS 和檔案共用。

#### <a name="dns-based-failover-configuration"></a>以 DNS 為基礎的容錯移轉設定

其中一個候選方法是在您所控制的 DNS 中保存 DNS SRV 記錄中的資訊，並指向各自的事件中樞端點。 

> [!IMPORTANT] 
> 請注意，事件中樞不允許其端點直接以 CNAME 記錄為別名，這表示您將使用 DNS 作為端點位址的復原查閱機制，而不是直接解析 IP 位址資訊。

假設您擁有網域， `example.com` 以及應用程式的區域 `test.example.com` 。 針對兩個替代事件中樞，您現在會在每個區域中建立兩個額外的嵌套區域和 SRV 記錄。

SRV 記錄是遵循一般慣例， `_azure_eventhubs._amqp` 並保留兩個端點記錄：一個用於埠5671上的 AMQP OVER TLS，另一個用於埠443上的 AMQP Over websocket，兩者都會指向對應至該區域之命名空間的事件中樞端點。

| 區域                   | SRV 記錄                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

在您的應用程式區域中，您會建立一個 CNAME 專案，指向對應至主要事件中樞的從屬區域：

| CNAME 記錄                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

使用 DNS 用戶端，以明確地 (JAVA 和 .NET 的內建用戶端明確地查詢 CNAME 和 SRV 記錄，只允許對 IP 位址的名稱進行簡單的解析) ，然後您就可以解析所需的端點。 例如，使用 [DnsClient.NET](https://dnsclient.michaco.net/)時，lookup 函數為：

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

此函式會傳回針對目前以 CNAME 為別名之區域的埠5671所註冊的目標主機名稱，如上所示。

執行容錯移轉需要編輯 CNAME 記錄，並將其指向替代區域。

使用 DNS （尤其是 [Azure DNS](../dns/dns-overview.md)）的優點是，Azure DNS 資訊會進行全域複寫，因此可在單一區域中斷時復原。

此程式類似于 [事件中樞異地 DR](event-hubs-geo-dr.md) 的運作方式，但完全在您自己的控制之下，也適用于主動/主動案例。

#### <a name="file-share-based-failover-configuration"></a>以檔案共用為基礎的容錯移轉設定

使用 DNS 來共用端點資訊的最簡單方式，就是將主要端點的名稱放入純文字檔案中，並從穩固的基礎結構提供檔案，以防止中斷並仍允許更新。

如果您已經使用全球可用性和內容複寫來執行高可用性的網站基礎結構，請在該處新增這類檔案，並在需要切換時重新發佈檔案。

> [!CAUTION]
> 您應該只以這種方式發佈端點名稱，而不是包含秘密的完整連接字串。

#### <a name="extra-considerations-for-failing-over-consumers"></a>容錯移轉取用者的額外考慮

針對事件中樞取用者，容錯移轉策略的進一步考慮取決於事件處理器的需求。

如果有嚴重損壞需要重建系統（包括資料庫、從備份資料中），而且直接從事件中樞所持有的事件中，或透過中繼處理來處理資料庫，則您將會還原備份，然後從原始系統損毀的時間開始，開始將事件重新執行至系統。

如果失敗只會影響系統的配量，或只有單一事件中樞已變成無法連線，您可能會想要從處理中斷的相同位置繼續處理事件。

若要瞭解這兩個案例，並使用個別 Azure SDK 的事件處理器， [您將建立新的檢查點存放區](event-processor-balance-partition-load.md#checkpointing) ，並根據您要從中繼續處理的 _時間戳記_ ，提供初始分割區位置。

如果您仍可存取您正在切換的事件中樞的檢查點存放區，上述傳播的 [中繼資料](#service-assigned-metadata) 將可協助您略過已處理的事件，並從您上次離開的地方精確繼續。

## <a name="merge"></a>合併

合併模式有一或多個指向一個目標的複寫工作，可能與一般產生者同時傳送事件至相同的目標。

這些模式的變化如下：

- 有兩個或多個複寫函數可同時從不同的來源取得事件，並將事件傳送至相同的目標。
- 另一個複寫函式會從來源取得事件，同時也會由生產者直接使用目標。
- 先前的模式（但在兩個或多個事件中樞之間鏡像）會導致這些事件中樞包含相同的資料流程，而不論事件產生的位置為何。

前兩個模式的變化很簡單，而且不會與一般複寫工作不同。

最後一個案例需要排除已複寫的事件，而不會再次複寫。 [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/main/code/EventHubToEventHubMerge)範例會示範並說明這項技術。

## <a name="editor"></a>編輯器

編輯器模式是以 [複寫模式為](#replication) 基礎，但在轉送訊息之前會先修改訊息。 

這類修改的範例包括：

- **_轉碼_** -如果事件內容 (也稱為「主體」或「承載」 ) 從使用 _Apache Avro_ 格式或某些專屬序列化格式進行編碼的來源，但是，如果系統擁有的目標是要以 _json_ 編碼內容，轉碼複寫工作會先將 _Apache Avro_ 的承載還原序列化為記憶體中的物件圖形，然後再將該圖形序列化為所轉送事件的 _JSON_ 格式。 轉碼也包含 **內容壓縮** 和解壓縮工作。
- **_轉換_** -包含結構化資料的事件可能需要重新安排該資料，以方便下游取用者取用。 這可能涉及像是簡維化的嵌套結構、剪除無關的資料元素，或將裝載調整為完全符合指定的架構。
- **_批次處理_** -事件可能會以批次方式接收 (單一傳送) 中的多個事件，但必須單獨轉送到目標，反之亦然。 因此，工作可能會根據單一輸入事件傳輸來轉送多個事件，或匯總一組接著一起傳送的事件。
- **_驗證_** -來自外部來源的事件資料通常需要檢查它們是否符合一組規則，然後才可加以轉送。 這些規則可以使用架構或程式碼來表示。 找不到合規性的事件可能會遭到捨棄、記錄中注明的問題，或可轉送到特殊的目標目的地，以供進一步處理。
- **_擴充_** -來自某些來源的事件資料可能需要擴充和進一步的內容，才能在目標系統中使用。 這可能牽涉到查閱參考資料，並使用事件來內嵌該資料，或加入複寫工作已知的來源相關資訊，但不包含在事件中。
- **_篩選_** -來自來源的某些事件可能必須根據某些規則從目標中進行清除。 篩選準則會根據規則來測試事件，並在事件不符合規則時卸載事件。 藉由觀察特定準則並卸載具有相同值的後續事件，來篩選出重複的事件是篩選的形式。
- **_密碼_** 編譯-複寫工作可能必須解密來自來源的內容，以及/或加密轉送到目標的內容，以及/或可能必須驗證內容和中繼資料的完整性（相對於事件中所附的簽章），或附加這類簽章。
- **_證明_** -複寫工作可將中繼資料（可能受數位簽章保護）附加至證明事件已透過特定通道或特定時間接收的事件。
- **_連結_** -複寫工作可能會將簽章套用至事件的資料流程，讓資料流程的完整性受到保護，而且可以偵測到遺失的事件。

轉換、批次處理和擴充模式通常最適合使用 [Azure 串流分析](../stream-analytics/stream-analytics-introduction.md) 作業來執行。 

您可以使用 Azure Functions，使用 [事件中樞觸發](../azure-functions/functions-bindings-event-hubs-trigger.md) 程式來取得事件和 [事件中樞輸出](../azure-functions/functions-bindings-event-hubs-output.md) 系結來傳遞這些模式，以執行所有這些模式。

## <a name="routing"></a>路由

路由 [模式是以複寫模式為](#replication) 基礎，但不是具有一個來源和一個目標，而是在 c # 中說明了多個目標，如下所示：

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

路由函式會考慮訊息中繼資料和/或訊息內容，然後挑選其中一個可用目的地來傳送至。

在 Azure 串流分析中，您可以透過定義多個輸出，然後針對每個輸出執行查詢來達到相同的目的。

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>記錄投射

記錄投射模式會將事件資料流程壓平合併到索引資料庫，並將事件變成資料庫中的記錄。 一般情況下，事件會新增至相同的集合或資料表，而事件中樞分割區索引鍵會成為主要索引鍵的合作物件，以尋找讓記錄變成唯一的。

記錄投射可以產生事件資料的時間序列 historian 或壓縮的視圖，因此每個分割區索引鍵只會保留最新的事件。 目標資料庫的圖形最終會由您和您的應用程式需求所組成。 此模式也稱為「事件來源」。

> [!TIP]
> 您可以輕鬆地在 Azure 串流分析中建立記錄投影至 [Azure SQL Database](../stream-analytics/sql-database-output.md) 和 [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) ，而且您應該偏好使用該選項。

下列 Azure 函式會將已壓縮到 Azure CosmosDB 集合的事件中樞內容投射在一起。

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

- [Azure Functions 中的事件複製器應用程式][1]
- [在事件中樞之間複寫事件][2]
- [將事件複寫至 Azure 服務匯流排][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
