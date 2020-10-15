---
title: 使用 Advisor 改善 Azure 應用程式的效能
description: 使用 Azure Advisor 中的效能建議來改善業務關鍵應用程式的速度和回應能力。
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 44252171a714acec0a9c0e83c9272b2f845560b3
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077808"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>使用 Azure Advisor 來改善 Azure 應用程式的效能

Azure Advisor 中的效能建議有助於改善業務關鍵應用程式的速度和回應能力。 您可以在 Advisor 儀表板的 [效能]**** 索引標籤上，取得 Advisor 的效能建議。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>縮短流量管理員設定檔的 DNS 存留時間，以更快速容錯移轉至狀況良好的端點

您可以使用 Azure 流量管理員設定檔上的生存 [時間 (TTL) 設定](../traffic-manager/traffic-manager-performance-considerations.md) ，以指定在指定端點停止回應查詢時切換端點的速度。 如果您減少 TTL 值，用戶端會以更快的速度路由傳送至正常運作的端點。

Azure Advisor 識別已設定較長 TTL 的流量管理員設定檔。 建議您將 TTL 設定為20秒或60秒，這取決於設定檔是否設定為 [快速容錯移轉](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)。

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>使用 SQL Database Advisor (暫時停用，以改善資料庫效能) 

Azure Advisor 可針對所有的 Azure 資源提供一致的合併建議檢視。 它會與 SQL Database Advisor 整合，以提供改進資料庫效能的建議。SQL Database Advisor 藉由分析您的使用量歷程記錄來評估您資料庫的效能。 然後，它會提供最適合用於執行資料庫之一般工作負載的建議。

> [!NOTE]
> 在您可以取得建議之前，您的資料庫需要大約一周的使用時間，而那一周內需要有一些一致的活動。 相較於隨機蹦出的活動，一致的查詢模式更有利於 SQL Database Advisor 最佳化。

如需詳細資訊，請參閱 [SQL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md)。

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>將儲存體用戶端程式庫升級到最新版本，以提升可靠性和效能

儲存體用戶端程式庫 SDK 的最新版本包含客戶回報的問題修正，並可透過我們的 QA 流程主動識別。 最新版本也提供可靠性和效能優化，以及可改善使用 Azure 儲存體之整體體驗的新功能。 如果您使用的是過時版本，Advisor 會提供升級至最新版 SDK 所需的建議和步驟。 這些建議適用于支援的語言： c + + 和 .NET。

## <a name="improve-app-service-performance-and-reliability"></a>改善 App Service 的效能和可靠性

Azure Advisor 整合建議以改善您的 App Service 體驗，並探索相關的平臺功能。 App Service 建議的範例包括：
* 偵測應用程式執行時間耗盡記憶體或 CPU 資源的實例，並具有緩和選項。
* 偵測共置資源（例如 web 應用程式和資料庫）可改善效能並降低成本的實例。

如需詳細資訊，請參閱 [Azure App Service 的最佳做法](../app-service/app-service-best-practices.md)。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>使用受控磁片來防止磁片 i/o 節流

Advisor 會識別屬於儲存體帳戶的虛擬機器，而該儲存體帳戶會達到其擴充性目標。 這種情況讓這些 VM 很可能會執行 I/O 節流。 Advisor 會建議他們使用受控磁片，以避免效能降低。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>使用進階儲存體以改善虛擬機器磁碟的效能和可靠性

Advisor 會使用標準磁片來識別儲存體帳戶上具有大量交易的虛擬機器。 建議您升級至 premium 磁片。 

針對執行時需要大量 I/O 之工作負載的虛擬機器，「Azure 進階儲存體」可提供高效能、低延遲的磁碟支援。 使用進階儲存體帳戶的虛擬機器磁片會將資料儲存在固態硬碟 (Ssd) 。 為了讓您的應用程式達到最佳效能，建議您將需要高 IOPS 的虛擬機器磁片遷移至進階儲存體。

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>移除 Azure Synapse Analytics 資料表上的資料扭曲以提升查詢效能

當您執行工作負載時，資料扭曲可能會造成不必要的資料移動或資源瓶頸。 Advisor 偵測到分佈資料扭曲大於15%。 建議您重新發佈您的資料，並重新流覽您的資料表散發索引鍵選擇。 若要深入了解如何識別和移除扭曲，請參閱[針對扭曲進行疑難排解](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)。

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>在 Azure Synapse Analytics 資料表中建立或更新過期的資料表統計資料，以提升查詢效能

