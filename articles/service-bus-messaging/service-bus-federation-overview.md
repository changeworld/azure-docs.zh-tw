---
title: 訊息複寫和跨區域同盟-Azure 服務匯流排 |Microsoft Docs
description: 本文提供事件複寫和與 Azure 服務匯流排的跨區域同盟的總覽。
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d5153820feab8b7901356838ec435d992aa82116
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803267"
---
# <a name="message-replication-and-cross-region-federation"></a>訊息複寫和跨區域同盟

在命名空間中，Azure 服務匯流排支援 [使用自動轉寄來建立連結佇列和主題訂用](service-bus-auto-forwarding.md) 帳戶的拓撲，以允許執行各種路由模式。 比方說，您可以提供具有私用佇列的夥伴，它們具有傳送或接收許可權，而且可以在需要時暫時暫停，並以彈性的方式將它們連接至應用程式私用的其他實體。 您也可以建立複雜的多階段路由拓撲，也可以建立信箱樣式的佇列，這會清空類似佇列的訂用帳戶，並允許每個訂閱者擁有更多儲存容量。 

許多複雜的解決方案也需要在命名空間界限之間複寫訊息，才能執行這些和其他模式。 訊息可能必須在與多個不同的應用程式租使用者相關聯的命名空間之間流動，或在多個不同的 Azure 區域之間流動。 

您的解決方案會維護不同區域中的多個服務匯流排命名空間，並在佇列與主題之間複寫訊息，以及/或您將與來源和目標（如 [Azure 事件中樞](../event-hubs/event-hubs-about.md)、 [Azure IoT 中樞](../iot-fundamentals/iot-introduction.md)或 [Apache Kafka](https://kafka.apache.org)）交換訊息。 

這些案例是本文的重點。 

## <a name="federation-patterns"></a>同盟模式

您可能會想要在服務匯流排實體（例如佇列或主題）之間，或在服務匯流排與其他來源與目標之間移動訊息，有許多可能的動機。 

相較于 [事件中樞](../service-bus-messaging/service-bus-federation-overview.md)的類似模式集合，類似佇列的實體的同盟會更複雜，因為訊息佇列可保證其取用者必須有任何單一訊息的獨佔擁有權、預期會在訊息傳遞中保留抵達順序，而訊息代理程式則會在 [競爭取用者](/azure/architecture/patterns/competing-consumers)之間協調訊息的公平散發。 

有一些實用的阻礙，包括 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)的條件約束，因此很難提供可在多個區域同時提供之佇列的統一觀點，並可讓地區散發、競爭的取用 [者](/azure/architecture/patterns/competing-consumers) 取得訊息的獨佔擁有權。 這類異地分散的佇列需要完全一致的複寫，而不只是訊息，還需要每個訊息的傳遞狀態，才能讓取用者使用訊息。 假設的地區分散式佇列完全一致的目標，就是在考慮同盟案例時，與幾乎 Azure 服務匯流排所有客戶都有的主要目標有直接的衝突：其解決方案的最高可用性和可靠性。 

因此，此處所呈現的模式著重于可用性和可靠性，同時也是最能避免資訊遺失和重複處理訊息的意圖。 

### <a name="resiliency-against-regional-availability-events"></a>針對區域可用性事件的復原能力 

雖然最高可用性和可靠性是服務匯流排的最高操作優先權，但在某些情況下，生產者或取用者可能會因為網路或名稱解析問題而無法與指派的「主要」服務匯流排交談，或是服務匯流排實體可能會暫時沒有回應或傳回錯誤。 指定的訊息處理器也可能變得無法使用。

這種情況並不是「災難性的」，因此您會想要完全放棄區域部署，就像您在嚴重損壞修復的情況下所做的一樣，但是某些應用程式的商務案例可能已受到過去不超過幾分鐘或甚至幾秒鐘的可用性事件所影響。 Azure 服務匯流排通常用於混合式雲端環境，以及位於網路邊緣的用戶端，例如零售商店、餐廳、銀行分支、製造地點、物流設施和機場。 網路路由或擁塞問題可能會影響任何一個網站存取其所指派服務匯流排端點的能力，而不同區域中的次要端點則可能可連線。 同時，處理來自這些網站之訊息的系統仍可能無法防止存取主要和次要服務匯流排端點。 

這類混合式雲端和邊緣應用程式有許多實用的範例，對網路路由問題的影響或服務匯流排實體的暫時性可用性問題有很低的商業承受力。 這包括處理零售地點的付款、機場閘道，以及餐廳中的行動電話訂單，而這些都是立即的，並且會在每次可靠的通訊路徑無法使用時完成停滯。

