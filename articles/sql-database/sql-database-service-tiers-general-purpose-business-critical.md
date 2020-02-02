---
title: 一般用途和業務關鍵
description: 本文討論以 vCore 為基礎的購買模型中的一般用途和商務關鍵服務層級。
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
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937847"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database 服務層級

Azure SQL Database 是以針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保99.99% 的可用性，即使發生基礎結構失敗也是一樣。 Azure SQL Database 中會使用三個服務層級，每個都有不同的架構模型。 這些服務層級為：

- [一般用途](sql-database-service-tier-general-purpose.md)，專為以預算為導向的工作負載而設計。
- [超大規模資料庫](sql-database-service-tier-hyperscale.md)是針對大部分的商務工作負載所設計，可提供高度可擴充的儲存體、讀取向外延展，以及快速的資料庫還原功能。
- [業務關鍵](sql-database-service-tier-business-critical.md)，專為低延遲工作負載所設計，其具有高彈性的失敗和快速容錯移轉。

這篇文章討論在 vCore 為基礎的購買模型中，在一般用途和業務關鍵服務層的服務層級、儲存體和備份考慮方面的差異。

## <a name="service-tier-comparison"></a>服務層級比較

下表描述最新一代（第5代）服務層級之間的主要差異。 請注意，單一資料庫和受控執行個體中的服務層特性可能會有所不同。

