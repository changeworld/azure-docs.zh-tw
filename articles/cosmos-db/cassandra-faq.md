---
title: 有關 Azure Cosmos DB 之 Cassandra API 的常見問題
description: 取得 Azure Cosmos DB 的 Cassandra API 常見問題的解答。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: b467e191a8f009b85abb4866821dd64fc87ce657
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092376"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>關於 Cassandra API 的常見問題 Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

本文說明 Azure Cosmos DB 中的 Apache Cassandra 與 Cassandra API 之間的功能差異。 它也會提供有關 Azure Cosmos DB 中 Cassandra API 常見問題的解答。

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Apache Cassandra 與 Cassandra API 之間的主要差異

- Apache Cassandra 建議的分割區索引鍵大小有 100-MB 的限制。 Azure Cosmos DB 的 Cassandra API 允許每個資料分割最多 20 GB。
- Apache Cassandra 可讓您停用持久認可。 您可以跳過寫入認可記錄檔，然後直接前往 memtables。 如果節點在 memtables 排清至磁片上的 SSTables 之前停止運作，這可能會導致資料遺失。 Azure Cosmos DB 一律會執行永久性認可，以協助防止資料遺失。
- 如果工作負載牽涉到許多取代或刪除，則 Apache Cassandra 可以看到效能降低的情況。 原因是，讀取工作負載需要跳過才能提取最新的資料。 當工作負載有許多取代或刪除時，Cassandra API 不會看到降低的讀取效能。
- 在高更換工作負載的案例中，需要執行壓縮以合併磁片上的 SSTables。  (需要合併，因為 Apache Cassandra 的寫入只會附加。 多個更新會儲存為個別的 Sstable loader 專案，這些專案需要定期合併) 。 這種情況也可能導致壓縮期間的讀取效能降低。 因為 API 不會執行壓縮，所以 Cassandra API 不會對效能造成影響。
- 使用 Apache Cassandra 可以設定1的複寫因數。 但是，如果有資料的唯一節點停止運作，它會導致低可用性。 這並不是 Azure Cosmos DB 的 Cassandra API 問題，因為一律會有4個 (仲裁的複寫因數 3) 。
- 在 Apache Cassandra 中新增或移除節點需要手動介入，以及新節點上的高 CPU 使用率，而現有的節點則會將其某些權杖範圍移至新節點。 當您解除委任現有的節點時，就會發生這種情況。 不過，Cassandra API 在服務或應用程式中未觀察到任何問題時相應放大。
- 您不需要在叢集中的每個節點上設定 **num_tokens** ，如同在 Apache Cassandra 中。 Azure Cosmos DB 完全管理節點和權杖範圍。
- Cassandra API 完全受控。 您不需要在 Apache Cassandra 中使用 **nodetool** 命令，例如 repair 和解除委任。

## <a name="other-frequently-asked-questions"></a>其他常見問題

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Cassandra API 支援的通訊協定版本為何？

