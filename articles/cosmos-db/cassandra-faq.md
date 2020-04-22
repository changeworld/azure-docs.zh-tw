---
title: 關於卡桑德拉的 Azure 宇宙 DB API 的常見問題。
description: 獲取有關 Cassandra 的 Azure Cosmos DB API 的常見問題的解答。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727381"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>關於卡桑德拉的 Azure 宇宙 DB API 的常見問題

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>阿帕奇·卡桑德拉和卡桑德拉 API 之間有哪些主要區別?

- Apache Cassandra 建議對分區密鑰的大小限制 100MB。 Cassandra API 允許每個分區最多 10GB。
- Apache Cassandra 允許您禁用持久提交 - 即跳過寫入提交日誌並直接轉到 Memtable。 如果節點在將 Memtables 刷新到磁碟上的 SStables 之前出現故障,則可能導致資料丟失。 Cosmos DB 始終執行持久提交,因此您永遠不會丟失數據。
- 如果工作負載涉及大量替換和/或刪除,Apache Cassandra 的性能可能會降低。 原因是讀取工作負載需要跳過以獲取最新數據的墓碑。 當工作負載有很多替換和/或刪除時,Cassandra API 的讀取性能不會降低。
- 在高替換工作負載方案中,需要運行壓縮以在磁碟上合併 SSTable(因為 Apache Cassandra 的寫入僅追加,因此多個更新儲存為需要定期合併的單個 SSTable 項目)。 這還可能導致壓縮期間的讀取性能降低。 這在 Cassandra API 中不會發生,因為它不實現壓實。
- 使用 Apache Cassandra 可以設置複製因數 1。 但是,如果只有具有數據的節點出現故障,則會導致可用性低。 這不是 Azure Cosmos DB Cassandra API 的問題,因為複製因數始終為 4(仲裁為 3)。
- 在 Apache Cassandra 中添加/刪除節點需要大量的手動干預,但還需要在新節點上使用高 CPU,而現有節點會將其權杖範圍移動到新節點。 停用現有節點時,情況相同。 但是,在 Azure Cosmos DB Cassandra API 中,擴展將無縫地完成,在服務/應用程式中沒有任何問題。
- 無需像 Apache Cassandra 那樣在群集中的每個節點上設置num_tokens。 節點和令牌範圍完全由Cosmos DB管理。
- Azure Cosmos DB Cassandra API 已完全管理,因此您不需要 Apache Cassandra 中使用的節點工具命令(如修復、停用等)。

## <a name="other-frequently-asked-questions"></a>其他常見問題

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Azure 宇宙 DB Cassandra API 支援的協定版本是什麼? 是否打算支援其他通訊協定？

