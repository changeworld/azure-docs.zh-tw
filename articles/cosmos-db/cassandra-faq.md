---
title: 有關 Azure Cosmos DB 的 Cassandra API 常見問題
description: 取得 Azure Cosmos DB 的 Cassandra API 常見問題的解答。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 04708a307cd0eedfbe0510324930eb2327adf06e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84449731"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>有關 Azure Cosmos DB 中的 Cassandra API 常見問題

本文說明 Azure Cosmos DB 中 Apache Cassandra 和 Cassandra API 之間的功能差異。 它也會提供有關 Azure Cosmos DB 中 Cassandra API 問題的解答。

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Apache Cassandra 與 Cassandra API 之間的主要差異

- Apache Cassandra 建議在分割區索引鍵的大小上使用 100 MB 的限制。 Azure Cosmos DB 的 Cassandra API 允許每個分割區最多 20 GB。
- Apache Cassandra 可讓您停用持久認可。 您可以略過寫入認可記錄檔，並直接移至 memtables。 如果節點在 memtables 排清到磁片上的 SSTables 之前停止運作，這可能會導致資料遺失。 Azure Cosmos DB 一律會執行長期認可，以協助防止資料遺失。
- 如果工作負載牽涉到許多取代或刪除，Apache Cassandra 會看到效能降低的情況。 這是因為讀取工作負載需要略過來提取最新資料的原因。 當工作負載有許多取代或刪除時，Cassandra API 不會看到降低的讀取效能。
- 在高更換工作負載的情況下，需要執行壓縮以合併磁片上的 SSTables。 （需要合併，因為 Apache Cassandra 的寫入只會附加。 多個更新會儲存為需要定期合併的個別 Sstable loader 專案。 這種情況也可能導致壓縮期間的讀取效能降低。 Cassandra API 不會對效能造成影響，因為 API 不會執行壓縮。
- 您可以使用 Apache Cassandra 來設定複寫因數1。 不過，如果只有一個具有資料的節點停止運作，它會導致低可用性。 這不是 Azure Cosmos DB 的 Cassandra API 的問題，因為一律有複寫因數4（仲裁為3）。
- 在 Apache Cassandra 中新增或移除節點需要手動介入，以及新節點上的高 CPU 使用量，而現有節點則會將其部分權杖範圍移至新節點。 當您解除委任現有的節點時，這種情況就會相同。 不過，Cassandra API 會相應放大，而不會在服務或應用程式中觀察到任何問題。
- 您不需要在叢集中的每個節點上設定**num_tokens** ，如同 Apache Cassandra。 Azure Cosmos DB 完全管理節點和權杖範圍。
- Cassandra API 是完全受控的。 您不需要在 Apache Cassandra 中使用的**nodetool**命令，例如修復和解除委任。

## <a name="other-frequently-asked-questions"></a>其他常見問題

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Cassandra API 支援哪些通訊協定版本？

