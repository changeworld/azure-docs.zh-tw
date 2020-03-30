---
title: 利用 Azure Advisor 改善 Azure 應用程式的效能
description: 使用 Advisor 將 Azure 部署的效能最佳化。
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 405ec395feeb33b8511b9b915151b2ed9503c371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443064"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>利用 Azure Advisor 改善 Azure 應用程式的效能

Advisor 效能建議有助於提升業務關鍵應用程式的速度和回應能力。 您可以在 Advisor 儀表板的 [效能]**** 索引標籤上，取得 Advisor 的效能建議。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>減少 DNS 在流量管理員設定檔上的存留時間，以更快速容錯移轉至健康情況良好的端點上

流量管理員設定檔上的[存留時間 (TTL) 設定](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)可讓您在指定端點停止回應查詢時，指定切換至端點的速度。 減少 TTL 值表示用戶端將會更快速路由傳送至運作的端點。

Azure Advisor 會識別設定較長 TTL 的流量管理員設定檔，並且建議將 TTL 設定為 20 秒或 60 秒，取決於是否設定[快速容錯移轉](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)的設定檔。

## <a name="improve-database-performance-with-sql-db-advisor"></a>使用 SQL DB Advisor 來改善資料庫效能

建議程式可針對所有的 Azure 資源提供一致的合併建議檢視。 它會與 SQL Database 建議程式整合，以提供改善 SQL Azure 資料庫效能的相關建議。SQL Database 建議程式會藉由分析您的使用歷程記錄來評估 SQL Azure 資料庫的效能。 接著會提供最適合用於執行資料庫之一般工作負載的建議事項。

> [!NOTE]
> 若要取得建議，資料庫必須持續使用一週，而且那一週之內必須有一些一致的活動。 相較於隨機蹦出的活動，一致的查詢模式更有利於 SQL Database Advisor 最佳化。

有關 SQL 資料庫顧問的詳細資訊，請參閱[SQL 資料庫顧問](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)。

## <a name="improve-app-service-performance-and-reliability"></a>改善 App Service 的效能和可靠性

Azure 建議程式整合了最佳作法建議，以供提升應用程式服務體驗和探索相關的平台功能。 應用程式服務建議的範例如下︰
* 偵測應用程式執行階段與緩和選項已耗盡記憶體或 CPU 資源的執行個體。
* 偵測共置資源 (如 Web 應用程式和資料庫) 可改善效能並降低成本的執行個體。