Azure Cosmos DB 的 Cassandra API 支援 CQL 3.x 版。 其 CQL 相容性是以公用 [Apache Cassandra GitHub 存放庫](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)為基礎。 如果您有關于支援其他通訊協定的意見反應，請透過 [使用者語音意見](https://feedback.azure.com/forums/263030-azure-cosmos-db) 反應，或傳送電子郵件給我們 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 。

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>為什麼要選擇資料表的輸送量需求？

Azure Cosmos DB 會根據您在其中建立資料表的位置，設定容器的預設輸送量： Azure 入口網站或 CQL。

Azure Cosmos DB 能提供效能和延遲上的保證，限定作業時的上限。 當引擎可以在租使用者的作業上強制執行治理時，可能會有這些保證。 設定輸送量能確保您享有保證的輸送量和延遲，因為平台會保留產能，保證作業成功。
您可以 [彈性變更輸送量](manage-scale-cassandra.md) ，以從應用程式的季節性獲益並節省成本。

輸送量概念的說明請參閱 [Azure Cosmos DB 中的要求單位](request-units.md)一文。 資料表的輸送量會平均分散到基礎實體資料分割。

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>透過 CQL 建立之資料表的輸送量為何？

Azure Cosmos DB 使用每秒的要求單位 (RU/秒) 作為提供輸送量的貨幣。 透過 CQL 建立的資料表預設具有 400 RU。 您可以從 Azure 入口網站變更 RU。

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

Azure Cosmos DB 能提供效能和延遲上的保證，限定作業時的上限。 當引擎可以在租使用者的作業上強制執行治理時，可能會有這些保證。 設定輸送量能確保您享有保證的輸送量和延遲，因為平台會保留產能，保證作業成功。

當您超過此容量時，您會收到下列錯誤訊息，指出已使用您的容量：

**0x1001 多載：無法處理要求，因為「要求速率很大」** 

查看 (的作業及其磁片區) 會造成此問題是很重要的。 您可以使用 Azure 入口網站上的計量，瞭解已耗用容量在布建容量上的耗用量。 然後，您必須確保在所有基礎分割區上幾乎平均耗用容量。 如果您看到某個資料分割耗用大部分的輸送量，您就會有工作負載的扭曲。

計量可讓您瞭解如何在數小時、數天內、每七天、跨資料分割或匯總中使用輸送量。 如需詳細資訊，請參閱[使用 Azure Cosmos DB 中的計量監控及偵錯](use-metrics.md)。

診斷記錄的說明請見 [Azure Cosmos DB 診斷記錄](./monitor-cosmos-db.md)一文。

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>主要索引鍵是否會對應至 Azure Cosmos DB 的資料分割索引鍵概念？

是，資料分割索引鍵是用來將實體放置在正確的位置。 在 Azure Cosmos DB 中，它是用來尋找實體資料分割上所儲存的正確邏輯資料分割。 資料分割概念的詳細說明請見[在 Azure Cosmos DB 中進行資料分割和調整](partitioning-overview.md)一文。 此處的重要重點是邏輯分割區不應超過 20 GB 的限制。

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>當我收到磁碟分割已滿的通知時，會發生什麼事？

Azure Cosmos DB 是以服務等級協定為基礎的系統， (SLA) 。 它提供無限制的調整，並保證延遲、輸送量、可用性及一致性。 這種無限制的儲存體是以水準相應放大的資料為基礎，使用資料分割作為主要概念。 資料分割概念的詳細說明請見[在 Azure Cosmos DB 中進行資料分割和調整](partitioning-overview.md)一文。

您應遵循每個邏輯分割區的實體或專案數目的 20 GB 限制。 為了確保您的應用程式能夠進行適當的調整，建議您「避免」  建立常用資料分割 (將所有資訊儲存在一個資料分割，然後進行查詢)。 只有當您的資料扭曲時，才會發生此錯誤：也就是說，您有一個資料分割索引鍵的大量資料 (20 GB 以上的) 。 您可以使用儲存體入口網站來尋找資料的分佈。 修正此錯誤的方法是重新建立資料表，然後選擇細微的主要 (分割區索引鍵) ，以便更妥善地散發資料。

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>我可以使用 Cassandra API 做為具有上百萬或數十億個分割區索引鍵的索引鍵值存放區嗎？

Azure Cosmos DB 可以透過相應放大儲存體以儲存無限量的資料。 此儲存體與輸送量無關。 是的，您一律可以使用 Cassandra API，方法是指定正確的主要/分割區索引鍵來儲存和取得索引鍵和值。 這些個別的金鑰會取得自己的邏輯分割區，並放在不具問題的實體分割區上方。

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>我可以使用 Cassandra API 建立多個資料表嗎？

是的，您可以使用 Cassandra API 建立多個資料表。 系統會將這些資料表各視為輸送量和儲存體的單位。

### <a name="can-i-create-more-than-one-table-in-succession"></a>我可以連續建立一個以上的資料表嗎？

Azure Cosmos DB 是適用于資料和控制平面活動的資源控管系統。 容器（例如集合和資料表）是針對指定的輸送量容量所布建的執行時間實體。 快速地連續建立這些容器不是預期的活動，而且可能會進行節流。 如果您有立即卸載或建立資料表的測試，請嘗試將它們加入空間。

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>我可以建立的資料表數目上限為何？

資料表數目並無實際限制。 如果您有大量的資料表 (其中的穩定大小總計超過 10 TB 的資料) 需要建立，而不是一般的數十或數百個，則會傳送電子郵件給 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 。

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>我可以建立的 keyspace 數目上限為何？

Keyspace 數目沒有實體限制，因為它們是中繼資料容器。 如果您有大量的 keyspace，請傳送電子郵件至 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 。

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>從一般資料表開始之後，我可以導入大量資料嗎？

是。 假設是一致的分散式分割區，則會自動管理儲存體容量，並在您推送更多資料時增加。 如此一來，您就可以放心地匯入所需的資料量，而不必管理和布建節點等。 但是，如果您預期會有許多立即的資料成長，則直接布建 [預期的輸送量](set-throughput.md) ，而不是從較低的位置開始，並立即加以增加，會更加合理。

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>我可以使用 YAML 檔案設定來設定 API 行為嗎？

Azure Cosmos DB 的 Cassandra API 會提供通訊協定層級的相容性，以執行作業。 它會讓管理、監視和設定作業變得不再複雜。 如果您是開發人員/使用者，您不必擔心可用性、標記、金鑰快取、資料列快取、bloom 篩選和其他許多設定。 Cassandra API 著重于提供您所需的讀取和寫入效能，而不會產生設定和管理的額外負荷。

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Cassandra API 是否支援節點新增、叢集狀態和節點狀態命令？

Cassandra API 可簡化容量規劃，以及回應輸送量和儲存體的彈性需求。 使用 Azure Cosmos DB，您可以布建所需的輸送量。 然後，您可以在一天內相應增加和減少任何次數，而不必擔心新增、刪除或管理節點。 您不需要使用適用于節點和叢集管理的工具。

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>使用簡單/網路等 keyspace 建立的各種設定時，會發生什麼事？

Azure Cosmos DB 針對可用性和低延遲原因提供現成的全域散發。 您不需要設定複本或其他專案。 寫入一律會在您撰寫的任何區域中認可永久仲裁，並提供效能保證。

### <a name="what-happens-with-various-settings-for-table-metadata"></a>資料表中繼資料的各種設定會發生什麼事？

Azure Cosmos DB 可提供讀取、寫入和輸送量的效能保證。 因此您不需要擔心觸及任何設定設定，也不會不慎操控它們。 這些設定包括 bloom 篩選、快取、讀取修復機會、gc_grace，以及壓縮 memtable_flush_period。

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Cassandra 資料表是否支援存留時間？

是，有支援 TTL。

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>如何監視基礎結構以及輸送量？

Azure Cosmos DB 是一種平台服務，可協助您提高產能，而不必擔心如何管理及監視基礎結構。 例如，您不需要使用各種工具來監視節點狀態、複本狀態、gc 和 OS 參數。 您只需要處理入口網站計量中可用的輸送量，就可以查看您是否正在進行節流，然後增加或減少該輸送量。 您可以：

- 監視 [sla](./monitor-cosmos-db.md)
- 使用 [計量](use-metrics.md)
- 使用 [診斷記錄](./monitor-cosmos-db.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>哪些用戶端 Sdk 可以使用 Cassandra API？

使用 CQLv3 的 Apache Cassandra SDK 用戶端驅動程式用於用戶端程式。 如果您有其他使用的驅動程式或遇到問題，請將郵件傳送至 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 。

### <a name="are-composite-partition-keys-supported"></a>是否支援複合分割區索引鍵？

是，您可以使用一般語法來建立複合分割區索引鍵。

### <a name="can-i-use-sstableloader-for-data-loading"></a>是否可以使用 sstableloader 進行資料載入？

否，不支援 sstableloader。

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>我可以將內部部署 Apache Cassandra 叢集與 Cassandra API 配對嗎？

目前，Azure Cosmos DB 具有適用于雲端環境的優化體驗，而不會有作業的額外負荷。 如果您需要配對，請傳送電子郵件至， [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 並提供您案例的描述。 我們正在努力協助將內部部署或雲端 Cassandra 叢集與 Azure Cosmos DB 的 Cassandra API 配對。

### <a name="does-the-cassandra-api-provide-full-backups"></a>Cassandra API 是否提供完整備份？

Azure Cosmos DB 提供兩個在所有 Api 之間採用四小時間隔的免費完整備份。 因此您不需要設定備份排程。 

如果您想要修改保留和頻率，請傳送電子郵件給 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 或提出支援案例。 [使用 Azure Cosmos DB 進行自動線上備份及還原](online-backup-and-restore.md)一文會提供備份功能的相關資訊。

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>當區域發生服務中斷時，Cassandra API 帳戶如何處理容錯移轉？

Cassandra API 從 Azure Cosmos DB 的全域分散式平臺借用。 為確保您的應用程式可容忍資料中心停機時間，請在 Azure 入口網站中為帳戶啟用至少一個區域。 如需詳細資訊，請參閱 [Azure Cosmos DB 的高可用性](high-availability.md)。

您可以針對帳戶加入任意數目的區域，並藉由提供容錯移轉優先順序來控制容錯移轉的目標位置。 若要使用的資料庫，您還需要在目標位置提供應用程式。 如果您這樣做，您的客戶就不會經歷停機時間。

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Cassandra API 預設會為實體的所有屬性編制索引嗎？

否。 Cassandra API 支援 [次要索引](cassandra-secondary-index.md)，其行為方式與 Apache Cassandra 類似。 根據預設，API 不會編制每個屬性的索引。  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>我可以在本機搭配使用新的 Cassandra API SDK 與模擬器嗎？

是，支援這種情況。 您可以在「 [使用 Azure Cosmos 模擬器進行本機開發和測試](local-emulator.md#cassandra-api) 」一文中找到如何啟用此功能的詳細資料。


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>如何將資料從 Apache Cassandra 叢集遷移至 Azure Cosmos DB？

您可以在 Azure Cosmos DB 教學課程中瞭解將 [您的資料移轉至 Cassandra API 帳戶](cassandra-import-data.md) 的遷移選項。


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>我可以在哪裡提供 Cassandra API 功能的意見反應？

請透過 [UserVoice 意見反應](https://feedback.azure.com/forums/263030-azure-cosmos-db)提供意見反應。

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>下一步

- 開始使用 [彈性調整 Azure Cosmos DB Cassandra API 帳戶](manage-scale-cassandra.md)。