---
title: Azure 訂用帳戶限制與配額
description: 提供通用的 Azure 訂用帳戶和服務限制、配額和條件約束的清單。 本文包含如何增加限制和最大值的資訊。
ms.topic: conceptual
author: davidsmatlak
ms.date: 06/04/2020
ms.openlocfilehash: 79aaa594a708fe775a2cdcc6e15b7c7f880f9f50
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810474"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 訂用帳戶和服務限制、配額與限制

本文件列出一些最常見的 Microsoft Azure 限制，有時也稱為配額。

若要深入瞭解 Azure 定價，請參閱[azure 定價總覽](https://azure.microsoft.com/pricing/)。 在這裡，您可以使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估成本。 您也可以移至特定服務（例如[Windows vm](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)）的定價詳細資料頁面。 如需協助您管理成本的祕訣，請參閱[使用 Azure 計費與成本管理避免非預期的成本](../../cost-management-billing/manage/getting-started.md)。

## <a name="managing-limits"></a>管理限制

> [!NOTE]
> 某些服務具有可調整的限制。
>
> 當服務沒有可調整的限制時，下表會使用標頭**限制**。 在這些情況下，預設值和最大限制都相同。
>
> 當可以調整限制時，資料表會包含**預設限制**和**最大限制**標頭。 限制可以高於預設限制，但高於最大限制。
>
> 如果您想要將限制或配額提升到預設限制以上，請免費[開啟線上客戶支援要求](../templates/error-resource-quota.md)。

[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)訂用帳戶不符合限制或配額增加的資格。 如果您有[免費試用訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0044p)，則可以升級到[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)訂用帳戶。 如需詳細資訊，請參閱將[Azure 免費試用訂用帳戶升級為隨用隨付訂](../../cost-management-billing/manage/upgrade-azure-subscription.md)用帳戶和[免費試用訂](https://azure.microsoft.com/free/free-account-faq)用帳戶常見問題。

某些限制是在區域層級管理。

讓我們以 vCPU 配額為例。 若要要求增加對個 vcpu 支援的配額，您必須決定要在哪些區域中使用多少個 vcpu。 然後，針對您想要的數量和區域，對 Azure 資源群組 vCPU 配額提出特定要求。 如果您需要在西歐使用30個 vcpu 來執行應用程式，您會在西歐特別要求30個 vcpu。 您的 vCPU 配額不會在任何其他區域中增加，只有西歐具有 30 vCPU 的配額。

因此，在任何一個區域中決定您的工作負載所需的 Azure 資源群組配額。 然後在您要部署的每個區域中要求該金額。 如需如何判斷您目前的特定區域配額的說明，請參閱[解決資源配額的錯誤](../templates/error-resource-quota.md)。

## <a name="general-limits"></a>一般限制

如需資源名稱的限制，請參閱[Azure 資源的命名規則和限制](resource-name-rules.md)。

如需 Resource Manager API 讀取和寫入限制的相關資訊，請參閱[對 Resource Manager 要求進行節流](request-limits-and-throttling.md)。

### <a name="management-group-limits"></a>管理群組限制

下列限制適用于[管理群組](../../governance/management-groups/overview.md)。

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>訂用帳戶限制

當您使用 Azure Resource Manager 和 Azure 資源群組時，適用下列限制。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>資源群組限制

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

## <a name="azure-cloud-services-limits"></a>Azure 雲端服務限制

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Azure 認知搜尋限制

定價層會決定搜尋服務的容量和限制。 層級包括：

* 與其他 Azure 訂閱者共用的**免費**多租使用者服務，適用于評估和小型開發專案。
* **** 可針對規模較小的生產工作負載提供專用的計算資源，以及針對高可用性的查詢工作負載提供最多 3 個複本。
* **標準**（包括 S1、S2、S3 及 s3 高密度）適用于較大型的生產工作負載。 標準層中有多個層級，因此您可以選擇最符合您工作負載設定檔的資源設定。

**每一訂用帳戶的限制**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**每個搜尋服務的限制**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

若要深入瞭解更細微層級的限制，例如檔案大小、每秒的查詢數、金鑰、要求和回應，請參閱[Azure 認知搜尋中的服務限制](../../search/search-limits-quotas-capacity.md)。

## <a name="azure-cognitive-services-limits"></a>Azure 認知服務限制

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB 限制

如 Azure Cosmos DB 限制，請參閱[Azure Cosmos DB 的限制](../../cosmos-db/concepts-limits.md)。

## <a name="azure-data-explorer-limits"></a>Azure 資料總管限制

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫

如需適用於 MySQL 的 Azure 資料庫限制詳細資訊，請參閱[適用於 MySQL 的 Azure 資料庫中的限制](../../mysql/concepts-limits.md)。

## <a name="azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫

如需適用於 PostgreSQL 的 Azure 資料庫限制詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫中的限制](../../postgresql/concepts-limits.md)。

## <a name="azure-functions-limits"></a>Azure Functions 限制

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

如需詳細資訊，請參閱[函數主控方案比較](../../azure-functions/functions-scale.md#hosting-plans-comparison)。

## <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes Service 限制

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure Machine Learning 限制

您可以在 [ [Azure Machine Learning 配額] 頁面](../../machine-learning/how-to-manage-quotas.md)中找到 Azure Machine Learning 計算配額的最新值

## <a name="azure-maps-limits"></a>Azure 地圖服務限制

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Azure 監視器限制

### <a name="alerts"></a>警示

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>動作群組

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>記錄查詢和語言

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics 工作區

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Azure 原則限制

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Azure SignalR Service 限制

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>備份限制

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Batch 限制

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>傳統部署模型限制

如果您使用傳統部署模型，而不是 Azure Resource Manager 部署模型，則適用下列限制。

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Container Instances 限制

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Container Registry 登入

下表詳細說明基本、標準和 Premium[服務層級](../../container-registry/container-registry-skus.md)的功能和限制。

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>內容傳遞網路限制

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

## <a name="digital-twins-limits"></a>數位 Twins 限制

> [!NOTE]
> 此服務的某些區域具有可調整的限制，有些則沒有。 這會在下表中以可*調整的？* 資料行來表示。 可以調整限制時，可調整的 *？* 值為 *[是]*。

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>事件方格限制

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>事件中樞限制

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Identity Manager 限制

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>IoT Central 限制
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT 中樞限制

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>IoT 中樞裝置佈建服務限制

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>金鑰保存庫限制

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>媒體服務限制

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>媒體服務 v2 (舊版)

如需媒體服務 v2 (舊版) 特有的限制，請參閱[媒體服務 v2 (舊版) ](../../media-services/previous/media-services-quotas-and-limitations.md)

## <a name="mobile-services-limits"></a>行動服務限制

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>多重要素驗證限制

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>網路限制

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>ExpressRoute 限制

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>虛擬 WAN 限制

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

### <a name="azure-bastion-limits"></a>Azure 防禦限制

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Azure DNS 限制

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Azure 防火牆限制

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Azure Front Door Service 限制

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>通知中樞限制

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>角色型存取控制限制

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>服務匯流排限制

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Site Recovery 限制

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>SQL Database 限制

如 SQL Database 限制，請參閱[單一資料庫的 SQL Database 資源限制](../../azure-sql/database/resource-limits-vcore-single-databases.md)、彈性集區和集區[資料庫的 SQL Database 資源](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)限制，以及[SQL 受控執行個體的 SQL Database 資源限制](../../azure-sql/managed-instance/resource-limits.md)。

## <a name="azure-synapse-analytics-limits"></a>Azure Synapse 分析限制

如需 Azure Synapse 分析限制，請參閱[Azure Synapse 資源限制](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)。

## <a name="storage-limits"></a>儲存體限制

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

如需標準儲存體帳戶限制的詳細資訊，請參閱[標準儲存體帳戶的擴充性目標](../../storage/common/scalability-targets-standard-account.md)。

### <a name="storage-resource-provider-limits"></a>儲存體資源提供者限制

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Azure Blob 儲存體的限制

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Azure 檔案的限制

如需 Azure 檔案儲存體限制的詳細資訊，請參閱[Azure 檔案儲存體擴充性和效能目標](../../storage/files/storage-files-scale-targets.md)。

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

如需詳細資訊，請參閱[虛擬機器大小](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

#### <a name="disk-encryption-sets"></a>磁片加密集

每個訂用帳戶的每個區域都有50個磁片加密集的限制。 如需詳細資訊，請參閱[Linux](../../virtual-machines/linux/disk-encryption.md#restrictions)或[Windows](../../virtual-machines/windows/disk-encryption.md#restrictions)虛擬機器的加密檔。 如果您需要增加配額，請聯絡 Azure 支援。

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

當您使用 Azure Resource Manager 和 Azure 資源群組時，適用下列限制。

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>共用映射資源庫限制

使用共用映射庫部署資源時，每個訂用帳戶都有限制：

- 每個區域中每個訂用帳戶 100 個共用映像庫
- 每個區域中每個訂用帳戶 1000 個映像定義
- 每個區域中每個訂用帳戶 10000 個映像版本

## <a name="virtual-machine-scale-sets-limits"></a>虛擬機器擴展集限制

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>另請參閱

* [瞭解 Azure 限制和增加](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Azure 的虛擬機器和雲端服務大小](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure 雲端服務的大小](../../cloud-services/cloud-services-sizes-specs.md)
* [Azure 資源的命名規則和限制](resource-name-rules.md)