如需應用程式服務建議的詳細資訊，請參閱 [Azure App Service 的最佳作法](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>使用受控磁碟可避免磁碟 I/O 節流

Advisor 會識別即將達到其延展性目標的儲存體帳戶所包含的虛擬機器。 這種情況讓這些 VM 很可能會執行 I/O 節流。 Advisor 會建議它們使用受控磁碟，以避免效能降低。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>使用進階儲存體以改善虛擬機器磁碟的效能和可靠性

虛擬機器若使用標準磁碟，且在您的儲存體帳戶上有大量交易，Advisor 會加以識別，並建議升級為進階磁碟。 

針對執行時需要大量 I/O 之工作負載的虛擬機器，「Azure 進階儲存體」可提供高效能、低延遲的磁碟支援。 使用進階儲存體帳戶的虛擬機器磁碟會將資料儲存在固態硬碟 (SSD) 上。 為了讓應用程式發揮最佳效能，建議您將任何需要高 IOPS 的虛擬機器磁碟移轉至進階儲存體。

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>在 SQL 資料倉儲資料表上移除資料扭曲以提升查詢效能

執行工作負載時，資料扭曲可能會造成不必要的資料移動或資源瓶頸。 Advisor 將偵測到大於 15% 的散發資料扭曲，且會建議您重新散發資料，並重新檢視您的資料表散發金鑰選取項目。 若要深入了解如何識別和移除扭曲，請參閱[針對扭曲進行疑難排解](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)。

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>在 SQL 資料倉儲資料表上建立或更新過期的資料表統計資料以提升查詢效能

Advisor 會識別不含最新[資料表統計資料](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)的資料表，以及建立或更新資料表統計資料的建議。 SQL 資料倉儲查詢最佳化工具會使用最新的統計資料，來估計基數或查詢結果中的資料列數目，以利其建立高品質的查詢計劃來取得更快速的效能。

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>藉由相應增加將 SQL 資料倉儲資料表的快取使用率最佳化，以提升查詢效能

Azure Advisor 會偵測您的 SQL 資料倉儲是否有快取使用百分比偏高、命中百分比偏低的情形。 這種情況表示快取收回率偏高，而可能會影響到您 SQL 資料倉儲的效能。 Advisor 會建議您相應增加 SQL 資料倉儲，以確保能配置足夠的快取容量供工作負載使用。

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>將 SQL 資料倉儲資料表轉換為複寫資料表，以提升查詢效能

Advisor 會識別不是複寫資料表、但可因轉換而受益的資料表，並建議您轉換這些資料表。 提供的建議取決於複寫的資料表大小、資料行數目、資料表散發類型，以及 SQL 資料倉儲資料表的分割區數目。 此外也可能在內容的建議中提供啟發學習法。 若要深入了解這項建議的權衡方式，請參閱 [SQL 資料倉儲建議](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>將儲存體帳戶移轉至 Azure Resource Manager 以取得所有最新的 Azure 功能

將儲存體帳戶部署模型遷移到 Azure Resource Manager (Resource Manager)，可使用範本部署、其他安全性選項，以及可升級至 GPv2 帳戶，以利用 Azure 儲存體的最新功能。 Advisor 會識別任何使用傳統部署模型的獨立儲存體帳戶，並建議遷移到 Resource Manager 部署模型。

> [!NOTE]
> Azure 監視器中的經典警報已于 2019 年 8 月停用。 建議您將傳統儲存體帳戶升級為使用 Resource Manager，以在新平台上保留警示功能。 如需詳細資訊，請參閱[傳統警示洶汰](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform)。

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>設計存儲帳戶以防止達到最大訂閱限制

Azure 區域每個訂閱最多可以支援 250 個存儲帳戶。 達到限制後，您將無法在該區域/訂閱組合中創建任何存儲帳戶。 Advisor 將檢查您的訂閱和表面建議，以便您為接近最大限制的任何存儲帳戶進行設計。

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>優化 Azure MySQL、Azure PostgreSQL 和 Azure MariaDB 伺服器的性能 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>修復 Azure MySQL、Azure PostgreSQL 和 Azure MariaDB 伺服器的 CPU 壓力，並設置 CPU 瓶頸
長時間 CPU 的利用率非常高可能會導致工作負載的查詢性能降低。 增加 CPU 大小將有助於優化資料庫查詢的運行時並提高整體性能。 Azure Advisor 將標識 CPU 利用率高且可能運行 CPU 受限工作負載的伺服器，並建議擴展計算。

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>減少 Azure MySQL、Azure PostgreSQL 和 Azure MariaDB 伺服器上的記憶體限制，或移動到記憶體優化的 SKU
較低的緩存命中率可能會導致查詢性能降低和 IOPS 增加。 這可能是由於查詢計劃錯誤或運行記憶體密集型工作負載造成的。 修復查詢計劃或增加 PostgreSQL 資料庫伺服器、Azure MySQL 資料庫伺服器或 Azure MariaDB 伺服器 Azure 資料庫的 [記憶體](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers)將有助於優化資料庫工作負載的執行。 Azure Advisor 可識別由於此高緩衝區池改動而受影響的伺服器，並建議修復查詢計劃、移動到具有更多記憶體的更高 SKU 或增加存儲大小以獲取更多 IOPS。

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>使用 Azure MySQL 或 Azure PostgreSQL 讀取副本擴展讀取以進行讀取密集型工作負荷
Azure Advisor 利用基於工作負載的啟發式方法，例如過去七天在伺服器上讀取與寫入的比率，以識別讀取密集型工作負載。 用於 PostgreSQL 資源的 Azure 資料庫或具有非常高讀取/寫入比率的 MySQL 資源的 Azure 資料庫可能會導致 CPU 和/或記憶體爭用，從而導致查詢性能降低。 添加 [副本](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal)將有助於向副本伺服器擴展讀取，防止主伺服器上的 CPU 和/或記憶體限制。 Advisor 將標識具有如此高讀取密集型工作負載的伺服器，並建議添加 [讀取副本](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) 以卸載某些讀取工作負載。


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>將 Azure MySQL、Azure PostgreSQL 或 Azure MariaDB 伺服器縮放到更高的 SKU，以防止連接約束
與資料庫伺服器的每個新連接都會佔用一些記憶體。 如果由於記憶體中的 [上限](https://docs.microsoft.com/azure/postgresql/concepts-limits)而與伺服器的連接失敗，資料庫伺服器的性能會降低。 Azure Advisor 將識別運行在許多連接失敗的伺服器，並建議升級伺服器的連接限制，通過擴展計算或使用記憶體優化 SKU（每個內核的計算更多）為伺服器提供更多記憶體。

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>將緩存縮放為不同的大小或 SKU 以提高緩存和應用程式性能

當緩存實例在高記憶體壓力、伺服器負載或高網路頻寬下未運行時性能最佳，這可能導致它們無回應、資料丟失或不可用。 Advisor 將識別這些條件下的緩存實例，並建議應用最佳實踐以降低記憶體壓力、伺服器負載或網路頻寬，或擴展到不同大小或容量更大的 SKU。

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>將具有流量的區域添加到 Azure Cosmos 資料庫帳戶

Advisor 將檢測具有當前未配置區域流量的 Azure Cosmos DB 帳戶，並建議添加該區域。 這將改善來自該區域的請求的延遲，並確保在區域中斷時提供可用性。 [瞭解有關使用 Azure Cosmos DB 進行全域資料分發的更多情況](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>使用客戶包含或排除的路徑配置 Azure Cosmos DB 索引策略

Azure Advisor 將標識使用預設索引策略但可以從基於工作負載模式的自訂索引策略中受益的 Cosmos DB 容器。 預設索引策略對所有屬性進行索引，但使用自訂索引策略，具有查詢篩選器中使用的顯式包含或排除路徑，可以減少用於索引的 R 和存儲。 [瞭解有關修改索引策略的更多](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>將 Azure Cosmos DB 查詢頁面大小 (MaxItemCount) 設定為 -1 

Azure Advisor 將標識使用查詢頁大小為 100 的 Azure Cosmos DB 容器，並建議使用 -1 的頁面大小來加快掃描速度。 [瞭解有關最大專案計數的更多](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何在建議程式中存取效能建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，按一下 [效能]**** 索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：

* [Advisor 簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [Advisor 高可用性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [顧問卓越運營建議](advisor-operational-excellence-recommendations.md)