| | 資源類型 | 一般用途 |  Hyperscale | 業務關鍵 |
|:---:|:---:|:---:|:---:|:---:|
| **適用對象** | |  提供以預算為導向且平衡的計算與儲存體選項。 | 大部分的商業工作負載。 自動調整儲存體大小，最高可達 100 TB，流暢的垂直和水準計算調整，快速的資料庫還原。 | 具有高交易率和低 IO 延遲的 OLTP 應用程式。 為失敗提供最高的復原能力，並使用多個同步更新的複本快速容錯移轉。|
|  **適用于資源類型：** ||單一資料庫/彈性集區/受控執行個體 | 單一資料庫 | 單一資料庫/彈性集區/受控執行個體 |
| **計算大小**|單一資料庫/彈性集區 | 1 到 80 個虛擬核心 | 1到80虛擬核心 | 1 到 80 個虛擬核心 |
| | 受控執行個體 | 4、8、16、24、32、40、64、80虛擬核心 | N/A | 4、8、16、24、32、40、64、80虛擬核心 |
| | 受控實例集區 | 2、4、8、16、24、32、40、64、80虛擬核心 | N/A | N/A |
| **儲存體類型** | 所有 | 進階遠端儲存體 (每個執行個體) | 與本機 SSD 快取分離的儲存體 (每個執行個體) | 超快速本機 SSD 儲存體 (每個執行個體) |
| **資料庫大小** | 單一資料庫/彈性集區 | 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 受控執行個體  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **儲存體大小** | 單一資料庫/彈性集區 | 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 受控執行個體  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **TempDB 大小** | 單一資料庫/彈性集區 | [每個 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [每個 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [每個 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | 受控執行個體  | [每個 vCore 24 GB](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | 最多 4 TB-[依儲存體大小限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **記錄寫入輸送量** | 單一資料庫 | [每個 vCore 1.875 MB/秒（最多 30 MB/秒）](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/秒 | [每個 vCore 6 MB/s （最大 96 MB/秒）](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | 受控執行個體 | [每個 vCore 3 MB/s （最大 22 MB/秒）](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | [每個 vcore 4 MB/s （最大 48 MB/秒）](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**可用性**|所有| 99.99% |  [具有一個次要複本的99.95%，還有更多複本的99.99%](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99.99% <br/> [具有區域冗余單一資料庫的99.995%](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**備份**|所有|RA-GRS、7-35 天 (預設為 7 天)| GRS、7天、固定時間點恢復（PITR） | RA-GRS、7-35 天 (預設為 7 天) |
|**記憶體內部 OLTP** | | N/A | N/A | 可用 |
|**唯讀複本**| | 0內建 <br> 0-4 使用[異地](sql-database-active-geo-replication.md)複寫 | 0-4 內建 | 1個內建，包含在價格中 <br> 0-4 使用[異地](sql-database-active-geo-replication.md)複寫 |
|**定價/計費** | 單一資料庫 | [vCore、保留的儲存體和備份儲存體](https://azure.microsoft.com/pricing/details/sql-database/single/)會收費。 <br/>IOPS 不會收費。 | [每個複本的 vCore 和使用的儲存體](https://azure.microsoft.com/pricing/details/sql-database/single/)都會收費。 <br/>IOPS 尚未收費。 | [vCore、保留的儲存體和備份儲存體](https://azure.microsoft.com/pricing/details/sql-database/single/)會收費。 <br/>IOPS 不會收費。 |
|| 受控執行個體 | [vCore、保留的儲存體和備份儲存體](https://azure.microsoft.com/pricing/details/sql-database/managed/)會收費。 <br/>不收取 IOPS 費用| N/A | [vCore、保留的儲存體和備份儲存體](https://azure.microsoft.com/pricing/details/sql-database/managed/)會收費。 <br/>IOPS 不會收費。| 
|**折扣模型**| | [保留的實例](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) （不適用於開發/測試訂閱）<br/>[企業](https://azure.microsoft.com/offers/ms-azr-0148p/)與[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0023p/)開發/測試訂閱| [Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) （不適用於開發/測試訂閱）<br/>[企業](https://azure.microsoft.com/offers/ms-azr-0148p/)與[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0023p/)開發/測試訂閱| [保留的實例](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) （不適用於開發/測試訂閱）<br/>[企業](https://azure.microsoft.com/offers/ms-azr-0148p/)與[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0023p/)開發/測試訂閱|

如需詳細資訊，請參閱單一資料庫中的服務層[級（vCore）](sql-database-vcore-resource-limits-single-databases.md)、[單一資料庫集區（vCore）](sql-database-dtu-resource-limits-single-databases.md)、[單一資料庫（dtu）](sql-database-dtu-resource-limits-single-databases.md)、[單一資料庫集區（dtu）](sql-database-dtu-resource-limits-single-databases.md)和[受控執行個體](sql-database-managed-instance-resource-limits.md)頁面中的詳細差異。

> [!NOTE]
> 如需有關以 vCore 為基礎的購買模型中超大規模資料庫服務層級的詳細資訊，請參閱[超大規模資料庫服務層級](sql-database-service-tier-hyperscale.md)。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-purchase-models.md)。

## <a name="data-and-log-storage"></a>資料和記錄儲存體

下列因素會影響用於資料和記錄檔的儲存空間量，並適用于一般用途和商務關鍵性。 如需超大規模資料庫中資料和記錄儲存體的詳細資訊，請參閱[超大規模資料庫服務層級](sql-database-service-tier-hyperscale.md)。

- 已配置的儲存體會由資料檔案（MDF）和記錄檔（LDF）使用。
- 每個單一資料庫計算大小都支援資料庫大小上限，預設大小上限為 32 GB。
- 當您設定所需的單一資料庫大小（MDF 檔案的大小）時，將會自動新增額外的30% 的儲存體，以支援 LDF 檔案。
- SQL Database 受控實例的儲存體大小必須以 32 GB 的倍數來指定。
- 您可以選取 10 GB 到支援的最大值之間的任何單一資料庫大小。
  - 針對標準或一般用途服務層級中的儲存體，增加或減少大小（以 10 GB 為增量單位）。
  - 針對 premium 或業務關鍵服務層級中的儲存體，增加或減少大小（以 250-GB 為單位）。
- 在一般用途服務層級中，`tempdb` 會使用連接的 SSD，而此儲存成本會包含在 vCore 價格中。
- 在業務關鍵服務層級中，`tempdb` 會與 MDF 和 LDF 檔案共用附加的 SSD，而 `tempdb` 儲存體成本則包含在 vCore 價格中。

> [!IMPORTANT]
> 系統會向您收取配置給 MDF 和 LDF 檔案的總儲存體費用。

若要監視您的 MDF 和 LDF 檔案的目前大小總計，請使用[sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要監視個別 MDF 和 LDF 檔案的目前大小，請使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>備份和儲存體

會設定資料庫備份的儲存體，以支援 SQL Database 的時間點還原（PITR）和[長期保留（LTR）](sql-database-long-term-retention.md)功能。 這個儲存體會分別配置給每個資料庫，並以兩個不同的每一資料庫費用來計費。

- **PITR**：個別的資料庫備份會自動複製到[讀取權限異地冗余（RA-GRS）儲存體](../storage/common/storage-designing-ha-apps-with-ragrs.md)。 當建立新的備份時，儲存體大小會以動態方式增加。 每週完整備份、每日差異備份和交易記錄備份都使用此儲存體，每5分鐘複製一次。 儲存體耗用量取決於資料庫的變更率和備份的保留期限。 您可以為每個資料庫設定 7 到 35 天的不同保留期限。 最小儲存體數量等於資料庫大小的100% （1x），免費提供。 對於大多數資料庫來說，此數量就足以儲存 7 天份的備份。
- **LTR**： SQL Database 提供選項，讓您設定完整備份的長期保留期，最長可達10年。 如果您設定 LTR 原則，這些備份會自動儲存在 GRS 儲存體中，但您可以控制複本備份的頻率。 若要符合不同的合規性需求，您可以針對每週、每月和/或每年備份選取不同的保留週期。 您選擇的設定會決定要將多少儲存體用於 LTR 備份。 若要估計 LTR 儲存體的成本，您可以使用 LTR 定價計算機。 如需詳細資訊，請參閱[SQL Database 長期保留](sql-database-long-term-retention.md)。

## <a name="next-steps"></a>後續步驟

- 如需一般用途和業務關鍵服務層級中，單一資料庫可用的特定計算大小和儲存體大小的詳細資訊，請參閱[針對單一資料庫 SQL Database vCore 為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 如需一般用途和業務關鍵服務層級中彈性集區可用的特定計算大小和儲存體大小的詳細資訊，請參閱[針對彈性集區 SQL Database vCore 為基礎的資源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