Advisor 會識別沒有最新 [資料表統計資料](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) 的資料表，並建議建立或更新統計資料。 Azure Synapse Analytics 中的查詢最佳化工具會使用最新的統計資料，來估計查詢結果中的基數或資料列數目。 這些評估可讓查詢最佳化工具建立查詢計劃，以提供最快的效能。

## <a name="improve-mysql-connection-management"></a>改善 MySQL 連線管理

Advisor 分析可指出連接至 MySQL 伺服器的應用程式可能無法有效率地管理連接。 這種情況可能會導致不必要的資源耗用量和整體的應用程式延遲。 若要改善連線管理，建議您減少短期連線的數目，並排除不必要的閒置連線。 您可以藉由設定伺服器端連接共用器（例如 ProxySQL）來進行這些改進。


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>擴大以優化 Azure Synapse Analytics 資料表的快取使用率，以提升查詢效能

Azure Advisor 會偵測您的 Azure Synapse Analytics 資料表是否有較高的快取使用百分比和較低的點擊百分比。 這種情況表示快取收回率很高，可能會影響 Azure Synapse Analytics 實例的效能。 Advisor 建議您擴大 Azure Synapse Analytics 實例，以確保您為您的工作負載配置足夠的快取容量。

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>將 Azure Synapse Analytics 資料表轉換為複寫資料表，以提升查詢效能

Advisor 會識別不是複寫資料表的資料表，但可以從轉換中獲益。 建議您轉換這些資料表。 建議的依據：
- 複寫資料表的大小。 
- 行數。 
- 資料表散發類型。 
- Azure Synapse Analytics 資料表上的資料分割數目。 

在內容的建議中，可能會提供其他啟發學習法。 若要深入瞭解如何判斷這項建議，請參閱 [Azure Synapse Analytics 建議](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables)。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>將您的儲存體帳戶遷移至 Azure Resource Manager，以取得最新的 Azure 功能

將您的儲存體帳戶部署模型遷移至 Azure Resource Manager，以利用：
- 範本部署。
- 其他安全性選項。
- 升級至 GPv2 帳戶的能力，讓您可以使用最新的 Azure 儲存體功能。 

Advisor 會識別任何使用傳統部署模型的獨立儲存體帳戶，並建議遷移至 Resource Manager 部署模型。

> [!NOTE]
> Azure 監視器中的傳統警示已于2019年8月淘汰。 建議您將傳統儲存體帳戶升級為使用 Resource Manager，以在新平台上保留警示功能。 如需詳細資訊，請參閱 [傳統警示淘汰](../azure-monitor/platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)。

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>設計您的儲存體帳戶，以避免達到最大訂用帳戶限制

Azure 區域最多可支援每個訂用帳戶250個儲存體帳戶。 達到此限制之後，您就無法在該區域/訂用帳戶組合中建立儲存體帳戶。 Advisor 會檢查您的訂用帳戶，並提供建議，讓您針對接近達到上限的任何訂用帳戶/區域，設計較少的儲存體帳戶。

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>請考慮增加 VPN 閘道 SKU 的大小，以解決高 P2S 的使用

每個 Azure VPN 閘道 SKU 只能支援指定數目的並行 P2S 連接。 如果您的連線計數接近閘道限制，其他連線嘗試可能會失敗。 如果您增加閘道的大小，您將能夠支援更多的並行 P2S 使用者。 Advisor 提供增加閘道大小的建議和指示。

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>請考慮增加 VPN 閘道 SKU 的大小以解決高 CPU

在高流量負載下，您的 VPN 閘道可能會因為高 CPU 而捨棄封包。 請考慮升級您的 VPN 閘道 SKU。 增加 VPN 閘道的大小，可確保不會因為高 CPU 而中斷連線。 Advisor 提供可主動解決此問題的建議。 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>增加載入時的批次大小，以最大化載入輸送量、資料壓縮和查詢效能

Advisor 會偵測到您是否可以在載入至資料庫時增加批次大小，以增加負載效能和輸送量。 您可以考慮使用 COPY 語句。 如果您無法使用 COPY 語句，請考慮在使用 SQLBulkCopy API 或 BCP 等載入公用程式時，增加批次大小。 良好的一般規則是使用介於100000到1000000個數據列之間的批次大小。 增加批次大小會增加載入輸送量、資料壓縮和查詢效能。

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>共置相同區域中的儲存體帳戶，以將載入時的延遲降至最低

