---
title: 使用 Advisor 改善 Azure 應用程式的效能
description: 在 Azure Advisor 中使用效能建議，以改善業務關鍵應用程式的速度和回應能力。
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 45fb1f8844067cb34b415c3897db3b1233ab5c7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85124481"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>使用 Azure Advisor 改善 Azure 應用程式的效能

Azure Advisor 中的效能建議有助於改善業務關鍵應用程式的速度和回應能力。 您可以在 Advisor 儀表板的 [效能]**** 索引標籤上，取得 Advisor 的效能建議。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>在您的流量管理員設定檔上減少 DNS 存留時間，以更快故障切換到狀況良好的端點

您可以使用 Azure 流量管理員設定檔上的生存[時間（TTL）設定](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)，指定當指定的端點停止回應查詢時，切換端點的速度。 如果您減少 TTL 值，用戶端將會更快地路由到運作中的端點。

Azure Advisor 識別已設定較長 TTL 的流量管理員設定檔。 視設定檔是否設定為[快速容錯移轉](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)而定，建議您將 TTL 設為20秒或60秒。

## <a name="improve-database-performance-by-using-sql-database-advisor"></a>使用 SQL Database Advisor 改善資料庫效能

Azure Advisor 可針對所有的 Azure 資源提供一致的合併建議檢視。 它會與 SQL Database Advisor 整合，為您提供改善資料庫效能的建議。SQL Database Advisor 會分析您的使用量歷程記錄，以評估資料庫的效能。 然後，它會提供最適合執行資料庫一般工作負載的建議。

> [!NOTE]
> 在您可以取得建議之前，您的資料庫必須使用大約一周，而且該周內必須有一些一致的活動。 相較於隨機蹦出的活動，一致的查詢模式更有利於 SQL Database Advisor 最佳化。

如需詳細資訊，請參閱[SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)。

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>將您的儲存體用戶端程式庫升級至最新版本，以獲得更好的可靠性和效能

最新版的儲存體用戶端程式庫 SDK 包含客戶回報之問題的修正，並透過我們的 QA 流程主動識別。 最新版本也提供可靠性和效能優化，以及可改善您使用 Azure 儲存體之整體體驗的新功能。 如果您使用的是過時版本，Advisor 會提供升級至最新版本 SDK 所需的建議和步驟。 這些建議適用于支援的語言： c + + 和 .NET。

## <a name="improve-app-service-performance-and-reliability"></a>改善 App Service 的效能和可靠性

Azure Advisor 整合建議來改善您的 App Service 體驗，以及探索相關的平臺功能。 App Service 建議的範例如下：
* 偵測應用程式執行時間耗盡記憶體或 CPU 資源的實例，並提供緩和選項。
* 偵測共同尋找資源（例如 web 應用程式和資料庫）可改善效能並降低成本的實例。

