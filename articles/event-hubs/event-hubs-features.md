---
title: 功能概觀 - Azure 事件中樞 | Microsoft Docs
description: 本文將詳細說明 Azure 事件中樞的相關功能與術語。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8860a8aa83a17b12236dd47d79479a82846fa8a8
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791941"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Azure 事件中樞的功能與術語

事件中樞是可調整的事件處理服務，它會擷取和處理大量的事件和資料，具有低延遲和高可靠性。 如需高階概觀，請參閱[何謂事件中樞？](./event-hubs-about.md)

這篇文章是根據[概觀](./event-hubs-about.md)中的資訊建置，並且提供有關事件中樞元件和功能的技術和實作詳細資料。

> [!TIP]
> [ **Apache Kafka** 用戶端的通訊協定支援](event-hubs-for-kafka-ecosystem-overview.md)  (版本 >= 1.0) 提供的網路端點，可讓建立的應用程式搭配任何用戶端使用 Apache Kafka，以使用事件中樞。 大部分現有的 Kafka 應用程式都可以直接重新設定為指向事件中樞命名空間，而不是 Kafka 叢集啟動程式伺服器。 
>
>從成本、營運工作量和可靠性的觀點來看，Azure 事件中樞是部署和操作您自己的 Kafka 和 Zookeeper 叢集，以及不是 Azure 原生的 Kafka 即服務供應專案的絕佳替代方案。 
>
> 除了取得 Apache Kafka 訊息代理程式的相同核心功能之外，您還可以存取 Azure 事件中樞功能，例如自動批次處理和透過 [事件中樞捕捉](event-hubs-capture-overview.md)進行的封存、自動調整和平衡、嚴重損壞修復、成本中立的可用性區域支援、彈性且安全的網路整合，以及多通訊協定的支援，包括防火牆易記的 AMQP over websocket 通訊協定。