在此類別中，我們將討論三種不同的分散式模式：「全主動」複寫、「主動-被動」複寫和「溢出」複寫。 

#### <a name="all-active-replication"></a>All-Active 複寫

「全部-主動」複寫模式允許相同邏輯主題的作用中複本 (或佇列) 可用於多個命名空間 (和區域) ，而且所有的訊息都可以在所有複本中使用，而不論它們是否已排入佇列。 此模式通常會保留相對於任何發行者的訊息順序。 

![所有現用模式](media/service-bus-federation-overview/mirrored-topics.jpg)

如圖所示，此模式通常會仰賴在服務匯流排主題上。 每個應該參與複寫配置的命名空間都有一個主題。 這些主題中的每個主題都有一個「複寫訂閱」，適用于任何其他應複寫訊息的主題。 在上圖中，我們只會有一組主題，因此為個別的其他主題提供單一複寫訂閱。 在具有三個命名空間 *{n1，n2，n3}* 的案例中，命名空間 *n1* 中的主題會有兩個複寫訂閱，一個用於 *n2* 中對應的主題，另一個用於 *n3* 中的對應主題。 

每個複寫訂閱都有一個規則，可結合 SQL 篩選運算式 (`replicated <> 1`) ，以及 () 的 sql 動作 `set replicated = 1` 。 規則的篩選可確保只有未設定自訂屬性或沒有 `replication` 值的訊息 `1` 會變成適合此訂用帳戶，而且該動作會在之後將該確切屬性設定為 `1` 每個所選訊息上的值。 效果是當訊息複製到對應的主題時，它不再適合以相反的方向進行複寫，因此我們可以避免在複本之間彈跳的訊息。

您可以使用與此類似的 Azure CLI，輕鬆地將具有個別規則的訂用帳戶新增至任何主題。

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

若要建立佇列模型，每個主題只會限制為一個定期訂用帳戶 (而非所有取用者共用) 的複寫訂閱。

> All-主動複寫模型會將每個傳送至主題的每個訊息複本放入每個主題中。 這表示您在每個區域中的應用程式程式碼都會看到並處理所有訊息。
> 此模式適用于將資料共用到多個區域，或通常需要重複處理的案例。 如果您只需要處理每個訊息一次，如同一般佇列，您需要考慮下列兩種模式的其中一種。  

#### <a name="active-passive-replication"></a>Active-Passive 複寫

