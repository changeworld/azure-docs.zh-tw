---
title: 利用 Azure Advisor 改善 Azure 應用程式的效能
description: 使用 Advisor 將 Azure 部署的效能最佳化。
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: ff9b8fb9494c887397947f009b22cdc89d8f70b5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787935"
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

如需 SQL Database Advisor 的詳細資訊，請參閱[SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)。

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>將您的儲存體用戶端程式庫更新為最新版本以提供更可靠的服務和效能

針對客戶回報的問題以及由我們的 QA 流程主動找出的問題，最新版本的儲存體用戶端程式庫/ SDK 均提供了可解決上述問題的修正程式。 除了各項新功能，最新版本也提供了更高的可靠性及最佳化效能，能改善您使用 Azure 儲存體的整體經驗。 如果您使用的是過時版本，建議程式會提供您升級至最新版本 SDK 的建議和步驟。 這些建議適用于支援的語言-c + + 和 .Net。

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
> Azure 監視器中的傳統警示已于2019年8月淘汰。 建議您將傳統儲存體帳戶升級為使用 Resource Manager，以在新平台上保留警示功能。 如需詳細資訊，請參閱[傳統警示洶汰](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform)。

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>設計您的儲存體帳戶，以避免達到最大訂用帳戶限制

Azure 區域每個訂用帳戶最多可支援250個儲存體帳戶。 達到限制之後，您將無法在該區域/訂用帳戶組合中建立其他任何儲存體帳戶。 Advisor 將會檢查您的訂用帳戶和呈現建議，讓您針對任何接近達到上限的儲存體帳戶進行設計。

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-adress-high-p2s-use"></a>請考慮增加 VNet 閘道 SKU 的大小，以使用高 P2S 的位址

每個閘道 SKU 只能支援指定的並行 P2S 連接計數。 如果您的連線計數接近閘道限制，則其他連線嘗試可能會失敗。 增加閘道的大小可讓您支援更多並行的 P2S 使用者。Advisor 會針對此提供建議和採取的步驟。

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-address-high-cpu"></a>請考慮增加 VNet 閘道 SKU 的大小，以解決高 CPU

在高流量負載之下，VPN 閘道可能會因為高 CPU 而捨棄封包。 您應該考慮升級 VPN 閘道 SKU，因為您的 VPN 一直都是在執行。增加 VPN 閘道的大小可確保連線不會因為高 CPU 而中斷。 Advisor provdes 建議，以主動解決此問題。 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>載入時增加批次大小，以最大化負載輸送量、資料壓縮和查詢效能

Advisor 可以藉由在載入資料庫時增加批次大小，偵測到您可以增加負載效能和輸送量。 您可以考慮使用 COPY 語句。 如果您無法使用 COPY 語句，請考慮在使用載入公用程式（例如 SQLBulkCopy API 或 BCP）時增加批次大小，這是一個很好的經驗法則，這是批次大小介於100K 到1M 個數據列之間。 這會增加負載輸送量、資料壓縮和查詢效能。

## <a name="co-locate-the-storage-account-within-the-same-region-to-minimize-latency-when-loading"></a>共置在相同區域內的儲存體帳戶，以在載入時將延遲降至最低

Advisor 可以偵測到您從不同于 SQL 集區的區域載入。 您應該考慮從與 SQL 集區位於相同區域內的儲存體帳戶進行載入，以將載入資料時的延遲降至最低。 這有助於將延遲降到最低並增加負載效能。

## <a name="unsupported-kubernetes-version-is-detected"></a>偵測到不支援的 Kubernetes 版本