## <a name="namespace"></a>命名空間
事件中樞命名空間提供 DNS 整合式網路端點，以及一系列的存取控制和網路整合管理功能（例如 [IP 篩選](event-hubs-ip-filtering.md)、 [虛擬網路服務端點](event-hubs-service-endpoints.md)和 [Private Link](private-link-service.md) ），而且是在 Kafka 說法) 中的多個事件中樞實例之一 (或主題的管理容器。

## <a name="event-publishers"></a>事件發佈者

將資料傳送至事件中樞的任何實體都是 (同義與 *事件* 產生者) 搭配使用的 *事件發行者*。 事件發行者可以使用 HTTPS 或 AMQP 1.0 或 Kafka 通訊協定發佈事件。 事件發行者會使用 Azure Active Directory 型授權搭配 OAuth2 發出的 JWT 權杖或事件中樞特定的共用存取簽章 (SAS) 權杖取得發佈存取權。

### <a name="publishing-an-event"></a>發佈事件

您可以透過 AMQP 1.0、Kafka 通訊協定或 HTTPS 來發佈事件。 事件中樞服務會提供 [REST API](/rest/api/eventhub/) 和 [.net](event-hubs-dotnet-standard-getstarted-send.md)、 [JAVA](event-hubs-java-get-started-send.md)、 [Python](event-hubs-python-get-started-send.md)、 [JavaScript](event-hubs-node-get-started-send.md)和 [Go](event-hubs-go-get-started-send.md) 用戶端程式庫，以將事件發佈至事件中樞。 對於其他執行階段和平台，您可以使用任何 AMQP 1.0 用戶端 (如 [Apache Qpid](https://qpid.apache.org/))。 

使用 AMQP 或 HTTPS 的選擇因使用案例而異。 除了傳輸層級安全性 (TLS) 或 SSL/TLS 之外，AMQP 還需要建立持續性的雙向通訊端。 在初始化會話時，AMQP 會有較高的網路成本，不過 HTTPS 需要針對每個要求增加額外的 TLS 負擔。 針對頻繁的發行者，AMQP 具有大幅較高的效能，而且在搭配非同步發佈程式碼使用時可以達到更低的延遲。

您可以個別或批次發佈事件。 單一發行集的限制為 1 MB，不論它是單一事件或批次。 超過此臨界值的發佈事件將會遭到拒絕。 

事件中樞輸送量是使用資料分割和輸送量單位配置來調整 (請參閱以下) 。 發行者最好保持不知道針對事件中樞選擇的特定資料分割模型，並且只指定用來一致地將相關事件指派給相同資料分割的資料分割索引 *鍵* 。

![資料分割索引鍵](./media/event-hubs-features/partition_keys.png)

事件中樞可確保共用資料分割索引鍵值的所有事件都會儲存在一起，並以抵達順序傳遞。 如果資料分割索引鍵與發佈者原則搭配使用，發佈者的身分識別與資料分割索引鍵的值必須相符， 否則就會發生錯誤。

### <a name="event-retention"></a>事件保留期

已發佈的事件會根據可設定的以時間為基礎的保留原則，從事件中樞移除。 預設值和最短的可能保留期限為1天 (24 小時) 。 針對事件中樞標準，最大保留期限為7天。 針對事件中樞專用，最大保留期限為90天。

> [!NOTE]
> 事件中樞是一種即時事件串流引擎，其設計目的不是要用來取代資料庫和/或永久存放區，以保存無限期的事件資料流程。 
> 
> 事件資料流程的歷程記錄愈深入，您將需要輔助索引以找出特定資料流程的特定歷程記錄配量。 檢查事件承載和索引不在事件中樞 (或 Apache Kafka) 的功能範圍內。 因此，資料庫與特製化分析存放區和引擎（例如 [Azure Data Lake 存放區](../data-lake-store/data-lake-store-overview.md)、 [Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) 和 [Azure Synapse](../synapse-analytics/overview-what-is.md) ）都更適合用來儲存歷程記錄事件。
>
> [事件中樞捕捉](event-hubs-capture-overview.md) 可直接與 Azure Blob 儲存體和 Azure Data Lake Storage 整合，而透過該整合，也可讓您 [直接將事件流至 Azure Synapse](store-captured-data-data-warehouse.md)。
>
> 如果您想要針對您的應用程式使用 [事件來源](/azure/architecture/patterns/event-sourcing) 模式，您應該將快照集策略與事件中樞的保留限制對齊。 請勿從一開始就從原始事件重建具體化視圖。 當您的應用程式在生產環境中一段時間後，您一定會致歉這種策略，而且您的投射產生器在嘗試趕上最新和持續進行的變更時，必須有多年的變更事件。 


### <a name="publisher-policy"></a>發佈者原則

事件中樞可讓您透過發佈者 原則更精確地控制事件發佈者。 發佈者原則是為了協助大量獨立事件發佈者而設計的執行階段功能。 有了發佈者原則，當每位發佈者使用下列機制將事件發佈到事件中樞時，都能使用自己的唯一識別碼：

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

您不需要事先建立發佈者名稱，不過這些名稱必須符合發佈事件時使用的 SAS 權杖，以確保發佈者身分識別是獨立的。 使用發行者原則時，**PartitionKey** 值會設為發行者名稱。 為了正常運作，這些值必須相符。

## <a name="capture"></a>擷取

[事件中樞擷取](event-hubs-capture-overview.md)可讓您自動擷取「事件中樞」中的串流資料，然後將它儲存到您選擇的 Blob 儲存體帳戶，或是 Azure Data Lake 服務帳戶。 您可以從 Azure 入口網站啟用「擷取」，然後指定執行擷取的大小下限和時間範圍。 使用事件中樞擷取，您就可以指定自己的 Azure Blob 儲存體帳戶和容器或是 Azure Data Lake 服務帳戶，使用其中之一來儲存擷取的資料。 擷取的資料會以 Apache Avro 格式撰寫。

## <a name="partitions"></a>資料分割
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS 權杖

事件中樞會使用 *共用存取* 簽章，可在命名空間和事件中樞層級使用。 SAS 權杖可自 SAS 金鑰產生，它是經過特定格式編碼的 URL SHA 雜湊。 事件中樞可以使用金鑰 (原則) 的名稱和權杖來重新產生雜湊，藉此驗證傳送者。 一般情況下，會建立事件發行者的 SAS 權杖，而且只會在特定事件中樞上建立「 **傳送** 」許可權。 此 SAS 權杖 URL 機制是導入發佈者原則之發佈者識別的基礎。 如需使用 SAS 的詳細資訊，請參閱[使用服務匯流排的共用存取簽章驗證](../service-bus-messaging/service-bus-sas.md)。

## <a name="event-consumers"></a>事件取用者

任何從事件中樞讀取事件資料的實體都是 *事件取用者*。 所有事件中樞取用者都透過 AMQP 1.0 工作階段連接，而可供取用的事件都透過工作階段傳遞。 用戶端不需要輪詢資料可用性。

### <a name="consumer-groups"></a>取用者群組

事件中心的發佈/訂閱機制是透過「取用者群組」啟用。 取用者群組是檢視整個事件中樞 (狀態、位置或位移) 的窗口。 取用者群組能讓多個取用應用程式擁有自己的事件串流檢視，以及按照自己的步調及運用自己的位移自行讀取串流。

在串流處理架構中，每個下游應用程式等同於一個取用者群組。 如果您想要將事件資料寫入長期存放區，該存放裝置寫入器應用程式即是取用者群組。 複雜的事件處理可以由另一個獨立的取用者群組來執行。 您只能透過取用者群組來存取資料分割。 每個事件中樞永遠有一個預設的取用者群組，而您可以為標準層事件中樞建立最多 20 個取用者群組。

每一取用者群組的一個分割區上最多可以有 5 個並行讀取器；不過，**建議在每一取用者群組的一個分割區上只有一個作用中的接收器**。 在單一分割區內，每個讀取器都會接收所有訊息。 如果相同分割區上有多個讀取器，則您會需要處理重複的訊息。 您需要在程式碼中處理此狀況，但這可能不容易。 不過，在某些情況下這是有效的方法。

Azure Sdk 所提供的部分用戶端是智慧型取用者代理程式，可自動管理詳細資料，以確保每個分割區都有單一讀取器，並且正在讀取事件中樞的所有資料分割。 這可讓您的程式碼專注于處理從事件中樞讀取的事件，讓它可以忽略分割區的許多詳細資料。 如需詳細資訊，請參閱 [連接到磁碟分割](#connect-to-a-partition)。

下列範例顯示取用者群組 URI 慣例：

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

下圖顯示事件中樞串流處理架構︰

![事件中樞架構](./media/event-hubs-about/event_hubs_architecture.svg)

### <a name="stream-offsets"></a>串流位移

「位移」是事件在資料分割內的位置。 您可以將位移視為用戶端指標。 位移是事件的位元組編號。 此位移可讓事件取用者 (讀取器) 在事件串流中指定某個點，以便從該點開始讀取事件。 您可以指定時間戳記或位移值等形式的位移。 取用者需負責將自己的位移值儲存在事件中樞服務之外。 在資料分割內，每個事件都含有一個位移。

![資料分割位移](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>檢查點檢查

*設立檢查點* 是讀取器在資料分割事件序列中標記或認可其位置的程序。 設立檢查點是取用者的責任，這項作業會在取用者群組內依照資料分割來進行。 此責任表示在每個取用者群組內，每個資料分割讀取器必須追蹤自己目前在事件串流中的位置，而當它們認為資料串流完成時，可以通知服務。

如果讀取器與資料分割中斷連線，當它重新連線時，會從該取用者群組中該資料分割最後一個讀取器先前提交的檢查點開始讀取。 當讀取器連線時，它會將此位移傳遞給事件中樞，以指定要開始讀取的位置。 如此一來，檢查點能成為下游應用程式標記事件「完成」的方法，也能針對在不同機器上執行的讀取器提供容錯移轉發生時的恢復功能。 從這個檢查點處理程序中指定較低的位移，即可回到較舊的資料。 透過這項機制，檢查點可提供容錯移轉彈性，並支援事件串流重播。

> [!NOTE]
> 如果您在支援不同于 Azure 上的儲存體 Blob SDK 版本的環境中使用 Azure Blob 儲存體作為檢查點存放區，您必須使用程式碼將儲存體服務 API 版本變更為該環境所支援的特定版本。 例如，如果您是 [在 Azure Stack Hub 2002 版上執行事件中樞](/azure-stack/user/event-hubs-overview)，則儲存體服務的最高可用版本是2017-11-09 版。 在此情況下，您必須使用程式碼將儲存體服務 API 版本的目標設為2017-11-09。 如需如何以特定儲存體 API 版本為目標的範例，請參閱 GitHub 上的下列範例： 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)。 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) 或  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>常見的取用者工作

所有事件中樞取用者都會透過 AMQP 1.0 工作階段 (狀態感知的雙向通訊通道) 來連線。 每個資料分割都有 AMQP 1.0 工作階段，有助於傳輸資料分割所隔離的事件。

#### <a name="connect-to-a-partition"></a>連接資料分割

連接至資料分割時，常見的做法是使用租用機制來協調讀取器連接到特定的資料分割。 如此一來，取用者群組中的每個資料分割都有可能只有一個作用中的讀取器。 使用事件中樞 Sdk 內的用戶端來簡化檢查點、租用和管理讀取器，以作為智慧型取用者代理程式。 這些警告是：

- 適用于 .NET 的[EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient)
- 適用于 JAVA 的[EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient)
- 適用于 Python 的[EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient)
- JavaScript/TypeScript 的[EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient)

#### <a name="read-events"></a>讀取事件

在開啟特定資料分割的 AMQP 1.0 工作階段和連結後，事件中樞服務會將事件傳遞至 AMQP 1.0 用戶端。 相較於以提取為基礎的機制 (如 HTTP GET)，這個傳遞機制能產生更高的輸送量和較低的延遲。 將事件傳送到用戶端時，每個事件資料執行個體都包含如位移和序號等有助於在事件序列上設立檢查點的重要中繼資料。

事件資料︰
* Offset
* 序號
* 主體
* 使用者屬性
* 系統屬性

您必須負責管理位移。

## <a name="next-steps"></a>後續步驟

如需事件中樞的詳細資訊，請造訪下列連結：

- 開始使用事件中心
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [事件中樞程式設計指南](event-hubs-programming-guide.md)
* [事件中樞的可用性和一致性](event-hubs-availability-and-consistency.md)
* [事件中樞常見問題集](event-hubs-faq.md)
* [事件中樞範例](event-hubs-samples.md)