適用于 Azure Cosmos DB 的 Cassandra API 支援 CQL 3.x. x 版。 其 CQL 相容性是以公用[Apache Cassandra GitHub 存放庫](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)為基礎。 如果您有關于支援其他通訊協定的意見反應，請透過[user voice 意見](https://feedback.azure.com/forums/263030-azure-cosmos-db)反應讓我們知道，或傳送電子郵件至 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 。

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>為何需要為數據表選擇輸送量？

Azure Cosmos DB 會根據您在其中建立資料表的位置，為您的容器設定預設輸送量： Azure 入口網站或 CQL。

Azure Cosmos DB 能提供效能和延遲上的保證，限定作業時的上限。 當引擎可以對租使用者的作業強制執行控管時，可能會有這些保證。 設定輸送量能確保您享有保證的輸送量和延遲，因為平台會保留產能，保證作業成功。
您可以[彈性變更輸送量](manage-scale-cassandra.md)，以受益于應用程式的季節性並節省成本。

輸送量概念的說明請參閱 [Azure Cosmos DB 中的要求單位](request-units.md)一文。 資料表的輸送量會平均分散到基礎實體分割區。

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>透過 CQL 建立之資料表的輸送量為何？

Azure Cosmos DB 使用每秒的要求單位（RU/秒）做為提供輸送量的貨幣。 根據預設，透過 CQL 建立的資料表具有 400 RU。 您可以從 Azure 入口網站變更 RU。

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

Azure Cosmos DB 能提供效能和延遲上的保證，限定作業時的上限。 當引擎可以對租使用者的作業強制執行控管時，可能會有這些保證。 設定輸送量能確保您享有保證的輸送量和延遲，因為平台會保留產能，保證作業成功。

當您超過此容量時，您會收到下列錯誤訊息，指出您的容量已用完：

**0x1001 超載：無法處理要求，因為「要求速率很大」** 

請務必查看哪些作業（和其磁片區）造成此問題。 您可以使用 Azure 入口網站上的計量，取得已布建容量的已耗用容量相關概念。 接著，您必須確保在所有基礎資料分割上幾乎平均地耗用容量。 如果您看到某個分割區耗用大部分的輸送量，您就會有工作負載的扭曲。

計量可用來顯示輸送量在數小時內如何使用，以及跨資料分割或匯總的每七天。 如需詳細資訊，請參閱[使用 Azure Cosmos DB 中的計量監控及偵錯](use-metrics.md)。

診斷記錄的說明請見 [Azure Cosmos DB 診斷記錄](logging.md)一文。

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>主要索引鍵是否會對應至 Azure Cosmos DB 的資料分割索引鍵概念？

是，資料分割索引鍵是用來將實體放在正確的位置。 在 Azure Cosmos DB 中，它是用來尋找儲存在實體資料分割上的正確邏輯資料分割。 資料分割概念的詳細說明請見[在 Azure Cosmos DB 中進行資料分割和調整](partition-data.md)一文。 此處的重要重點是邏輯分割區不應超過 10 GB 的限制。

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>當我收到資料分割已滿的通知時，會發生什麼事？

Azure Cosmos DB 是以服務等級協定（SLA）為基礎的系統。 它提供無限制的規模，並保證延遲、輸送量、可用性和一致性。 這種無限制的儲存體是以水準向外延展資料為基礎，使用分割做為關鍵概念。 資料分割概念的詳細說明請見[在 Azure Cosmos DB 中進行資料分割和調整](partition-data.md)一文。

您應該遵守每個邏輯分割區的實體或專案數目的 10 GB 限制。 為了確保您的應用程式能夠進行適當的調整，建議您「避免」** 建立常用資料分割 (將所有資訊儲存在一個資料分割，然後進行查詢)。 只有當您的資料扭曲時，才會發生此錯誤：也就是說，您有許多資料用於一個分割區索引鍵（超過 10 &nbsp; GB）。 您可以使用儲存體入口網站來尋找資料的散發。 修正此錯誤的方法是重新建立資料表，然後選擇細微的主要（資料分割索引鍵），這可允許更好的資料散發。

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>我可以使用 Cassandra API 做為具有百萬或數十億個數據分割索引鍵的金鑰值存放區嗎？

Azure Cosmos DB 可以透過相應放大儲存體以儲存無限量的資料。 此儲存體與輸送量無關。 是，您隨時都可以使用 Cassandra API 來儲存和取出索引鍵和值，方法是指定正確的主要/分割區索引鍵。 這些個別的索引鍵會取得自己的邏輯分割區，並在沒有問題的情況下放在實體分割區。

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>我可以使用 Cassandra API 建立一個以上的資料表嗎？

是，您可以使用 Cassandra API 建立一個以上的資料表。 系統會將這些資料表各視為輸送量和儲存體的單位。

### <a name="can-i-create-more-than-one-table-in-succession"></a>我可以連續建立多份資料表嗎？

Azure Cosmos DB 是適用于資料和控制平面活動的資源控管系統。 容器（例如集合和資料表）是針對給定的輸送量容量所布建的執行時間實體。 快速地連續建立這些容器不是預期的活動，可能會進行節流處理。 如果您有立即卸載或建立資料表的測試，請嘗試將它們放在外。

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>我可以建立的資料表數目上限為何？

資料表數目沒有實體限制。 如果您有大量的資料表（穩定大小總計超過 10 TB 的資料）需要建立（而不是一般的數十或數百個），請傳送電子郵件至 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 。

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>我可以建立的 keyspace 數目上限為何？

Keyspace 數目沒有實體限制，因為它們是中繼資料容器。 如果您有大量的 keyspace，請傳送電子郵件至 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 。

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>從一般資料表開始之後，我可以導入大量資料嗎？

是。 假設有一致的分散式分割區，儲存容量會自動受到管理，並隨著您推送更多資料而增加。 因此，您可以放心地匯入您所需的資料，而不需要管理和布建節點等等。 但是，如果您預期會有許多立即的資料成長，請直接布建[預期的輸送量](set-throughput.md)，而不是從較低的速度開始，然後立即增加。

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>我可以使用 YAML 檔案設定來設定 API 行為嗎？

Azure Cosmos DB 的 Cassandra API 可為執行中的作業提供通訊協定層級的相容性。 它會讓管理、監視和設定作業變得不再複雜。 身為開發人員/使用者，您不需要擔心可用性、標記、金鑰快取、資料列快取、bloom 篩選，以及許多其他設定。 Cassandra API 著重于提供您所需的讀取和寫入效能，而不會產生設定和管理的額外負荷。

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Cassandra API 是否支援節點新增、叢集狀態和節點狀態命令？

Cassandra API 可簡化容量規劃，並回應輸送量和儲存體的彈性需求。 有了 Azure Cosmos DB，您就可以布建所需的輸送量。 然後您可以在一天內相應增加或減少任何次數，而不必擔心新增、刪除或管理節點。 您不需要使用適用于節點和叢集管理的工具。

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>Keyspace 建立的各種設定（例如 simple/network）會發生什麼事？

Azure Cosmos DB 提供現成的全域散發，以供可用性和低延遲的原因。 您不需要設定複本或其他專案。 寫入一律會在您撰寫的任何區域中認可永久仲裁，並提供效能保證。

### <a name="what-happens-with-various-settings-for-table-metadata"></a>資料表中繼資料的各種設定會發生什麼事？

Azure Cosmos DB 提供讀取、寫入和輸送量的效能保證。 因此您不需要擔心觸及任何設定的問題，也不會意外地操作它們。 這些設定包括 bloom 篩選、快取、讀取修復機會、gc_grace 和壓縮 memtable_flush_period。

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Cassandra 資料表是否支援存留時間？

是，有支援 TTL。

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>如何使用輸送量來監視基礎結構？

Azure Cosmos DB 是一種平台服務，可協助您提高產能，而不必擔心如何管理及監視基礎結構。 例如，您不需要稍早使用各種工具來監視節點狀態、複本狀態、gc 和 OS 參數。 您只需要處理入口網站計量中可用的輸送量，即可查看是否受到節流，然後增加或減少該輸送量。 您可以：

- 監視[sla](monitor-accounts.md)
- 使用[計量](use-metrics.md)
- 使用[診斷記錄](logging.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>哪些用戶端 Sdk 可以與 Cassandra API 搭配使用？

使用 CQLv3 的 Apache Cassandra SDK 用戶端驅動程式會用於用戶端程式。 如果您使用其他驅動程式，或如果您遇到問題，請傳送電子郵件至 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 。

### <a name="are-composite-partition-keys-supported"></a>是否支援複合資料分割索引鍵？

是，您可以使用一般語法來建立複合資料分割索引鍵。

### <a name="can-i-use-sstableloader-for-data-loading"></a>我可以使用 sstableloader 來載入資料嗎？

否，不支援 sstableloader。

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>我可以將內部部署 Apache Cassandra 叢集與 Cassandra API 配對嗎？

目前，Azure Cosmos DB 對於雲端環境有優化的體驗，而不會產生作業的額外負荷。 如果您需要配對，請傳送電子郵件至， [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 並提供您案例的描述。 我們正致力於提供服務，以協助將內部部署或雲端 Cassandra 叢集與 Azure Cosmos DB 的 Cassandra API 配對。

### <a name="does-the-cassandra-api-provide-full-backups"></a>Cassandra API 是否提供完整備份？

Azure Cosmos DB 提供兩個在所有 Api 的四小時間隔內執行的免費完整備份。 因此，您不需要設定備份排程。 

如果您想要修改保留和頻率，請傳送電子郵件至 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 或提出支援案例。 [使用 Azure Cosmos DB 進行自動線上備份及還原](online-backup-and-restore.md)一文會提供備份功能的相關資訊。

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>當區域發生服務中斷時，Cassandra API 帳戶如何處理容錯移轉？

Cassandra API 從 Azure Cosmos DB 的全域分散式平臺借用。 若要確保您的應用程式可以容忍資料中心停機時間，請在 Azure 入口網站中為該帳戶至少啟用一個區域。 如需詳細資訊，請參閱[具有 Azure Cosmos DB 的高可用性](high-availability.md)。

您可以針對帳戶加入任意數目的區域，並藉由提供容錯移轉優先順序來控制容錯移轉的目標位置。 若要使用的資料庫，您還需要在目標位置提供應用程式。 如果您這樣做，您的客戶就不會經歷停機時間。

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Cassandra API 預設會為實體的所有屬性編制索引嗎？

否。 Cassandra API 支援[次要索引](cassandra-secondary-index.md)，其運作方式類似于 Apache Cassandra。 根據預設，API 不會為每個屬性編制索引。  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>我可以在本機搭配使用新的 Cassandra API SDK 與模擬器嗎？

是，支援這種情況。 如需如何啟用此功能的詳細資訊，[請參閱使用 Azure Cosmos 模擬器進行本機開發和測試](local-emulator.md#cassandra-api)一文。


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>如何將資料從 Apache Cassandra 叢集遷移至 Azure Cosmos DB？

您可以在 Azure Cosmos DB 教學課程中，閱讀將[您的資料移轉至 Cassandra API 帳戶](cassandra-import-data.md)中有關遷移選項的資訊。


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>我可以在何處提供有關 Cassandra API 功能的意見反應？

請透過 [UserVoice 意見反應](https://feedback.azure.com/forums/263030-azure-cosmos-db)提供意見反應。

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>後續步驟

- 開始使用[彈性調整 Azure Cosmos DB Cassandra API 帳戶](manage-scale-cassandra.md)。