Advisor 可以偵測是否偵測到不支援的 Kubernetes 版本。 建議可協助確保 Kubernetes 叢集以支援的版本執行。

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>將 Azure MySQL、Azure 于 postgresql 和 Azure 適用于 mariadb server 的效能優化 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>修正具有 CPU 瓶頸的 Azure MySQL、Azure 于 postgresql 和 Azure 適用于 mariadb server 的 CPU 壓力
長時間的 CPU 使用率非常高，可能會導致工作負載的查詢效能變慢。 增加 CPU 大小有助於優化資料庫查詢的執行時間，並改善整體效能。 Azure Advisor 將會識別 CPU 使用率很高且可能執行 CPU 限制工作負載的伺服器，並建議調整您的計算。

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>降低 Azure MySQL、Azure 于 postgresql 和 Azure 適用于 mariadb 伺服器上的記憶體限制，或移至記憶體優化 SKU
較低的快取點擊率會導致查詢效能變慢，並增加 IOPS。 這可能是因為查詢計劃不正確或執行記憶體密集型工作負載所造成。 若要修正查詢計劃或增加適用於 PostgreSQL 的 Azure 資料庫資料庫伺服器、Azure MySQL 資料庫伺服器或 Azure 適用于 mariadb server [的記憶體](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers)，將有助於優化資料庫工作負載的執行。 Azure Advisor 識別由於這項高緩衝集區變換而受到影響的伺服器，並建議您修正查詢計劃、移至具有更多記憶體的較高 SKU，或增加儲存體大小以取得更多 IOPS。

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>使用 Azure MySQL 或 Azure 于 postgresql 讀取複本來向外延展大量讀取的工作負載
Azure Advisor 利用以工作負載為基礎的啟發學習法，例如過去七天內，伺服器上的讀取與寫入比例，以識別大量讀取的工作負載。 您的 Azure database for 于 postgresql 資源或「適用于 MySQL 的 Azure 資料庫」資源具有非常高的讀取/寫入比率，可能會導致 CPU 和/或記憶體爭用，進而使查詢效能變慢。 新增 [複本](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal)有助於向外延展讀取至複本伺服器，以防止主伺服器上的 CPU 和/或記憶體限制。 Advisor 會識別具有大量讀取密集型工作負載的伺服器，並建議新增 [讀取複本](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) 來卸載部分讀取工作負載。


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>將您的 Azure MySQL、Azure 于 postgresql 或 Azure 適用于 mariadb server 調整為較高的 SKU，以避免連接限制
資料庫伺服器的每個新連接都會佔用一些記憶體。 如果伺服器的連接因為記憶體的 [上限](https://docs.microsoft.com/azure/postgresql/concepts-limits)而失敗，資料庫伺服器的效能會降低。 Azure Advisor 會識別執行有許多連線失敗的伺服器，並建議升級您的伺服器連線限制，藉由相應增加計算或使用記憶體優化 Sku （每個核心有更多計算），為您的伺服器提供更多記憶體。

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>將您的快取調整為不同的大小或 SKU，以改善快取和應用程式效能

當快取實例在記憶體不足的壓力下執行時，最佳的伺服器負載或高網路頻寬可能會使它們變得沒有回應、遇到資料遺失或無法使用的情況。 Advisor 會識別這些情況下的快取實例，並建議套用最佳作法以降低記憶體壓力、伺服器負載或網路頻寬，或調整為具有更多容量的不同大小或 SKU。

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>將具有流量的區域新增至您的 Azure Cosmos DB 帳戶

Advisor 將會偵測到 Azure Cosmos DB 的帳戶，其流量來自目前未設定的區域，並建議新增該區域。 這將可改善來自該區域之要求的延遲，並在發生區域中斷時確保可用性。 [深入瞭解使用 Azure Cosmos DB 的全域資料散發](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>使用客戶包含或排除的路徑來設定您的 Azure Cosmos DB 編制索引原則

Azure Advisor 會識別使用預設索引編制原則，但可根據工作負載模式從自訂索引編制原則獲益的 Cosmos DB 容器。 預設的索引編制原則會編制所有屬性的索引，但使用自訂的索引編制原則，其中包含用於查詢篩選器的明確包含或排除路徑，可以減少用於編制索引的 ru 和儲存體。 [深入瞭解如何修改索引原則](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>將 Azure Cosmos DB 查詢頁面大小 (MaxItemCount) 設定為 -1 

Azure Advisor 會識別使用100查詢頁面大小的 Azure Cosmos DB 容器，並建議您使用-1 的頁面大小來進行更快速的掃描。 [深入瞭解最大專案計數](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

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
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
