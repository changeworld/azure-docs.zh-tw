---
title: 從 Apache Kafka 應用程式使用事件中樞 - Azure 事件中樞 | Microsoft Docs
description: 本文提供與 Azure 事件中樞所支援的 Apache Kafka 有關的資訊。
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: d9aa8af30d5ef5e1a985e4d73a9d4a8921ac7d45
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369585"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>從 Apache Kafka 應用程式使用 Azure 事件中樞
事件中樞提供與 Apache Kafka 相容的端點，®生產者和取用者 Api，可供大部分現有的 Apache Kafka 用戶端應用程式用來作為執行您自己的 Apache Kafka 叢集的替代方案。 事件中樞支援在1.0 和更新版本的 Apache Kafka 生產者和取用者 Api 用戶端。

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>適用於 Kafka 的事件中樞提供哪些功能？

「適用于 Apache Kafka 的事件中樞」功能提供通訊協定標 Azure 事件中樞頭，其通訊協定與針對 Apache Kafka server 1.0 版和更新版本所建立的 Apache Kafka 用戶端相容，而且支援讀取和寫入事件中樞，這相當於 Apache Kafka 主題。 

相較于現有的 Kafka 設定，您通常可以在應用程式中使用事件中樞 Kafka 端點，而不需變更程式碼：更新設定中的連接字串，以指向事件中樞所公開的 Kafka 端點，而不是指向 Kafka 叢集。 然後，您可以開始將事件從使用 Kafka 通訊協定的應用程式串流到事件中樞。 

概念上，Kafka 和事件中樞非常類似：它們都是針對串流資料所建立的資料分割記錄，因此用戶端會控制要讀取的保留記錄檔部分。 下表提供 Kafka 與事件中樞之間的概念對應。

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka 和事件中樞的概念對應

| Kafka 概念 | 事件中樞概念|
| --- | --- |
| 叢集 | 命名空間 |
| 主題 | 事件中樞 |
| 資料分割 | 資料分割|
| 取用者群組 | 取用者群組 |
| Offset | Offset|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Apache Kafka 與事件中樞之間的主要差異

