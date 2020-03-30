---
title: 通用和業務關鍵型
description: 本文討論了基於 vCore 的採購模型中的通用和業務關鍵服務層。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937847"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database 服務層級

Azure SQL 資料庫基於 SQL Server 資料庫引擎體系結構，該體系結構根據雲環境進行了調整，以確保 99.99% 的可用性，即使出現基礎結構故障也是如此。 Azure SQL 資料庫中使用三個服務層，每個層具有不同的體系結構模型。 這些服務層級為：

- [通用](sql-database-service-tier-general-purpose.md)，專為面向預算的工作負載而設計。
- [Hyperscale](sql-database-service-tier-hyperscale.md)專為大多數業務工作負載而設計，提供高度可擴展的存儲、讀取橫向擴展和快速資料庫還原功能。
- [業務關鍵](sql-database-service-tier-business-critical.md)型 ，專為低延遲工作負載而設計，具有高故障彈性和快速容錯移轉。

本文討論了在基於 vCore 的採購模型中，針對一般用途和業務關鍵服務層，對服務層、存儲和備份注意事項進行補點的差異。

## <a name="service-tier-comparison"></a>服務層比較

下表描述了最新一代 （Gen5） 的服務層之間的主要差異。 請注意，在單一資料庫和託管實例中，服務層特徵可能不同。

