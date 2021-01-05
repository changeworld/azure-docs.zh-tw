---
title: 多網站和多區域同盟-Azure 事件中樞 |Microsoft Docs
description: 本文提供多網站和多區域同盟與 Azure 事件中樞的總覽。
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 12ef895c8b16fe18ed02ebf01d17624ac71c2f3e
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861455"
---
# <a name="multi-site-and-multi-region-federation"></a>多網站和多區域同盟

許多複雜的解決方案都需要相同的事件資料流程，才能在多個位置取用，或者它們需要在多個位置中收集事件資料流程，然後合併到特定的位置以供取用之用。 此外，也需要擴充或減少事件串流或進行事件格式轉換，也就是在單一區域和方案內。

實際上，這表示您的解決方案會維護多個事件中樞，通常位於不同的區域和事件中樞命名空間，然後在其間複寫事件。 您也可以與來源和目標（例如 [Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)、 [Azure IoT 中樞](../iot-fundamentals/iot-introduction.md)或 [Apache Kafka](https://kafka.apache.org)）交換事件。 

在不同區域中維護多個使用中的事件中樞也可讓用戶端在其內容進行合併時，選擇並在兩者之間切換，如此可讓整體系統更能因應區域性可用性問題而更具彈性。

這一章的「同盟」一章說明同盟模式，以及如何使用無伺服器 [Azure 串流分析](../stream-analytics/stream-analytics-introduction.md) 或 [Azure Functions][1] 執行時間來實現這些模式，並可選擇在事件流程路徑中直接使用自己的轉換或擴充程式碼。 

## <a name="federation-patterns"></a>同盟模式

您可能會想要在不同事件中樞或其他來源與目標之間移動事件的原因有很多，而且我們列舉本節中最重要的模式，並連結至個別模式的更詳細指引。 

- [針對區域可用性事件的復原能力](#resiliency-against-regional-availability-events)
- [延遲優化](#latency-optimization)
- [驗證、縮減和擴充](#validation-reduction-and-enrichment)
- [與分析服務整合](#integration-with-analytics-services)
- [事件資料流程的匯總和正規化](#consolidation-and-normalization-of-event-streams)
- [分割和路由事件資料流程](#splitting-and-routing-of-event-streams)
- [記錄投影](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>針對區域可用性事件的復原能力 

![區域可用性](media/event-hubs-federation-overview/regional-availability.jpg)

雖然最高可用性和可靠性是事件中樞最重要的作業優先順序，但在某些情況下，生產者或取用者可能會因為網路或名稱解析問題而無法與指派的「主要」事件中樞交談，或事件中樞可能真的沒有回應或傳回錯誤。 

這種情況並不是「災難性的」，因此您會想要完全放棄區域部署，就像您在嚴重損壞 [修復](event-hubs-geo-dr.md) 的情況下所做的一樣，但是某些應用程式的商務案例可能已受到過去不超過幾分鐘或甚至幾秒鐘的可用性事件所影響。 

有兩種基本模式可處理這類案例：

- 複寫 [模式是][4] 關於將主要事件中樞的內容複寫至次要事件中樞，而應用程式通常會使用主要事件中樞來產生和取用事件，而次要資料庫則是在主要事件中樞變成無法使用時作為回寫選項。 由於複寫是單向的，從主要複本到次要複本，將產生者和取用者從無法使用的主要轉換成次要資料庫時，會導致舊的主資料庫不再收到新的事件，因此將不再是最新的事件。
  因此，純複寫僅適用于單向容錯移轉案例。 一旦執行容錯移轉，就會放棄舊的主要複本，而且必須在不同的目的地區域中建立新的次要事件中樞。
- [合併][5]模式會執行兩個或多個事件中樞的內容連續合併，以擴充複寫模式。 在配置中所包含的其中一個事件中樞原始產生的每個事件，都會複寫到另一個事件中樞。 當事件被覆寫時，它們會加上批註，以供複寫目標的複寫程式之後略過。 使用合併模式的結果是兩個或多個事件中樞，將以最終一致的方式包含相同的事件集。 
  
無論是哪一種情況，事件中樞的內容都不會相同。 來自任何一個產生者並依相同資料分割索引鍵分組的事件，會以原先提交的相對順序顯示，但事件的絕對順序可能不同。 尤其適用于來源和目標事件中樞的分割區計數不同的情況，這對這裡所述的數個擴充模式而言是很好的。 分割 [器或路由器](#splitting-and-routing-of-event-streams) 可能會以數百個分割區和漏斗圖來取得較大的事件中樞配量至較小的事件中樞，而且只有少數磁碟分割，更適合用來處理有限處理資源的子集。 相反地， [合併](#consolidation-and-normalization-of-event-streams) 可能會將數個較小的事件中樞的資料，分組成單一、較大的事件中樞以及更多資料分割，以應付合併的輸送量和處理需求。

將事件保持在一起的準則是分割區索引鍵，而不是原始資料分割識別碼。 關於相對順序的進一步考慮，以及如何在不依賴相同範圍的資料流程位移的情況下，從某個事件中樞執行容錯移轉至下一個事件中樞，則會在 [複寫模式描述][4] 中討論。


指引： 
- [複寫模式][4]
- [合併模式][5]

### <a name="latency-optimization"></a>延遲優化 

![延遲優化](media/event-hubs-federation-overview/latency-optimization.jpg)  

事件串流會由生產者寫入一次，但事件取用者可能會讀取任意次數。 適用于區域中的事件資料流程由多個取用者共用的案例，而且需要在位於不同區域的分析處理期間重複存取，或在使並行取用者的整個要求中，將事件資料流程的複本放在分析處理器附近，以減少往返延遲。 

若要使用複寫的最佳方式，而不是從跨區域遠端取用事件，尤其是區域遠相距較遠的地方（例如，歐洲和澳大利亞幾乎 antipodes），地理位置和網路延遲可能會在任何來回行程中輕鬆超過250毫秒。
您無法加速燈的速度，但是可以減少與資料互動的高延遲往返次數。

指引： 
- [複寫模式][4]

### <a name="validation-reduction-and-enrichment"></a>驗證、縮減和擴充

![驗證、縮減、擴充](media/event-hubs-federation-overview/validation-enrichment.jpg)  

事件串流可能會由您自己解決方案外部的用戶端提交至事件中樞。 這類事件串流可能需要檢查外部提交的事件是否符合指定的架構，以及要卸載不符合規範的事件。 

在用戶端極大頻寬限制的情況下，在許多使用計量付費頻寬的「物聯網」案例中，或者事件最初是在具有受限封包大小的非 IP 網路上傳送的情況下，可能必須使用參考資料來擴充事件，以新增可供下游事件處理器使用的更多內容。

在其他情況下，特別是當串流正在合併時，事件資料可能必須藉由省略一些詳細資料來降低複雜度或大小的巨大。

這些作業中的任何一項都可能會在複寫、匯總或合併流程中發生。 

指引： 
- [編輯器模式][6]

### <a name="integration-with-analytics-services"></a>與分析服務整合

![與分析服務整合](media/event-hubs-federation-overview/integration.jpg)

Azure 的一些雲端原生分析服務（例如 Azure 串流分析或 Azure Synapse）與從 Azure 事件中樞提供的串流或預先批次處理資料效果最佳，Azure 事件中樞也可與數個開放原始碼分析套件（例如 Apache Samza、Apache Flink、Apache Spark 和 Apache Storm）整合。 

如果您的解決方案主要使用服務匯流排或事件格線，您可以讓這類分析系統輕鬆地存取這些事件，也可以在將它們加入事件中樞時，使用事件中樞捕獲來保存這些事件。 事件方格可透過其 [事件中樞整合](../event-grid/handler-event-hubs.md)，以原生方式執行，針對服務匯流排，您可以遵循 [服務匯流排複製指引](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)。

Azure 串流分析會 [直接與事件中樞整合](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)。

指引： 
- [複寫模式][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>事件資料流程的匯總和正規化

![事件資料流程的匯總和正規化](media/event-hubs-federation-overview/consolidation.jpg)

全球解決方案通常是由大部分獨立的區域性足跡所組成，包括擁有自己的分析功能，但超區域和全域分析的觀點將需要整合的觀點，這也是為什麼在個別的區域足跡中評估的相同事件串流的集中合併，以進行本機的觀點。 

正規化是合併案例的一種類別，其中有兩個以上的內送事件資料流程會攜帶相同類型的事件，但使用不同的結構或不同的編碼方式，以及最常轉碼或轉換的事件，然後才可以使用。 

正規化也可能包含加密工作，例如解密端對端加密的承載，並使用不同的金鑰和演算法對下游取用者物件進行重新加密。 

指引： 
- [合併模式][5]
- [編輯器模式][6]

### <a name="splitting-and-routing-of-event-streams"></a>分割和路由事件資料流程

![分割和路由事件資料流程](media/event-hubs-federation-overview/splitting.jpg)

Azure 事件中樞偶爾會用於「發佈-訂閱」樣式案例中，內嵌事件的內送 torrent 遠超過 Azure 服務匯流排或 Azure 事件方格的容量，這兩種情況都有原生髮布/訂閱篩選和散發功能，而且在此模式中是慣用的。 

雖然真正的「發佈訂閱」功能會將其保留給訂閱者來挑選他們想要的事件，但是分割模式則是讓生產者依預先決定的散發模型將事件對應至分割區，然後指定的取用者會以獨佔方式從「其」分割區提取。 當事件中樞緩衝處理整體流量時，特定磁碟分割的內容（代表原始輸送量磁片區的一部分）可能會複寫到佇列中，以提供可靠、交易式、競爭的取用者耗用量。

事件中樞主要用於在某個區域內的應用程式中移動事件的許多案例，在某些情況下，您也必須從單一分割區中取得選取事件。 此案例類似于分割案例，但可能會使用可擴充的路由器，以考慮所有抵達事件中樞的訊息，並揀選-只挑選幾個來進行向前路由，而且可能會根據事件中繼資料或內容來區分路由目標。 

指引：
- [路由模式][7]

### <a name="log-projections"></a>記錄投影 

![記錄投射](media/event-hubs-federation-overview/log-projection.jpg)

在某些情況下，您會想要能夠存取事件的任何子串流所傳送的最新值，而且通常會依分割區索引鍵來區分。 在 Apache Kafka 中，這通常是藉由啟用主題的「記錄檔壓縮」來達成，這會捨棄所有標記為唯一索引鍵的最新事件。 對數壓縮方法有三個複合缺點： 

- 壓縮需要連續重組記錄檔，這是針對僅限附加工作負載而優化的訊息代理程式，這是一項過於昂貴的作業。 
- 壓縮具有破壞性，而且不允許對相同的資料流程進行壓縮和未壓縮的觀點。
- 壓縮的資料流程仍具有連續存取模型，這表示在記錄檔中尋找所需的值，需要在最糟的情況下讀取整個記錄檔，這通常會導致優化，以執行此處所示的確切模式：將記錄內容投射到資料庫或快取中。 

最後，壓縮的記錄檔是索引鍵-值存放區，因此這是這類存放區的最糟可能的執行選項。 這對查閱和查詢來說更有效率，因為它會在適當的索引鍵/值存放區或其他資料庫上建立和使用記錄的永久投影。 

由於事件是不可變的，且順序一律會保留在記錄中，因此記錄到索引鍵/值存放區的任何投射永遠都會與相同範圍的事件保持一致，這表示您保持更新的投射一律會提供授權視圖，而且永遠不會有充分的理由從記錄內容中重建。 

指引：
- [記錄投射][8]

## <a name="replication-application-technologies"></a>複寫應用程式技術

針對您想要設定和執行的複寫工作，執行上述模式需要可調整且可靠的執行環境。 在 Azure 上，最適合這類工作的執行時間環境是無狀態的工作，也就是可設定狀態之資料流程複寫工作的 [Azure 串流分析](../stream-analytics/stream-analytics-introduction.md) ，以及適用于無狀態複寫工作的 [Azure Functions](../azure-functions/functions-overview.md) 。

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Azure 串流分析中的具狀態複寫應用程式

對於需要考慮事件之間的關聯性、建立複合事件、擴充事件或減少事件、建立資料匯總以及轉換事件裝載的具狀態複寫應用程式， [Azure 串流分析](../stream-analytics/stream-analytics-introduction.md) 是最佳的執行選項。

在 Azure 串流分析中，您可以[建立工作](../stream-analytics/stream-analytics-quick-create-portal.md)來整合[輸入](../stream-analytics/stream-analytics-add-inputs.md)和輸出，並透過[查詢](/stream-analytics-query/stream-analytics-query-language-reference)產生可在輸出中提供的結果來整合輸入和[輸出](../stream-analytics/stream-analytics-define-outputs.md)資料。

查詢是以 [SQL 查詢語言](/stream-analytics-query/stream-analytics-query-language-reference) 為基礎，而且可以用來在一段時間內輕鬆地篩選、排序、匯總和聯結串流資料。 您也可以使用 [JavaScript](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) 和 [c # 使用者定義函式 ](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md)來擴充此 SQL 語言， (udf) 。 透過簡單的語言建構和/或設定執行彙總作業時，您可以輕鬆調整事件排序選項和時間範圍的持續長度。

每個作業都具有所轉換資料的一或多個輸出，並可控制所要採取的動作以回應您所分析的資訊。 例如，您可以：

- 將資料傳送至服務如 Azure Functions、服務匯流排主題或佇列，以觸發通訊或自訂工作流程下游。
- 將資料傳送至 Power BI 儀表板以即時顯示在儀表板上。
- 將資料儲存在其他 Azure 儲存體服務中 (例如 Azure Data Lake、Azure Synapse Analytics 等），) 執行批次分析，或根據歷程記錄資料的大量索引集區來定型機器學習模型。
- 在資料庫 ([SQL Database](../stream-analytics/sql-database-output.md) [Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) ) 中，儲存投影 (也稱為「具體化視圖」 ) 。

### <a name="stateless-replication-applications-in-azure-functions"></a>Azure Functions 中的無狀態複寫應用程式

針對無狀態複寫工作，您想要在不考慮其承載的情況下轉送事件，或單獨處理它們，而不需要考慮事件的關聯性 (除了其相對順序) ，您可以使用 Azure Functions，以提供極大的彈性。

Azure Functions 具有適用于 [Azure 事件中樞](../azure-functions/functions-bindings-event-hubs.md)、 [Azure IoT 中樞](../azure-functions/functions-bindings-event-iot.md)、 [Azure 服務匯流排](../azure-functions/functions-bindings-service-bus.md)、 [azure 事件方格](../azure-functions/functions-bindings-event-grid.md)和 [azure 佇列儲存體](../azure-functions/functions-bindings-storage-queue.md)的預建、可調整的觸發程式和輸出系結，以及適用于 [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)和 [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)的自訂擴充功能。 大部分的觸發程式會根據記載的計量，將同時執行的實例數目相應增加或減少，以動態方式調整輸送量需求。 

針對建立記錄投射，Azure Functions 支援 [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) 和 [Azure 資料表儲存體](../azure-functions/functions-bindings-storage-table-output.md)的輸出系結。

Azure Functions 可以在 [Azure 受控身分識別](../active-directory/managed-identities-azure-resources/overview.md) 下執行，而且可以在 [Azure Key Vault](../key-vault/general/overview.md)內的緊密存取控制儲存體中保存認證的設定值。

Azure Functions 還可讓複寫工作直接與 Azure 虛擬網路和 [服務端點](../virtual-network/virtual-network-service-endpoints-overview.md) 整合，以供所有 azure 訊息服務使用，並與 [Azure 監視器](../azure-monitor/overview.md)緊密整合。

使用「Azure Functions 取用方案」時，預建的觸發程式甚至可以在沒有任何訊息可供複寫時縮小為零，這表示您無須支付設定準備好進行調整的成本;使用取用方案的主要缺點是，複寫工作「從這個狀態喚醒」的延遲明顯高於基礎結構保持執行所在的主控方案。  

相較之下，訊息和事件的最常見複寫引擎（例如 Apache Kafka 的 [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) ）會要求您提供裝載環境，並自行調整複寫引擎。 這包括設定和整合安全性和網路功能，以及加速監視資料的流程，然後您仍然沒有機會將自訂複寫工作插入流程中。 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>在 Azure Functions 與 Azure 串流分析之間做選擇

當您需要在複寫事件的承載時處理其承載時，Azure 串流分析 (ASA) 是最佳選項。 ASA 可以逐一複製事件，也可以建立匯總來壓縮事件資料流程的資訊，然後再將其轉送。 它可以輕易地精簡 Azure Blob 儲存體或 Azure SQL Database 中保留的 [參考資料](../stream-analytics/stream-analytics-use-reference-data.md) ，而不需要將這類資料匯入資料流程中。

有了 ASA，您就可以輕鬆地在超大規模資料庫中建立資料流程的持續性和具體化視圖。 這是相當笨拙 Apache Kafka 的「記錄壓縮」模型，以及 Kafka 串流之 volatile、用戶端資料表投影的絕佳方法。 

ASA 可以立即處理具有以 [CSV、JSON 和 Apache Avro 格式](../stream-analytics/stream-analytics-parsing-json.md) 編碼之承載的事件，而且您可以針對任何其他格式插入 [自訂還原序列化程式](../stream-analytics/custom-deserializer.md) 。

對於您想要以「依原樣」複製事件資料流程，而不需要觸及承載的所有複寫工作，或者，如果您需要執行路由器、執行密碼編譯工作、變更承載的編碼方式，或如果其他需要完全掌控資料流程內容，Azure Functions 是最佳選項。

## <a name="next-steps"></a>後續步驟

在本文中，我們探討了一系列的同盟模式，並說明了 Azure Functions 作為 Azure 中事件和訊息複寫執行時間的角色。

接下來，您可能會想要瞭解如何使用 Azure 串流分析或 Azure Functions 來設定複寫器應用程式，然後如何在事件中樞與其他各種事件和訊息系統之間複寫事件流程：

- [事件複寫工作模式][10]
- [使用 Azure 串流分析處理資料][9]
- [Azure Functions 中的事件複製器應用程式][1]
- [在事件中樞之間複寫事件][2]
- [將事件複寫至 Azure 服務匯流排][3]
- [使用 Apache Kafka MirrorMaker 搭配事件中樞][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md