Advisor 會偵測您是否是從不同于 SQL 集區的區域載入。 請考慮從與您的 SQL 集區位於相同區域的儲存體帳戶載入，以將載入資料時的延遲降至最低。 這種變更有助於將延遲降至最低，並提高負載效能。

## <a name="use-a-supported-kubernetes-version"></a>使用支援的 Kubernetes 版本

Advisor 偵測到不支援的 Kubernetes 版本。

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>優化適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫和適用於 MariaDB 的 Azure 資料庫伺服器的效能

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>使用 CPU 瓶頸來修正適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫和適用於 MariaDB 的 Azure 資料庫伺服器的 CPU 壓力
長時間 CPU 的高使用率可能會導致工作負載的查詢效能變慢。 增加 CPU 大小將有助於優化資料庫查詢的執行時間，並改善整體效能。 Advisor 會識別高 CPU 使用率的伺服器，這些伺服器可能會執行 CPU 限制的工作負載，並建議調整您的計算。

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>降低適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫和適用於 MariaDB 的 Azure 資料庫伺服器上的記憶體限制，或移至記憶體優化的 SKU
低快取點擊率可能會導致查詢效能變慢並增加 IOPS。 這種狀況可能是由不正確的查詢計劃或記憶體密集型工作負載所造成。 修正查詢計劃或增加適用於 PostgreSQL 的 Azure 資料庫、適用於 MySQL 的 Azure 資料庫或適用於 MariaDB 的 Azure 資料庫伺服器 [的記憶體](../postgresql/concepts-pricing-tiers.md) ，將有助於將資料庫工作負載的執行優化。 Azure Advisor 識別受此高緩衝集區變換影響的伺服器。 建議您採取下列其中一項動作： 
- 修正查詢計劃
- 移至具有更多記憶體的 SKU 
- 增加儲存體大小以取得更多 IOPS。

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>使用適用於 MySQL 的 Azure 資料庫或適用於 PostgreSQL 的 Azure 資料庫讀取複本來向外延展讀取大量讀取的工作負載
建議程式會使用以工作負載為基礎的啟發學習法，例如過去七天內對伺服器寫入的讀取比率，以識別大量讀取的工作負載。 具有較高讀取/寫入比率的適用於 PostgreSQL 的 Azure 資料庫或適用於 MySQL 的 Azure 資料庫資源可能會導致 CPU 或記憶體爭用，並導致查詢效能變慢。 新增 [複本](../postgresql/howto-read-replicas-portal.md) 有助於將讀取向外延展到複本伺服器，並防止主伺服器上的 CPU 或記憶體限制。 Advisor 會識別具有大量讀取工作負載的伺服器，並建議您新增 [讀取複本](../postgresql/concepts-read-replicas.md)   來卸載部分讀取工作負載。


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>將您的適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫或適用於 MariaDB 的 Azure 資料庫伺服器調整為較高的 SKU，以防止連接限制
您資料庫伺服器的每個新連接都會佔用記憶體。 如果您伺服器的連接因為記憶體的 [上限](../postgresql/concepts-limits.md) 而失敗，則資料庫伺服器的效能會降低。 Azure Advisor 識別執行有許多連接失敗的伺服器。 它會建議您執行下列其中一項動作，以升級伺服器的連接限制，以提供更多的記憶體給您的伺服器：
- 擴大計算。 
- 使用記憶體優化的 Sku （每個核心有更多計算）。

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>將您的快取調整至不同大小或 SKU，以改善快取和應用程式效能

當快取實例不是在高記憶體壓力下執行、高伺服器負載或高網路頻寬時，效能最佳。 這些情況可能會使它們變得沒有回應、遇到資料遺失或無法使用。 Advisor 會識別這些狀況中的快取實例。 建議您採取下列其中一項動作：
- 套用最佳做法以降低記憶體壓力、伺服器負載或網路頻寬。
- 調整為具有更多容量的不同大小或 SKU。

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>將具有流量的區域新增至您的 Azure Cosmos DB 帳戶

