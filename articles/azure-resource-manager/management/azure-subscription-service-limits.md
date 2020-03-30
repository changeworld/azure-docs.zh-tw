---
title: Azure 訂用帳戶限制與配額
description: 提供通用的 Azure 訂用帳戶和服務限制、配額和條件約束的清單。 本文包含有關如何增加限制和最大值的資訊。
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 325f7b3d03435945779c1f42e13681dcfd9604b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334796"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 訂用帳戶和服務限制、配額與限制

本文件列出一些最常見的 Microsoft Azure 限制，有時也稱為配額。

要瞭解有關 Azure 定價的詳細資訊，請參閱[Azure 定價概述](https://azure.microsoft.com/pricing/)。 在那裡，您可以使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)估計您的成本。 您還可以轉到特定服務的定價詳細資訊頁面，例如[Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)。 如需協助您管理成本的祕訣，請參閱[使用 Azure 計費與成本管理避免非預期的成本](../../billing/billing-getting-started.md)。

## <a name="managing-limits"></a>管理限制

> [!NOTE]
> 某些服務具有可調節的限制。
>
> 當服務沒有可調整的限制時，下表將使用標頭**限制**。 在這些情況下，預設值和最大限制相同。
>
> 當可以調整限制時，表包括**預設限制**和**最大限制**標頭。 限制可以高於預設限制，但不能高於最大限制。
>
> 如果要將限制或配額提高到預設限制以上，[請免費打開線上客戶支援請求](../templates/error-resource-quota.md)。

[免費試用訂閱](https://azure.microsoft.com/offers/ms-azr-0044p)不符合限制或配額增加的條件。 如果您有[免費試用訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0044p)，則可以升級到[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)訂用帳戶。 有關詳細資訊，請參閱將[Azure 免費試用版訂閱升級到即用即付訂閱](../../billing/billing-upgrade-azure-subscription.md)和[免費試用訂閱常見問題解答](https://azure.microsoft.com/free/free-account-faq)。

某些限制在區域一級管理。

讓我們以 vCPU 配額為例。 要請求增加配額，支援 vCPU，您必須決定要在哪些區域中使用多少個 vCPU。 然後，針對所需的數量和區域對 Azure 資源組 vCPU 配額發出特定請求。 如果您需要在西歐使用 30 個 vCPU 來運行應用程式，則特別在西歐請求 30 個 vCPU。 您的 vCPU 配額在任何其他區域不會增加 -只有西歐有 30 vCPU 配額。

因此，請確定 Azure 資源組配額必須適用于任何一個區域中的工作負荷。 然後，在要部署到的每個區域中請求該金額。 有關如何確定特定區域的當前配額的説明，請參閱[解決資源配額的錯誤](../templates/error-resource-quota.md)。

## <a name="general-limits"></a>一般限制

有關資源名稱的限制，請參閱[Azure 資源的命名規則和限制](resource-name-rules.md)。

如需 Resource Manager API 讀取和寫入限制的相關資訊，請參閱[對 Resource Manager 要求進行節流](request-limits-and-throttling.md)。

### <a name="management-group-limits"></a>管理組限制

以下限制適用于[管理組](../../governance/management-groups/overview.md)。

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>訂用帳戶限制

使用 Azure 資源管理器和 Azure 資源組時，將應用以下限制。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>資源組限制

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Active Directory 限制

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>API 管理限制

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>應用程式服務限制

下列 App Service 限制包含 Web 應用程式、行動應用程式和 API 應用程式的限制。

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>自動化限制

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure Cache for Redis 限制

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Azure 雲服務限制

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Azure 認知搜索限制

定價層會決定搜尋服務的容量和限制。 層級包括：

* 與其他 Azure 訂閱者共用**的免費**多租戶服務用於評估和小型開發專案。
* **** 可針對規模較小的生產工作負載提供專用的計算資源，以及針對高可用性的查詢工作負載提供最多 3 個複本。
* **標準**（包括 S1、S2、S3 和 S3 高密度）適用于更大的生產工作負載。 標準層中存在多個級別，因此您可以選擇最適合工作負荷設定檔的資源配置。

**每一訂用帳戶的限制**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**每個搜尋服務的限制**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

要瞭解有關更精細級別限制（如文檔大小、每秒查詢、鍵、請求和回應）的更多限制，請參閱[Azure 認知搜索 中的服務限制](../../search/search-limits-quotas-capacity.md)。

## <a name="azure-cognitive-services-limits"></a>Azure 認知服務限制

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB 限制

有關 Azure 宇宙資料庫限制，請參閱[Azure 宇宙 DB 中的限制](../../cosmos-db/concepts-limits.md)。

## <a name="azure-data-explorer-limits"></a>Azure 資料資源管理器限制

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫

如需適用於 MySQL 的 Azure 資料庫限制詳細資訊，請參閱[適用於 MySQL 的 Azure 資料庫中的限制](../../mysql/concepts-limits.md)。

## <a name="azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫

如需適用於 PostgreSQL 的 Azure 資料庫限制詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫中的限制](../../postgresql/concepts-limits.md)。

## <a name="azure-functions-limits"></a>Azure 函數限制

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Azure 庫伯內斯服務限制

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure 機器學習限制

Azure 機器學習計算配額的最新值可在[Azure 機器學習配額頁](../../machine-learning/how-to-manage-quotas.md)中找到

## <a name="azure-maps-limits"></a>Azure 地圖服務限制

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Azure 監視器限制

### <a name="alerts"></a>警示

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>動作群組

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>日誌查詢和語言

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics 工作區

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Azure 原則限制

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Azure 信號R 服務限制

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>備份限制

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Batch 限制

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>經典部署模型限制

如果使用經典部署模型而不是 Azure 資源管理器部署模型，則適用以下限制。

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Container Instances 限制

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Container Registry 登入

下表詳細介紹了基本、標準和高級[服務層的功能](../../container-registry/container-registry-skus.md)和限制。

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>內容交付網路限制

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Data Factory 限制

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Data Lake Analytics 限制

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Data Lake Store 限制

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>資料共用限制

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Database Migration Service 限制

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>事件網格限制

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>事件中樞限制

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>身份管理器限制

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>IoT 中心限制
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT 中樞限制

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>IoT 中樞裝置佈建服務限制

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>金鑰保存庫限制

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>媒體服務限制

[!INCLUDE [azure-mediaservices-limits](../../../includes/azure-mediaservices-limits.md)]

## <a name="mobile-services-limits"></a>行動服務限制

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>多重要素驗證限制

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>網路限制

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>快速路由限制

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>虛擬廣域網路限制

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>應用程式閘道限制

下列表格適用於 v1、v2、「標準」及 WAF SKU (除非另外註明)。
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>網路監看員限制

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Private Link 限制

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>流量管理員限制

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Azure 堡壘限制

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Azure DNS 限制

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Azure 防火牆限制

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Azure Front Door Service 限制

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>通知中心限制

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>角色型存取控制限制

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>服務匯流排限制

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Site Recovery 限制

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>SQL Database 限制

有關 SQL 資料庫限制，請參閱[單個資料庫的 SQL 資料庫資源限制](../../sql-database/sql-database-vcore-resource-limits-single-databases.md)、[彈性池和池資料庫的 SQL 資料庫資源限制](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)以及[託管實例的 SQL 資料庫資源限制](../../sql-database/sql-database-managed-instance-resource-limits.md)。

## <a name="sql-data-warehouse-limits"></a>SQL 資料倉儲限制

有關 SQL 資料倉儲限制，請參閱[SQL 資料倉儲資源限制](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)。

## <a name="storage-limits"></a>儲存體限制

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

有關標準存儲帳戶限制的詳細資訊，請參閱[標準存儲帳戶的可伸縮性目標](../../storage/common/scalability-targets-standard-account.md)。

### <a name="storage-resource-provider-limits"></a>儲存體資源提供者限制

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Azure Blob 儲存體的限制

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Azure 檔案的限制

有關 Azure 檔限制的詳細資訊，請參閱[Azure 檔可伸縮性和性能目標](../../storage/files/storage-files-scale-targets.md)。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Azure 檔案同步的限制

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Azure 佇列儲存體的限制

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Azure 資料表儲存體的限制

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>虛擬機器磁碟限制

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

有關詳細資訊，請參閱[虛擬機器大小](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="managed-virtual-machine-disks"></a>受控虛擬機器磁碟

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>非受控虛擬機器磁碟

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple 系統限制

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>串流分析限制

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>虛擬機器限制

### <a name="virtual-machines-limits"></a>虛擬機器限制

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>虛擬機器限制 - Azure 資源管理員

使用 Azure 資源管理器和 Azure 資源組時，將應用以下限制。

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>共用圖像庫限制

使用共用映射庫部署資源（每個訂閱）有限制：

- 每個區域每個訂閱 100 個共用圖像庫
- 每個區域每個訂閱 1，000 個圖像定義
- 每個區域每個訂閱 10，000 個映射版本

## <a name="virtual-machine-scale-sets-limits"></a>虛擬機器規模設置限制

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>另請參閱

* [瞭解 Azure 限制和增加](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Azure 的虛擬機器和雲服務大小](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure 雲服務的大小](../../cloud-services/cloud-services-sizes-specs.md)
* [Azure 資源的命名規則和限制](resource-name-rules.md)