如需詳細資訊，請參閱[Azure App Service 的最佳做法](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>使用受控磁片來防止磁片 i/o 節流

Advisor 會識別屬於儲存體帳戶的虛擬機器，而該儲存體帳戶會達到其擴充性目標。 這種情況讓這些 VM 很可能會執行 I/O 節流。 Advisor 會建議使用受控磁片，以避免效能降低。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>使用進階儲存體以改善虛擬機器磁碟的效能和可靠性

Advisor 會識別具有在您儲存體帳戶上具有大量交易之標準磁片的虛擬機器。 建議您升級至 premium 磁片。 

針對執行時需要大量 I/O 之工作負載的虛擬機器，「Azure 進階儲存體」可提供高效能、低延遲的磁碟支援。 使用進階儲存體帳戶的虛擬機器磁片會將資料儲存在固態硬碟（Ssd）上。 為了讓您的應用程式獲得最佳效能，建議您將需要高 IOPS 的任何虛擬機器磁片遷移到進階儲存體。

## <a name="remove-data-skew-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>移除 SQL 資料倉儲資料表上的資料扭曲，以提高查詢效能

當您執行工作負載時，資料扭曲可能會造成不必要的資料移動或資源瓶頸。 Advisor 會偵測到大於15% 的分佈資料扭曲。 建議您轉散發資料，並重新流覽您的資料表散發索引鍵選取專案。 若要深入了解如何識別和移除扭曲，請參閱[針對扭曲進行疑難排解](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)。

## <a name="create-or-update-outdated-table-statistics-in-your-sql-data-warehouse-tables-to-increase-query-performance"></a>在 SQL 資料倉儲資料表中建立或更新過期的資料表統計資料，以提高查詢效能

Advisor 會識別沒有最新[資料表統計資料](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)的資料表，並建議建立或更新統計資料。 Azure SQL 資料倉儲中的查詢最佳化工具會使用最新的統計資料，來估計基數或查詢結果中的資料列數目。 這些估計值可讓查詢最佳化工具建立查詢計劃，以提供最快的效能。

## <a name="improve-mysql-connection-management"></a>改善 MySQL 連線管理

Advisor 分析可能表示您的應用程式連線到 MySQL 伺服器可能沒有有效率地管理連接。 這種狀況可能會導致不必要的資源耗用量，以及整體的應用程式延遲。 若要改善連線管理，建議您減少短期連線的數目，並排除不必要的閒置連線。 您可以設定伺服器端連接共用器，例如 ProxySQL，以進行這些改良。


## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>藉由相應增加將 SQL 資料倉儲資料表的快取使用率最佳化，以提升查詢效能

Azure Advisor 會偵測您的 SQL 資料倉儲資料表是否有高快取使用百分比和較低的點擊百分比。 這種情況表示快取收回率過高，這可能會影響 SQL 資料倉儲實例的效能。 Advisor 建議您相應增加您的 SQL 資料倉儲實例，以確保您配置足夠的快取容量來處理您的工作負載。

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>將 SQL 資料倉儲資料表轉換為複寫資料表，以提升查詢效能

Advisor 會識別不是複寫資料表的資料表，但可受益于轉換。 建議您轉換這些資料表。 建議的依據：
- 複寫資料表的大小。 
- 資料行數目。 
- 資料表散發類型。 
- SQL 資料倉儲資料表上的資料分割數目。 

內容的建議中可能會提供其他啟發學習法。 若要深入瞭解如何判斷此建議，請參閱[SQL 資料倉儲建議](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>將您的儲存體帳戶遷移至 Azure Resource Manager 以取得最新的 Azure 功能

將您的儲存體帳戶部署模型遷移至 Azure Resource Manager 以利用：
- 範本部署。
- 其他安全性選項。
- 升級至 GPv2 帳戶的能力，讓您可以使用最新的 Azure 儲存體功能。 

Advisor 會識別任何使用傳統部署模型的獨立儲存體帳戶，並建議遷移至 Resource Manager 部署模型。

> [!NOTE]
> Azure 監視器中的傳統警示已于2019年8月淘汰。 建議您將傳統儲存體帳戶升級為使用 Resource Manager，以在新平台上保留警示功能。 如需詳細資訊，請參閱[傳統警示淘汰](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform)。

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>設計您的儲存體帳戶，以避免達到最大訂用帳戶限制

Azure 區域支援每個訂用帳戶最多250個儲存體帳戶。 達到此限制之後，您將無法在該區域/訂用帳戶組合中建立儲存體帳戶。 Advisor 會檢查您的訂用帳戶，並提供建議，讓您針對接近達到上限的任何訂閱/區域，設計較少的儲存體帳戶。

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>請考慮增加 VPN 閘道 SKU 的大小，以解決高 P2S 的使用

每個 Azure VPN 閘道 SKU 只能支援指定數目的並行 P2S 連接。 如果您的連線計數接近閘道限制，其他連接嘗試可能會失敗。 如果您增加閘道的大小，就能夠支援更多的並行 P2S 使用者。 Advisor 會提供建議，以及增加閘道大小的指示。

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>請考慮增加 VPN 閘道 SKU 的大小，以解決高 CPU

在高流量負載之下，您的 VPN 閘道可能會因為高 CPU 而捨棄封包。 請考慮升級您的 VPN 閘道 SKU。 增加 VPN 閘道的大小可確保不會因為高 CPU 而中斷連線。 Advisor 會提供建議，以主動解決此問題。 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>增加載入時的批次大小，以最大化載入輸送量、資料壓縮和查詢效能

Advisor 會偵測您是否可以在載入資料庫時增加批次大小，以增加負載效能和輸送量。 您可以考慮使用 COPY 語句。 如果您無法使用 COPY 語句，請考慮在使用載入公用程式（例如 SQLBulkCopy API 或 BCP）時增加批次大小。 良好的一般規則是使用介於100000到1000000個數據列之間的批次大小。 增加批次大小會增加負載輸送量、資料壓縮和查詢效能。

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>共置相同區域中的儲存體帳戶，以在載入時將延遲降至最低

Advisor 會偵測您是否要從不同于 SQL 集區的區域載入。 請考慮從與您的 SQL 集區位於相同區域的儲存體帳戶載入，以將載入資料時的延遲降至最低。 這種變更有助於將延遲降到最低並增加負載效能。

## <a name="use-a-supported-kubernetes-version"></a>使用支援的 Kubernetes 版本

Advisor 偵測到不支援的 Kubernetes 版本。

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>將適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫和適用於 MariaDB 的 Azure 資料庫伺服器的效能優化

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>修正 CPU 瓶頸適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫和適用於 MariaDB 的 Azure 資料庫伺服器的 CPU 壓力
長時間的 CPU 高使用率，會導致工作負載的查詢效能變慢。 增加 CPU 大小有助於優化資料庫查詢的執行時間，並改善整體效能。 Advisor 會識別 CPU 使用率很高的伺服器，其可能正在執行 CPU 限制的工作負載，並建議調整您的計算。

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>降低適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫和適用於 MariaDB 的 Azure 資料庫伺服器的記憶體限制，或移至記憶體優化 SKU
較低的快取點擊率會導致查詢效能變慢，並增加 IOPS。 這種狀況可能是因為不正確的查詢計劃或記憶體密集型工作負載所造成。 修正查詢計劃或增加適用於 PostgreSQL 的 Azure 資料庫、適用於 MySQL 的 Azure 資料庫或適用於 MariaDB 的 Azure 資料庫伺服器 [的記憶體](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers)，將有助於優化資料庫工作負載的執行。 Azure Advisor 識別受此高緩衝集區變換影響的伺服器。 建議您採取下列其中一項動作： 
- 修正查詢計劃
- 移至擁有更多記憶體的 SKU 
- 增加儲存體大小以取得更多 IOPS。

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>使用適用於 MySQL 的 Azure 資料庫或適用於 PostgreSQL 的 Azure 資料庫讀取複本，以向外延展大量讀取的工作負載
Advisor 會使用以工作負載為基礎的啟發學習法，如過去七天內，伺服器上的讀取與寫入比例，以識別大量讀取的工作負載。 具有高讀取/寫入比率的適用於 PostgreSQL 的 Azure 資料庫或適用於 MySQL 的 Azure 資料庫資源可能會導致 CPU 或記憶體爭用，並導致查詢效能變慢。 新增 [複本](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal)有助於向外延展讀取至複本伺服器，並防止主伺服器上的 CPU 或記憶體限制。 Advisor 會識別具有大量讀取工作負載的伺服器，並建議您新增 [讀取複本](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas)   來卸載部分讀取工作負載。


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>將您的適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫或適用於 MariaDB 的 Azure 資料庫伺服器調整為較高的 SKU，以避免連接限制
資料庫伺服器的每個新連接都會佔用記憶體。 如果伺服器的連接因為記憶體的 [上限](https://docs.microsoft.com/azure/postgresql/concepts-limits)而失敗，資料庫伺服器的效能會降低。 Azure Advisor 識別執行有許多連線失敗的伺服器。 它會建議您升級伺服器的連接限制，藉由採取下列其中一項動作來提供更多記憶體給您的伺服器：
- 相應增加計算。 
- 使用記憶體優化 Sku，其每個核心有更多計算。

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>將您的快取調整為不同的大小或 SKU，以改善快取和應用程式效能

當快取實例未在記憶體不足的壓力下執行、伺服器負載過高，或網路頻寬過高時，效能就會更好。 這些狀況可能會使它們變得沒有回應、遇到資料遺失或變得無法使用。 在這些情況中，Advisor 會識別快取實例。 建議您採取下列其中一項動作：
- 套用最佳作法，以降低記憶體壓力、伺服器負載或網路頻寬。
- 調整為具有更多容量的不同大小或 SKU。

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>將具有流量的區域新增至您的 Azure Cosmos DB 帳戶

Advisor 會偵測具有來自目前未設定之區域流量的 Azure Cosmos DB 帳戶。 它建議新增該區域。 這樣做可改善來自該區域之要求的延遲，並在發生區域中斷時確保可用性。 [深入瞭解使用 Azure Cosmos DB 的全域資料散發。](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>使用自訂的內含或排除路徑來設定您的 Azure Cosmos DB 編制索引原則

Advisor 會識別使用預設索引編制原則但可受益于自訂索引編制原則的 Azure Cosmos DB 容器。 這項決定是以工作負載模式為基礎。 預設的索引編制原則會編制所有屬性的索引。 具有查詢篩選器中所使用明確包含或排除路徑的自訂索引編制原則，可以減少用來編制索引的 ru 和儲存體。 [深入瞭解如何修改索引原則。](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>將您的 Azure Cosmos DB 查詢頁面大小（Feedoptions.maxitemcount）設定為-1 

Azure Advisor 會識別使用100查詢頁面大小的 Azure Cosmos DB 容器。 建議使用-1 的頁面大小，以進行更快速的掃描。 [深入瞭解 Feedoptions.maxitemcount。](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何存取 Advisor 中的效能建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，選取 [**效能**] 索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：

* [Advisor 簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [Advisor 可靠性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