「主動-被動」複寫模式是先前模式的變化，在此模式中，應用程式只會使用「主要」 )  (「主要」的其中一個主題來傳送和接收訊息，而且當主要主題可能無法使用或無法存取時，就會將訊息複寫至次要主題。 

![主動被動模式](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

此模式與先前模式之間的主要差異是，從主要主題到次要主題的複寫都是單向的。 次要主題永遠不會變成主要主題，但是是主要主題暫時無法使用時的備份選項。 

使用此模式的優點是它會嘗試將重複的處理降至最低。 在複寫期間， `TimeToLive` 訊息屬性會在複寫的訊息上設定為持續時間，以反映主要複本失敗導致容錯移轉的預期時間。 比方說，如果您的使用案例需要將取用者切換到最多1分鐘內的次要資料庫（從顯示問題的主要開始抓取訊息時），則次要複本應該會有您無法在主資料庫中存取的所有可用訊息，但在問題出現之前，您已經從主資料庫處理的訊息數目最少。 如果我們將設定 `TimeToLive` 為兩倍的間隔，2分鐘，在 (`set sys.TimeToLive = '0:2:0'` 的規則) 動作中，次要複本只會保留2分鐘的訊息，並捨棄舊的訊息。 這表示當接收者切換至次要資料庫時，它可以快速地讀取和捨棄早于最後一個處理常式的訊息，然後再從尚未看到的第一則訊息進行處理。 實際的保留持續時間取決於特定的使用案例，而且您可以在應用程式中快速切換至次要資料庫。 `TimeToLive`這項設定會在數秒到數天之間接受。 

當應用程式使用次要資料庫時，它也可以直接發佈至次要主題，這會如同任何一般主題一般。 在切換至次要資料庫之後，取用者將會看到混合的複寫訊息和直接發佈至次要資料庫的訊息。 因此，應用程式應該先將發行切換回主資料庫，並在將取用者切換回次要資料庫之前，仍允許清空本機發佈的訊息。 當主要複本再次可用時，因為複寫會自動繼續，所以取用者也會在這段時間內取得新的訊息發佈到主資料庫，但延遲較高。 

> 此模式適用于應只處理一次訊息的案例。 應用程式必須合作追蹤它從主資料庫處理的訊息，因為它會在次要資料庫中的容錯移轉視窗持續期間找到重複的專案，而且在切換回來時，會再次發現重複專案。
> 重復資料刪除準則應最適合提供應用程式 `MessageId` 。 此 `EnqueuedTimeUtc` 值也適合作為浮水印指標，但應用程式必須允許某些數量的時鐘漂移 (主要和次要資料庫之間的數秒) ，如同任何分散式系統。


#### <a name="spillover-replication"></a>溢出複寫

"溢出" 複寫模式允許在多個區域中使用多個服務匯流排實體的主動/主動使用，以處理服務匯流排狀況良好的案例，但取用 *者* 會因為擱置中的訊息數目或完全無法使用而被佔用。 原因可能是支援取用者進程的資料庫可能很慢或無法使用。 此模式適用于純佇列和主題訂閱。  

![溢出複寫](media/service-bus-federation-overview/spillover.jpg)  

如圖所示，溢出複寫模式會將訊息從佇列或訂用帳戶的相關聯寄不出 [的信件佇列](service-bus-dead-letter-queues.md) ，複寫至不同命名空間中配對的佇列或主題。 

如果沒有發生失敗狀況，則會以平行方式使用這兩個命名空間，每個命名空間都會接收部分的整體訊息流量，以及其相關聯的取用者處理該子集。 一旦其中一個取用者開始出現高失敗率或完全停止，則個別的訊息將會在寄不出的信件佇列中，最後是因為超過傳遞計數或過期。 複寫工作接著會在配對的佇列中挑選它們，然後將它們重新排入佇列，然後將它們呈現給 presumable 狀況良好的取用者。 

如果必須在特定期限內進行處理，則 `TimeToLive` 應該設定佇列和/或訊息的，以便在溢出次要資料庫仍能及時進行處理，例如， `TimeToLive` 可能會設定為允許的一半時間。

如同 [all-主動模式](#all-active-replication)，應用程式可以在訊息中加入指標，指出訊息是否已複寫一次，因此它們不會在一組佇列之間切換，而是張貼到輔助佇列中，以作為複合模式的寄不出的信件佇列。

> 這種模式適用于最重要的考慮因素，是要防範取用者或取用者所依賴的資源中的可用性問題，也可以在其中一個配對的佇列上重新散發流量尖峰。 如果取用者從這兩個佇列讀取，它也會讓其中一個命名空間變成無法使用，但到期時所加諸的複寫延遲 `TimeToLive` 可能會導致該時間範圍內的訊息絞在無法使用的命名空間中。 

### <a name="latency-optimization"></a>延遲優化 

主題可用來將資訊散發給多個取用者。 在某些情況下（特別是具有廣泛地理分佈的取用者），將主題中的訊息複寫到較接近取用者的次要命名空間中的主題可能會很有用。

![延遲優化](media/service-bus-federation-overview/latency-optimization.jpg)  

例如，在區域、大陸中樞之間共用資料時，在中樞之間只傳送資訊一次，並讓取用者從這些中樞取得資料複本，會更有效率。 

複寫傳輸可以批次的方式進行，而取用者通常會逐一取得和結算訊息。 相較于相同區域中的實體，在北美洲和歐洲之間的基本網路延遲為100毫秒時，每則訊息會200花更長的時間來處理遠端實體的兩次來回行程，以取得和輪詢訊息。 

### <a name="validation-reduction-and-enrichment"></a>驗證、縮減和擴充

您可以將訊息提交至服務匯流排佇列或主題，讓用戶端在您自己的解決方案外部。

![驗證、縮減、擴充](media/service-bus-federation-overview/validation.jpg)  

這類訊息可能需要檢查指定的架構是否符合規範，以及是否要捨棄不符合規範的訊息或使其失效。 某些訊息可能必須藉由省略資料來降低複雜度，而某些訊息可能必須藉由根據參考資料查閱加入資料來擴充。 您可以使用複寫工作中的自訂功能來執行作業。 

### <a name="stream-to-queue-replication"></a>串流至佇列複寫

Azure 事件中樞是處理極大量傳入事件的理想解決方案。 但是，事件中樞和 Apache Kafka 等類似的引擎都不會提供服務管理的 [競爭取用者](/azure/architecture/patterns/competing-consumers) 模型，讓多個取用者可以同時處理來自相同來源的訊息，而不會產生風險的重複處理，最後在處理過這些訊息之後加以結算。 

![整合](media/service-bus-federation-overview/hub-to-queue.jpg)

佇列複寫的資料流程會將單一事件中樞磁碟分割的內容或完整事件中樞的內容傳送至服務匯流排佇列，以便安全地處理訊息，並以競爭取用者的方式處理訊息。 此複寫也可以使用這些訊息的所有其他服務匯流排功能，包括使用主題和會話型分離信號進行路由。 

### <a name="consolidation-and-normalization"></a>合併和正規化 

全球解決方案通常是由大部分獨立的區域足跡所組成，包括擁有自己的處理能力，但超區域和全球觀點則需要資料整合，因此集中合併在個別區域足跡中針對當地觀點所評估的相同訊息資料。 

![固結](media/service-bus-federation-overview/merge.jpg)

正規化是合併案例的一種類別，其中有兩個或多個傳入的訊息序列會包含相同類型的資訊，但使用不同的結構或不同的編碼方式，而且必須先轉碼或轉換訊息，然後才能使用這些訊息。 

正規化也可能包含加密工作，例如解密端對端加密的承載，並使用不同的金鑰和演算法對下游取用者物件進行重新加密。 

### <a name="splitting-and-routing"></a>分割和路由

服務匯流排主題及其訂用帳戶規則通常用來篩選特定物件的訊息串流，然後該物件會從訂用帳戶取得篩選過的集合。 

![分割中](media/service-bus-federation-overview/split.jpg)

在全域系統中，這些訊息的物件是全域散發或屬於不同的應用程式，複寫可用來將訊息從這類訂用帳戶傳送到不同命名空間中的佇列或主題，以供取用。

### <a name="replication-applications-in-azure-functions"></a>Azure Functions 中的複寫應用程式

針對您想要設定和執行的複寫工作，執行上述模式需要可調整且可靠的執行環境。 在 Azure 上，最適用于無狀態工作的執行時間環境是 [Azure Functions](../azure-functions/functions-overview.md)。 

Azure Functions 可以在 [Azure 受控識別](../active-directory/managed-identities-azure-resources/overview.md) 下執行，如此一來，複寫工作就可以與來源和目標服務的角色型存取控制規則整合，而不需要透過複寫路徑管理秘密。 對於需要明確認證的複寫來源和目標，Azure Functions 可以將這些認證的設定值儲存在 [Azure Key Vault](../key-vault/general/overview.md)內緊密存取控制的儲存體中。

Azure Functions 此外，可讓複寫工作直接與 Azure 虛擬網路和 [服務端點](../virtual-network/virtual-network-service-endpoints-overview.md) 整合，以取得所有 azure 訊息服務，並與 [Azure 監視器](../azure-monitor/overview.md)緊密整合。

最重要的是，Azure Functions 具有適用于 [Azure 事件中樞](../azure-functions/functions-bindings-service-bus.md)、 [Azure IoT 中樞](../azure-functions/functions-bindings-event-iot.md)、 [Azure 服務匯流排](../azure-functions/functions-bindings-service-bus.md)、 [azure 事件方格](../azure-functions/functions-bindings-event-grid.md)和 [azure 佇列儲存體](/azure/azure-functions/functions-bindings-storage-queue)、適用于 [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)的自訂擴充功能和 [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)的預建、可調整的觸發程式和輸出系結。 大部分的觸發程式會根據記載的計量，將同時執行的實例數目相應增加或減少，以動態方式調整輸送量需求。 

使用「Azure Functions 取用方案」時，預建的觸發程式甚至可以在沒有任何訊息可供複寫時縮小為零，這表示您無須支付設定準備好進行調整的成本。 使用取用方案的主要缺點是，複寫工作「從這個狀態喚醒」的延遲明顯高於基礎結構保持執行所在的主控方案。  

相較之下，訊息和事件的最常見複寫引擎（例如 Apache Kafka 的 [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) ）會要求您提供裝載環境，並自行調整複寫引擎。 這包括設定和整合安全性和網路功能，以及加速監視資料的流程，然後您仍然沒有機會將自訂複寫工作插入流程中。 

## <a name="next-steps"></a>後續步驟

在本文中，我們探討了一系列的同盟模式，並說明了 Azure Functions 作為 Azure 中事件和訊息複寫執行時間的角色。 

接下來，您可能會想要瞭解如何使用 Azure Functions 來設定複製器應用程式，然後如何在事件中樞與其他各種事件和訊息系統之間複寫事件流程：

- [Azure Functions 中的複寫應用程式](service-bus-federation-replicator-functions.md)
- [在服務匯流排實體之間複寫事件](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [將事件路由至 Azure 事件中樞](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [從 Azure 事件中樞取得事件](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif 