Advisor 會偵測到 Azure Cosmos DB 的帳戶，該帳戶具有來自目前未設定之區域的流量。 建議您新增該區域。 這麼做可改善來自該區域之要求的延遲，並在發生區域中斷時確保可用性。 [深入瞭解使用 Azure Cosmos DB 的全域資料散發。](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>使用自訂包含或排除的路徑來設定您的 Azure Cosmos DB 編制索引原則

Advisor 會識別使用預設索引編制原則，但可受益于自訂編制索引原則的 Azure Cosmos DB 容器。 這項決定是根據工作負載模式。 預設索引編制原則會編制所有屬性的索引。 自訂索引編制原則具有在查詢篩選中使用的明確包含或排除路徑，可減少用於編制索引的 ru 和儲存體。 [深入瞭解如何修改索引原則。](/azure/cosmos-db/index-policy)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>將 Azure Cosmos DB 查詢頁面大小 (MaxItemCount) 設定為-1 

Azure Advisor 識別使用查詢頁面大小100的 Azure Cosmos DB 容器。 建議使用頁面大小-1 以加快掃描速度。 [深入瞭解 MaxItemCount。](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>考慮使用 HBase 叢集中的加速寫入功能來改善叢集效能
Azure Advisor 在過去7天內分析系統記錄，並識別您的叢集是否遇到下列情況：
1. 高 WAL 同步時間延遲 
2. 高寫入要求計數 (至少 3 個超過 1000 avg_write_requests/second/node 的一小時視窗)

這些條件是您的叢集飽受高寫入延遲所苦的指標。 這可能是因為在您的叢集上執行了繁重的工作負載。若要改善叢集的效能，您可能會想要考慮利用 Azure HDInsight HBase 所提供的加速寫入功能。 HDInsight Apache HBase 叢集的加速寫入功能會將進階 SSD 受控磁碟附加到每個 RegionServer (背景工作節點)，而不是使用雲端儲存體。 因此會為您的應用程式帶來低寫入延遲和更佳的復原能力。 若要[深入瞭解](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)這項功能，請參閱

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>查看 Azure 資料總管資料表快取期間 (原則) 以取得較佳的效能 (預覽) 
這項建議會顯示 Azure 資料總管資料表，其中包含大量查詢，會往回查看已設定的快取期間 (原則) 以外的範圍 (您會看到依據存取快取資料的查詢百分比排列的前 10 個資料表)。 改善叢集效能的建議動作：將此資料表的查詢限制為最小的所需時間範圍 (在定義的原則內)。 或者，如果需要來自整個時間範圍的資料，請將快取期間增加為建議的值。

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>藉由將 MySQL 暫存資料表大小調整最佳化來改善效能
Advisor 分析指出由於臨時表參數設定不足，您的 MySQL 伺服器可能會產生不必要的 i/o 額外負荷。 這可能會導致不必要的磁碟型交易和效能降低。 建議您增加 'tmp_table_size' 和 'max_heap_table_size' 參數值，以減少磁碟型交易的數目。 [深入了解](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>分配伺服器群組中的資料以在節點間分配工作負載
Advisor 會識別未散發資料，但仍留在協調器上的伺服器群組。 根據這一點，Advisor 建議您針對完整超大規模 (Citus) 權益將資料散發至您伺服器群組的背景工作節點。 這會利用伺服器群組中每個節點的資源來改善查詢效能。 [深入了解](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>藉由部署更接近 Windows 虛擬桌面部署位置的 Vm，來改善使用者體驗和連線能力
使用 Windows 虛擬桌面 (WVD)，我們判斷您的 VM 位置與使用者連線的區域不同或更遠。 這可能會導致連線回應時間變長，並會影響 WVD 的整體使用者體驗。 建立主機集區的 VM 時，您應該嘗試使用更接近使用者的區域。 較近的鄰近性可讓維持使用者對 WVD 服務的滿意度，同時獲得更好的整體體驗品質。 若[要深入瞭解連接延遲](../virtual-desktop/connection-latency.md)，請參閱。

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>升級到最新版本的沈浸式閱讀程式 SDK
我們發現此訂用帳戶下的資源使用過時的沈浸式閱讀程式 SDK 版本。 使用最新版本的沈浸式閱讀程式 SDK 可為您提供更新的安全性、效能和擴充的功能集，以便自訂及增強您的整合體驗。
深入瞭解 [沉浸式讀取器 SDK](../cognitive-services/immersive-reader/index.yml)。


## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何存取 Advisor 中的效能建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，選取 [ **效能** ] 索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：

* [Advisor 簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 分數](azure-advisor-score.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [Advisor 可靠性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)