| | 資源類型 | 一般用途 |  超大規模資料庫 | 業務關鍵 |
|:---:|:---:|:---:|:---:|:---:|
| **適用對象** | |  提供以預算為導向且平衡的計算與儲存體選項。 | 大部分的商業工作負載。 自動縮放存儲大小高達 100 TB，流體垂直和水準計算縮放，快速資料庫恢復。 | 具有高事務速率和低 IO 延遲的 OLTP 應用程式。 使用多個同步更新的副本提供對故障和快速容錯移轉的最高彈性。|
|  **提供資源類型：** ||單一資料庫/彈性集區/受控執行個體 | 單一資料庫 | 單一資料庫/彈性集區/受控執行個體 |
| **計算大小**|單一資料庫/彈性集區 | 1 到 80 個虛擬核心 | 1 到 80 vCore | 1 到 80 個虛擬核心 |
| | 受控執行個體 | 4， 8， 16， 24， 32， 40， 64， 80 vCores | N/A | 4， 8， 16， 24， 32， 40， 64， 80 vCores |
| | 託管實例池 | 2， 4， 8， 16， 24， 32， 40， 64， 80 vCores | N/A | N/A |
| **儲存體類型** | 全部 | 進階遠端儲存體 (每個執行個體) | 與本機 SSD 快取分離的儲存體 (每個執行個體) | 超快速本機 SSD 儲存體 (每個執行個體) |
| **資料庫大小** | 單一資料庫/彈性集區 | 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 受控執行個體  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **儲存體大小** | 單一資料庫/彈性集區 | 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 受控執行個體  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **TempDB 大小** | 單一資料庫/彈性集區 | [每個 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [每個 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [每個 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | 受控執行個體  | [每個 vCore 24 GB](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | 高達 4 TB -[受存儲大小限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **日誌寫入輸送量** | 單一資料庫 | [每 vCore 1.875 MB/s（最大 30 MB/s）](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/秒 | [每個 vCore 的 6 MB/s（最大 96 MB/s）](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | 受控執行個體 | [每個 vCore 3 MB/s（最大 22 MB/s）](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | [每個 vcore 的 4 MB/s（最大 48 MB/s）](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**可用性**|全部| 99.99% |  [99.95% 與一個輔助副本， 99.99% 與更多的副本](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99.99% <br/> [99.995% 帶區域冗余單資料庫](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**備份**|全部|RA-GRS、7-35 天 (預設為 7 天)| RA-GRS，7天，恒定時間點恢復（PITR） | RA-GRS、7-35 天 (預設為 7 天) |
|**記憶體中 OLTP** | | N/A | N/A | 可用 |
|**唯讀副本**| | 0 內置 <br> 0 - 4 使用[異地複製](sql-database-active-geo-replication.md) | 0 - 4 內置 | 1 內置，包含在價格中 <br> 0 - 4 使用[異地複製](sql-database-active-geo-replication.md) |
|**定價/計費** | 單一資料庫 | [vCore、預留存儲和備份存儲](https://azure.microsoft.com/pricing/details/sql-database/single/)都收費。 <br/>IOPS 不收費。 | [每個副本和已使用的存儲的 vCore](https://azure.microsoft.com/pricing/details/sql-database/single/)都會收費。 <br/>IOPS 尚未收費。 | [vCore、預留存儲和備份存儲](https://azure.microsoft.com/pricing/details/sql-database/single/)都收費。 <br/>IOPS 不收費。 |
|| 受控執行個體 | [vCore、預留存儲和備份存儲](https://azure.microsoft.com/pricing/details/sql-database/managed/)已收費。 <br/>未收取 IOPS| N/A | [vCore、預留存儲和備份存儲](https://azure.microsoft.com/pricing/details/sql-database/managed/)已收費。 <br/>IOPS 不收費。| 
|**折扣模型**| | [保留實例](sql-database-reserved-capacity.md)<br/>[Azure 混合優勢](sql-database-azure-hybrid-benefit.md)（在開發/測試訂閱上不可用）<br/>[企業和](https://azure.microsoft.com/offers/ms-azr-0148p/)[即用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)開發/測試訂閱| [Azure 混合優勢](sql-database-azure-hybrid-benefit.md)（在開發/測試訂閱上不可用）<br/>[企業和](https://azure.microsoft.com/offers/ms-azr-0148p/)[即用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)開發/測試訂閱| [保留實例](sql-database-reserved-capacity.md)<br/>[Azure 混合優勢](sql-database-azure-hybrid-benefit.md)（在開發/測試訂閱上不可用）<br/>[企業和](https://azure.microsoft.com/offers/ms-azr-0148p/)[即用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)開發/測試訂閱|

有關詳細資訊，請參閱[單個資料庫 （vCore）、](sql-database-vcore-resource-limits-single-databases.md)[單個資料庫池 （vCore）、](sql-database-dtu-resource-limits-single-databases.md)[單個資料庫 （DTU）、](sql-database-dtu-resource-limits-single-databases.md)[單個資料庫池 （DTU）](sql-database-dtu-resource-limits-single-databases.md)和[託管實例](sql-database-managed-instance-resource-limits.md)頁中的服務層之間的詳細差異。

> [!NOTE]
> 有關基於 vCore 的採購模型中的超大規模服務層的資訊，請參閱[超大規模服務層](sql-database-service-tier-hyperscale.md)。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-purchase-models.md)。

## <a name="data-and-log-storage"></a>資料和記錄儲存體

以下因素會影響用於資料和日誌檔的存儲量，並適用于通用和業務關鍵型。 有關超大規模中的資料和日誌存儲的詳細資訊，請參閱[超大規模服務層](sql-database-service-tier-hyperscale.md)。

- 分配的存儲由資料檔案 （MDF） 和日誌檔 （LDF） 使用。
- 每個單個資料庫計算大小都支援最大資料庫大小，預設最大大小為 32 GB。
- 配置所需的單個資料庫大小（MDF 檔的大小）時，將自動添加 30% 的額外存儲以支援 LDF 檔。
- SQL 資料庫託管實例的存儲大小必須以 32 GB 的倍數指定。
- 您可以選擇 10 GB 和受支援最大值之間的任何單個資料庫大小。
  - 對於標準或泛型服務層中的存儲，以 10 GB 的增量增加或減小大小。
  - 對於高級或業務關鍵型服務層中的存儲，以 250 GB 的增量增加或減小大小。
- 在泛型服務層中，`tempdb`使用附加的 SSD，此存儲成本包含在 vCore 價格中。
- 在業務關鍵型服務層中`tempdb`，將附加的 SSD 與 MDF 和 LDF 檔共用，`tempdb`並且存儲成本包含在 vCore 價格中。

> [!IMPORTANT]
> 您需要為 MDF 和 LDF 檔分配的存儲總額付費。

要監視 MDF 和 LDF 檔的當前總大小，請使用[sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要監視個別 MDF 和 LDF 檔案的目前大小，請使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 有關詳細資訊，請參閱在[Azure SQL 資料庫中管理檔空間](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>備份和儲存體

為資料庫備份分配存儲以支援 SQL 資料庫的時間點還原 （PITR）[和長期保留 （LTR）](sql-database-long-term-retention.md)功能。 這個儲存體會分別配置給每個資料庫，並以兩個不同的每一資料庫費用來計費。

- **PITR**：單個資料庫備份將自動複製到[讀取存取異地冗余 （RA-GRS） 存儲](../storage/common/storage-designing-ha-apps-with-ragrs.md)。 隨著創建新備份，存儲大小會動態增加。 存儲由每週完整備份、每日差異備份和事務記錄備份使用，每 5 分鐘複製一次。 存儲消耗取決於資料庫的更改速率和備份的保留期。 您可以為每個資料庫設定 7 到 35 天的不同保留期限。 提供至少相當於資料庫大小的 100% （1x） 的存儲量，無需支付額外費用。 對於大多數資料庫來說，此數量就足以儲存 7 天份的備份。
- **LTR**：SQL 資料庫為您提供了配置完整備份長期保留長達 10 年的選項。 如果設置了 LTR 策略，這些備份將自動存儲在 RA-GRS 存儲中，但您可以控制複本備份的頻率。 為了滿足不同的合規性要求，您可以選擇不同的每週、每月和/或年度備份的保留期。 您選擇的配置決定了用於 LTR 備份的存儲量。 要估計 LTR 存儲的成本，可以使用 LTR 定價計算機。 有關詳細資訊，請參閱[SQL 資料庫長期保留](sql-database-long-term-retention.md)。

## <a name="next-steps"></a>後續步驟

- 有關一般用途和業務關鍵服務層中單個資料庫可用的特定計算大小和存儲大小的詳細資訊，請參閱[單個資料庫的 SQL 資料庫 vCore 資源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 有關一般用途和業務關鍵服務層中可用於彈性池的特定計算大小和存儲大小的詳細資訊，請參閱[彈性池的 SQL 資料庫 vCore 資源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