雖然 [Apache Kafka](https://kafka.apache.org/) 是您通常需要安裝和操作的軟體，但事件中樞是完全受控的雲端原生服務。 沒有任何伺服器、磁片或網路可以管理及監視，也沒有任何要考慮或設定的代理程式。 您可以建立命名空間，此命名空間是具有完整功能變數名稱的端點，然後您會在該命名空間內建立事件中樞 (主題) 。 

如需關於事件中樞和命名空間的詳細資訊，請參閱[事件中樞功能](event-hubs-features.md#namespace)。 作為雲端服務，事件中樞會使用單一穩定的虛擬 IP 位址作為端點，因此用戶端不需要知道叢集中的訊息代理程式或機器。 雖然事件中樞會執行相同的通訊協定，但這項差異表示所有分割區的所有 Kafka 流量都會透過此端點以可預測的方式路由傳送，而不需要對叢集的所有代理程式進行防火牆存取。   

事件中樞的調整是由您所購買的輸送量單位數目來控制，每個輸送量單位須您每秒 1 Mb，或每秒輸入1000個事件，而在輸出中有兩倍。 當您使用 [自動](event-hubs-auto-inflate.md) 擴充功能時，事件中樞可以在達到輸送量限制時自動擴大輸送量單位;這項功能也適用于 Apache Kafka 通訊協定支援。  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Apache Kafka 適用于您工作負載的正確解決方案嗎？

使用 Apache Kafka 來建立應用程式時，瞭解 Azure 事件中樞是一群服務的一部分，其中也包含 [Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)和 [Azure 事件方格](../event-grid/overview.md)，也很有説明。 

雖然 apache Kafka 的某些商業發行版提供者可能會建議 Apache Kafka 是您所有訊息平臺需求的一站式，但事實上，Apache Kafka 並不會執行「 [競爭取用者](/azure/architecture/patterns/competing-consumers) 佇列」模式不支援「發佈-訂閱」層級上的「  [發行-訂閱](/azure/architecture/patterns/publisher-subscriber) 」，可讓訂閱者以非一般位移的伺服器評估規則來存取內送訊息，而且沒有任何設備可追蹤訊息所起始之作業的生命週期，或是將錯誤訊息 sidelining 到寄不出的信件佇列中，這些都是許多企業訊息案例的基礎

若要瞭解模式之間的差異，以及哪一種模式最適合由何種服務所涵蓋，請參閱 [Azure 指引中的非同步通訊選項](/azure/architecture/guide/technology-choices/messaging) 。 您可以使用「事件方格」或「服務匯流排」，以 Apache Kafka 使用者的身份，找出您目前為止使用 Kafka 實現的通訊路徑。 

如果您需要 Apache Kafka 的特定功能，但無法透過「適用于 Apache Kafka 的事件中樞」介面取得，或如果您的執行模式超過「 [事件中樞」配額](event-hubs-quotas.md)，您也可以 [在 Azure HDInsight 中執行原生 Apache Kafka](../hdinsight/kafka/apache-kafka-introduction.md)叢集。  

## <a name="security-and-authentication"></a>安全性和驗證
每次您發佈或取用事件中樞的事件以進行 Kafka 時，您的用戶端就會嘗試存取事件中樞資源。 您想要確保使用已授權的實體來存取資源。 搭配使用 Apache Kafka 通訊協定與用戶端時，您可以使用 SASL 機制來設定驗證和加密的設定。 使用適用于 Kafka 的事件中樞時，需要 TLS 加密 (因為與事件中樞傳輸中的所有資料都是以 TLS 加密) 。 您可以在設定檔中指定 SASL_SSL 選項來完成此操作。 

Azure 事件中樞提供多個選項來授權存取您的安全資源。 

- OAuth 2.0
- 共用存取簽章 (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
事件中樞與 Azure Active Directory (Azure AD) 整合，其提供符合 **OAuth 2.0** 規範的集中式授權伺服器。 使用 Azure AD 時，您可以使用 Azure 角色型存取控制 (Azure RBAC) ，將更細緻的許可權授與您的用戶端身分識別。 您可以將此功能與 Kafka 用戶端搭配使用，方法是指定該機制的通訊協定和**OAUTHBEARER**的**SASL_SSL** 。 如需有關設定存取範圍之 Azure 角色和層級的詳細資訊，請參閱 [使用 Azure AD 授權存取權](authorize-access-azure-active-directory.md)。

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>共用存取簽章 (SAS)
事件中樞也會提供 ** (SAS) 的共用存取 ** 簽章，以供委派存取 Kafka 資源的事件中樞。 使用 OAuth 2.0 權杖型機制來授與存取權，可提供更高的安全性，並讓您更輕鬆地使用 SAS。 內建角色也可以免除以 ACL 為基礎的授權需求，這必須由使用者維護及管理。 您可以將這項功能與 Kafka 用戶端搭配使用，方法是指定通訊協定的 **SASL_SSL** ，並使用 **純** 的機制。 

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> 將 `{YOUR.EVENTHUBS.CONNECTION.STRING}` 取代為事件中樞命名空間的連接字串。 如需取得連接字串的指示，請參閱 [取得事件中樞連接字串](event-hubs-get-connection-string.md)。 以下是範例設定： `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

> [!NOTE]
> 使用 SAS 驗證搭配 Kafka 用戶端時，重新產生 SAS 金鑰時，已建立的連線不會中斷連線。 


#### <a name="samples"></a>範例 
如需具有逐步指示的 **教學** 課程，以建立事件中樞並使用 SAS 或 OAuth 進行存取，請參閱 [快速入門：使用 Kafka 通訊協定與事件中樞進行資料串流](event-hubs-quickstart-kafka-enabled-event-hubs.md)。

如需示範如何使用 OAuth 搭配事件中樞以進行 Kafka 的更多 **範例** ，請參閱 [GitHub 上的範例](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。

## <a name="other-event-hubs-features"></a>其他事件中樞功能 

適用于 Apache Kafka 的事件中樞功能是在 Azure 事件中樞上同時提供的三種通訊協定之一，可補充 HTTP 和 AMQP。 您可以使用任何一種通訊協定來撰寫，並以任何其他通訊協定進行讀取，讓您目前的 Apache Kafka 生產者可透過 Apache Kafka 繼續發佈，但您的讀者可以利用與事件中樞的 AMQP 介面（例如 Azure 串流分析或 Azure Functions）的原生整合來獲益。 Reversely，您可以輕鬆地將 Azure 事件中樞整合至 AMQP 路由網路作為目標端點，還可透過 Apache Kafka 整合來讀取資料。  

此外，事件中樞的功能（例如 [Capture](event-hubs-capture-overview.md)）可透過 Azure Blob 儲存體和 Azure Data Lake Storage，啟用極具成本效益的長期保存，而 [異地災難](event-hubs-geo-dr.md) 復原也可與 Kafka 功能的事件中樞搭配運作。

## <a name="apache-kafka-feature-differences"></a>Apache Kafka 功能差異 

「適用于 Apache Kafka 的事件中樞」的目標是要將 Azure 事件中樞功能的存取權提供給已鎖定至 Apache Kafka API 的應用程式，否則必須由 Apache Kafka 叢集來支援。 

如先前所述，Azure 訊息提供者可為許多訊息案例提供豐富且穩固的涵蓋 [範圍，但](#is-apache-kafka-the-right-solution-for-your-workload)目前不支援透過 APACHE Kafka API 的「事件中樞」支援下列功能，我們指出所需功能的出現位置和方式。

### <a name="transactions"></a>交易

[Azure 服務匯流排](../service-bus-messaging/service-bus-transactions.md) 具有健全的交易支援，可在交易的一致性保護下傳送訊息處理至多個目標實體時，允許接收和輸出訊息和會話。 功能集不只允許在一系列的每個訊息中進行一次的處理，也可避免其他取用者不慎重新處理與 Apache Kafka 相同之訊息的風險。 服務匯流排是交易式訊息工作負載的建議服務。

### <a name="compression"></a>壓縮

Apache Kafka 的用戶端 [壓縮](https://cwiki.apache.org/confluence/display/KAFKA/Compression) 功能會將多個訊息的批次壓縮成生產者端上的單一訊息，並在取用者端將該批次解壓縮。 Apache Kafka 訊息代理程式會將批次視為特殊訊息。

這項功能在本質上可能會有 Azure 事件中樞的多重通訊協定模型，它可讓訊息（甚至是分批傳送的訊息）個別從訊息代理程式和透過任何通訊協定來取得。 

任何事件中樞事件的承載都是位元組資料流程，而內容可以使用您選擇的演算法來壓縮。 Apache Avro 編碼格式支援以原生方式壓縮。

### <a name="log-compaction"></a>記錄檔壓縮

Apache Kafka 記錄壓縮功能可讓您從磁碟分割收回所有索引鍵的最後一筆記錄，如此一來，就能有效地將 Apache Kafka 主題轉換成索引鍵/值存放區，其中最後一個新增的值會覆寫前一個值。 索引鍵/值存放區模式（即使是經常更新）在資料庫服務（例如 [Azure Cosmos DB](../cosmos-db/introduction.md)）中的支援程度較佳。

Kafka Connect 和 Kafka 串流用戶端架構會使用記錄檔壓縮功能。

### <a name="kafka-streams"></a>Kafka Streams

Kafka 資料流程是適用于串流分析的用戶端程式庫，其為 Apache Kafka 開放原始碼專案的一部分，但與 Apache Kafka 事件串流代理程式不同。 

客戶 Azure 事件中樞要求 Kafka 串流支援的最常見原因，是因為他們對 Confluent 的「ksqlDB」產品有興趣。 「ksqlDB」是一項專屬的共用來源專案，其 [授權](https://github.com/confluentinc/ksql/blob/master/LICENSE) 為沒有任何廠商提供軟體即服務、平臺即服務、基礎結構即服務，或與 Confluent 產品或服務競爭的其他類似線上服務」允許使用或提供「ksqlDB」支援。 實際上，如果您使用 ksqlDB，您必須自行操作 Kafka，否則您必須使用 Confluent 的雲端供應專案。 授權條款可能也會影響針對授權所排除的目的提供服務的 Azure 客戶。

獨立且不含 ksqlDB，Kafka 串流的功能比許多替代架構和服務的功能少，其中大部分都有內建的串流 SQL 介面，且全都與目前的 Azure 事件中樞整合：

- [Azure 串流分析](../stream-analytics/stream-analytics-introduction.md)
- [透過事件中樞捕獲 Azure Synapse Analytics () ](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Akka streams 資料流程](event-hubs-kafka-akka-streams-tutorial.md)

列出的服務和架構通常可以透過介面卡，直接從一組不同的來源取得事件串流和參考資料。 Kafka 串流只能從 Apache Kafka 取得資料，而您的分析專案則會鎖定至 Apache Kafka。 若要使用來自其他來源的資料，您必須先使用 Kafka Connect framework 將資料匯入至 Apache Kafka。

如果您必須在 Azure 上使用 Kafka 串流架構， [HDInsight 上的 Apache Kafka](../hdinsight/kafka/apache-kafka-introduction.md) 會為您提供該選項。 Apache Kafka on HDInsight 可讓您完全掌控 Apache Kafka 的所有設定層面，同時完全與 Azure 平臺的各個層面整合，從容錯/更新網域放置到網路隔離，到監視整合。 

## <a name="next-steps"></a>後續步驟
本文概略介紹了適用於 Kafka 的事件中樞。 若要深入了解，請參閱[適用於 Azure 事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)。