Azure 宇宙 DB Cassandra API 支援 CQL 版本 3.x。 它的CQL相容性是基於公共[阿帕契卡桑德拉GitHub儲存庫](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)。 如果您有關於支援其他通訊協定的意見反應，請透過 [User Voice 意見反應](https://feedback.azure.com/forums/263030-azure-cosmos-db)讓我們知道，或傳送電子郵件到 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)。

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>為什麼必須要為資料表選擇輸送量？

Azure Cosmos DB 會根據您是透過入口網站還是 CQL 建立資料表，為您的容器設定預設輸送量。
Azure Cosmos DB 能提供效能和延遲上的保證，限定作業時的上限。 當引擎能針對租用戶作業強制執行控管時，這項保證是可行的。 設定輸送量能確保您享有保證的輸送量和延遲，因為平台會保留產能，保證作業成功。
您可以[彈性地更改輸送量](manage-scale-cassandra.md),以受益於應用程式的季節性並節省成本。

輸送量概念的說明請參閱 [Azure Cosmos DB 中的要求單位](request-units.md)一文。 資料表的輸送量會平均分散到基礎的實體分割區。

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>透過 CQL 建立的資料表，其預設「RU/秒」為何？ 如果我需要變更此輸送量，該怎麼做？

Azure Cosmos DB 會使用每秒的要求單位數 (RU/秒) 做為用來提供輸送量的貨幣。 透過 CQL 所建立的資料表有 400 RU。 您可以從入口網站變更 RU。

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>耗盡輸送量會發生什麼事？

Azure Cosmos DB 能提供效能和延遲上的保證，限定作業時的上限。 當引擎能針對租用戶作業強制執行控管時，這項保證是可行的。 透過設定輸送量即可獲得保證，確保您享有保證的輸送量和延遲，因為平台會保留產能，保證作業成功。
當您超過此容量時，將會收到多載錯誤訊息，指出您已耗盡使用容量。
0x1001 多載：無法處理要求，因為「要求速率太大」。 在此時，請務必查看是哪些作業和其磁碟區導致此問題。 您可以透過入口網站上的計量，了解超過所佈建容量的已用容量。 然後，您必須確保您會在所有基礎分割區以近乎平均的方式取用容量。 如果您看到輸送量大多是取自某個分割區，則表示您的工作負載有所傾斜。

我們提供的計量可讓您知道所有分割區在幾小時、幾天以及每七天的輸送量使用情形，或其彙總。 如需詳細資訊，請參閱[使用 Azure Cosmos DB 中的計量監控及偵錯](use-metrics.md)。

診斷記錄的說明請見 [Azure Cosmos DB 診斷記錄](logging.md)一文。

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>主要索引鍵是否會對應至 Azure Cosmos DB 的資料分割索引鍵概念？

是，資料分割索引鍵可用來將實體放在正確的位置。 在 Azure Cosmos DB 中，我們會用它來尋找實體資料分割上所儲存的正確邏輯資料分割。 資料分割概念的詳細說明請見[在 Azure Cosmos DB 中進行資料分割和調整](partition-data.md)一文。 這裡的重點是，邏輯資料分割目前不應超過 10 GB 的限制。

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>如果我收到「配額已滿」通知，內容指出資料分割已滿，會發生什麼事？

Azure Cosmos DB 是 SLA 型系統，它提供無限制的調整和延遲、輸送量、可用性、一致性保證。 此無限制的儲存是根據使用資料分割做為重要概念的水平方式資料相應放大。 資料分割概念的詳細說明請見[在 Azure Cosmos DB 中進行資料分割和調整](partition-data.md)一文。

您應該遵守實體數目或每個邏輯資料分割之項目數的 10 GB 限制。 為了確保您的應用程式能夠進行適當的調整，建議您「避免」** 建立常用資料分割 (將所有資訊儲存在一個資料分割，然後進行查詢)。 當您的資料分佈有所傾斜：也就是說，如果您在一個資料分割索引鍵放了大量資料 (超過 10&nbsp;GB) 時才會發生這個錯誤。 您可以使用儲存體入口網站來找到資料的分佈。 若要修正此錯誤，方法是重新建立資料表，然後選擇細微的主要資料分割索引鍵，以讓資料分佈情形更加適當。

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>是否能將 Cassandra API 做為索引鍵值存放區，以存放數百萬或數十億個個別的資料分割索引鍵？

Azure Cosmos DB 可以透過相應放大儲存體以儲存無限量的資料。 這與輸送量無關。 是，您一律可以只使用 Cassandra API，透過指定正確的主要/資料分割索引鍵來儲存和擷取索引鍵/值。 這些個別的索引鍵都會有自己的邏輯資料分割，並且是以實體資料分割為基礎的，因此不會有問題。

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>是否能夠使用 Azure Cosmos DB 的 Apache Cassandra API 建立多個資料表？

是，您可以使用 Apache Cassandra API 建立多個資料表。 系統會將這些資料表各視為輸送量和儲存體的單位。

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>是否能夠連續建立多個資料表？

Azure Cosmos DB 是適用於資料和控制平面活動的資源所管理的系統。 集合和資料表等容器是針對給定的輸送量容量所佈建的執行階段實體。 快速地連續建立這些容器並非預料中的活動，因此會受到節流。 如果您要進行會立即卸除/建立資料表的測試，請嘗試將這些資料表分開。

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>可建立的資料表數目上限為何？

表數沒有物理限制,[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)如果您有大量表(總穩定大小超過 10 TB 的數據)需要從通常的 10s 或 100s 創建,請寄送電子郵件。

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>可建立的 keyspace 數目上限為何？

由於金鑰空間是元數據容器,因此對鍵空間的數量沒有物理限制,如果您由於某種原因擁有大量鍵[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)空間,請發送電子郵件。

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>從一般資料表開始後，有可能在其中放入大量資料嗎？

是的,假設統一分佈的分區,存儲容量會自動管理,並在您推送更多數據時增加。 因此，您可以放心地匯入您所需的資料量，而不必管理和佈建節點等。 但是,如果您預計數據會立即增長,則直接[預配預期的輸送量](set-throughput.md)而不是立即開始降低和增加吞吐量更有意義。

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>是否能夠提供 yaml 檔案設定，以設定「Azure Cosmos DB 的 Apache Cassandra API」的行為？

Azure Cosmos DB 的 Apache Cassandra API 是一種平台服務。 它可提供通訊協定層級的相容性，以供執行作業。 它會讓管理、監視和設定作業變得不再複雜。 身為開發人員/使用者，您不必擔心可用性、標記、金鑰快取、資料列快取、Bloom 篩選和諸多其他設定。 Azure Cosmos DB 的 Apache Cassandra API 著重在為您提供所需的讀取和寫入效能，而不會有設定和管理的額外負荷。

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Azure Cosmos DB 的 Apache Cassandra API 是否支援節點新增/叢集狀態/節點狀態命令？

Apache Cassandra API 是一種平台服務，可讓您輕鬆地規劃容量並回應輸送量和儲存體的彈性要求。 藉由 Azure Cosmos DB，您可以佈建所需的輸送量。 然後，您可以在一天之中不限次數地將此輸送量相應增加和減少，而不必擔心如何新增/刪除節點或進行管理。 這也表示您不需要使用節點、叢集管理工具。

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>各種組態設定會對 keyspace 的建立 (例如簡單/網路) 造成什麼影響？

基於可用性和低延遲的考量，Azure Cosmos DB 預設會提供全域發佈。 您不需要設定複本或其他項目。 所有寫入始終在您編寫的任何區域中執行持久仲裁,同時提供性能保證。

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>各種設定會對資料表中繼資料 (例如 Bloom 篩選、快取、讀取修復變更、gc_grace 和 compression memtable_flush_period 等) 造成什麼影響？

Azure Cosmos DB 會為讀取/寫入和輸送量提供效能，讓您不必接觸任何組態設定，也不會意外地操作到它們。

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Cassandra 資料表是否支援存留時間 (TTL)？

是，有支援 TTL。

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>是否有辦法透過各種工具及早監視節點狀態、複本狀態、gc 和 OS 參數？ 現在需要監視哪些項目？

Azure Cosmos DB 是一種平台服務，可協助您提高產能，而不必擔心如何管理及監視基礎結構。 您只需要處理輸送量，如果您遇到節流情形，您可以在入口網站的計量中找到輸送量，並將該輸送量予以增加或減少。
監視 [SLA](monitor-accounts.md)。
使用[計量](use-metrics.md)。使用[診斷記錄](logging.md)。

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>哪些用戶端 SDK 可以搭配 Azure Cosmos DB 的 Apache Cassandra API 來運作？

阿帕契卡桑德拉 SDK 的用戶端驅動程式使用 CQLv3 用於用戶端程式。 如果您有其他驅動程式,您使用或如果您面臨問題,請傳送信件[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)到 。

### <a name="is-composite-partition-key-supported"></a>是否支援複合資料分割索引鍵？

是，您可以使用一般語法來建立複合的資料分割索引鍵。

### <a name="can-i-use-sstableloader-for-data-loading"></a>我可以使用穩定載入器進行數據載入嗎?

否,不支持穩定載入器。

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>本地 Apache Cassandra 群集是否可以與 Azure Cosmos DB 的卡桑德拉 API 配對?

目前，Azure Cosmos DB 具有最佳化的雲端環境體驗，而不會產生作業負荷。 如果需要配對,請寄送郵件[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com), 並說明您的方案。 我們正在努力提供説明將本地/不同雲卡桑德拉群集與 Cosomos DB 的卡桑德拉 API 配對。

### <a name="does-cassandra-api-provide-full-backups"></a>Cassandra API 是否會提供完整備份？

Azure Cosmos DB 會提供兩個免費的完整備份，並於當天以四個小時為間隔針對所有 API 來擷取。 這可確保您不需要設定備份排程和其他項目。
如果要修改保留率和頻率,請發送電子郵件或[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)引發支援案例。 [使用 Azure Cosmos DB 進行自動線上備份及還原](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)一文會提供備份功能的相關資訊。

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>當區域發生服務中斷時，Cassandra API 帳戶如何處理容錯移轉？

Azure Cosmos DB Cassandra API 會藉助 Azure Cosmos DB 的全域分散式平台。 若要確保您的應用程式可以容許資料中心停機，請在 Azure Cosmos DB 入口網站中針對該帳戶至少啟用一個以上的區域，[使用多區域 Azure Cosmos DB 帳戶進行開發](high-availability.md)。 您可以使用入口網站來設定區域的優先順序，[使用多區域 Azure Cosmos DB 帳戶進行開發](high-availability.md)。

您可以針對帳戶加入任意數目的區域，並藉由提供容錯移轉優先順序來控制容錯移轉的目標位置。 若要使用的資料庫，您還需要在目標位置提供應用程式。 如果您這樣做，您的客戶就不會經歷停機時間。

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API 預設會為實體的所有屬性編製索引嗎？

否。 Cassandra API 支援[二級索引](cassandra-secondary-index.md),其活動方式與 Apache Cassandra 非常相似。 默認情況下,它不索引每個屬性。  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>我可以在本機搭配使用新的 Cassandra API SDK 與模擬器嗎？

是的,這是支援的。 您可以[找到](local-emulator.md#cassandra-api)如何開啟此功能的詳細資訊


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>如何將資料從其 Apache Cassandra 群集遷移到 Cosmos DB?

您可以在此處閱讀有關移轉選項[的資訊](cassandra-import-data.md)。


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>一般 Cassandra API 的某項功能運作情形和今天不同，我可以在哪裡提供意見反應？

請透過 [UserVoice 意見反應](https://feedback.azure.com/forums/263030-azure-cosmos-db)提供意見反應。

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>後續步驟

- 開始彈性[延伸 Azure 宇宙 DB Cassandra API 帳號](manage-scale-cassandra.md)。
