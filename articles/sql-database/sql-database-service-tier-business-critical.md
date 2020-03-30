---
title: 業務關鍵服務層級
description: 了解 Azure SQL Database 業務關鍵層
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: fc328c34c1543a75fdc885087d44b28e24c0850a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268674"
---
# <a name="business-critical-tier---azure-sql-database"></a>業務關鍵層 - Azure SQL Database

> [!NOTE]
> 在 DTU 購買模型中，業務關鍵層稱為進階層。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-purchase-models.md)。

Azure SQL Database 是以會針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保 99.99% 的可用性 (即使在基礎結構失敗的情況下)。 Azure SQL Database 中使用三個架構模型：
- 一般目的/標準 
- 業務關鍵/進階
- 超大規模資料庫

進階/業務關鍵服務層級模型會以資料庫引擎程序的叢集作為基礎。 此架構模型依賴事實上一律有法定的可用資料庫引擎節點數，而且對您工作負載的效能影響最小 (即使在維護活動期間亦然)。

Azure 透明地升級和修補基礎作業系統、驅動程式及 SQL Server Database Engine，為使用者將停機時間降到最低。 

進階可用性在 Azure SQL Database 的進階和商務關鍵性服務層級中啟用，是專為密集工作負載而設計，這些工作負載無法負擔因正在進行的維護作業所導致的任何效能影響。

在進階模式中，Azure SQL 資料庫於單一節點上整合了計算和儲存體。 使用類似 SQL Server [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技術，複寫四節點叢集中部署的計算 (SQL Server 資料庫引擎程序) 和儲存體 (本機連結的 SSD)，可達到此架構模型中的高可用性。

![資料庫引擎節點的叢集](media/sql-database-managed-instance/business-critical-service-tier.png)

SQL 資料庫引擎程序和基礎 mdf/ldf 檔案都放在具有本機連接 SSD 儲存體的同一節點上，為您的工作負載提供低延遲。 使用類似 SQL Server [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技術，實作高可用性。 每個資料庫都屬於資料庫節點叢集，其中一個主要資料庫可供客戶工作負載存取，還有三個包含資料副本的次要流程。 主要節點持續將更改推送到次要節點，以確保主要節點因故損毀時，次要複本仍可提供資料。 容錯轉移由 SQL Server Database Engine 處理 - 一個次要複本成為主要節點，並建立新的次要複本，以確保叢集中有足夠的節點。 工作負載會自動重新導向至新的主要節點。

此外，商務關鍵性叢集具有內建的[讀取縮放](sql-database-read-scale-out.md)功能，會提供免費的內建唯讀節點，該節點可用於執行不會影響主要工作負載效能的唯讀狀態 (例如報告)。

## <a name="when-to-choose-this-service-tier"></a>選擇此服務層級的時機？

業務關鍵服務層級專門用於有下列需求的應用程式：基礎 SSD 儲存體要有低延遲的回應 (平均 1 至 2 毫秒)、能夠在基礎結構失敗時快速復原，或需要將報告、分析和唯讀查詢卸載至主要資料庫的免費可讀次要複本。

選擇業務關鍵型服務層而不是通用層的主要原因有：
-   低 IO 延遲要求 – 需要存儲層快速回應的工作負載（平均 1-2 毫秒）應使用業務關鍵層。 
-   應用程式和資料庫之間頻繁通信。 不能利用應用程式層緩存或[請求批次處理](sql-database-use-batching-to-improve-performance.md)，並且需要發送許多必須快速處理的 SQL 查詢的應用程式是業務關鍵層的良好候選項。
-   大量更新 – 插入、更新和刪除操作會修改記憶體中的資料頁（髒頁），這些資料頁必須保存到具有`CHECKPOINT`操作的資料檔案中。 潛在的資料庫引擎進程崩潰或資料庫容錯移轉與大量髒頁可能會增加恢復時間在通用層。 如果您的工作負載導致許多記憶體中更改，請使用"業務關鍵"層。 
-   修改資料的長時間運行的事務。 長時間打開的事務可防止日誌檔的截斷，這可能會增加日誌大小和[虛擬日誌檔 （VLF）](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)的數量。 大量 VLF 會降低容錯移轉後資料庫的恢復速度。
-   具有可重定向到免費輔助唯讀副本的報告和分析查詢的工作負載。
- 更高的彈性和更快的故障恢復速度。 在系統發生故障的情況下，主實例上的資料庫將被禁用，其中一個輔助副本將立即成為新的讀寫主資料庫，可用於處理查詢。 資料庫引擎不需要從日誌檔中分析和重做事務，並將所有資料載入到記憶體緩衝區中。
- 高級資料損壞保護 - 業務關鍵層利用資料庫副本進行後臺業務連續性，因此該服務還利用自動頁面修復，這是用於 SQL Server 資料庫[鏡像和可用性組](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)的相同技術。 如果副本由於資料完整性問題而無法讀取頁面，將從另一個副本檢索該頁的新副本，替換不可讀的頁面，而不會遺失資料或客戶停機。 如果資料庫具有異地輔助副本，此功能在通用層中適用。
- 更高的可用性 - 多 AZ 配置中的業務關鍵層保證 99.995% 的可用性，而通用層為 99.99%。
- 快速異地恢復 - 使用異地複製配置的業務關鍵型層具有 5 秒的保證復原點目標 （RPO），恢復時間目標 （RTO） 為 30 秒，部署時數為 100%。

## <a name="next-steps"></a>後續步驟

- 在[託管實例](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)中查找業務關鍵型層的資源特徵（內核數、IO、記憶體數），[在 vCore 模型](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4)或[DTU 模型中](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)查找單個資料庫，或在[vCore 模型](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4)和[DTU 模型中](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits)查找彈性池。
- 了解[一般目的](sql-database-service-tier-general-purpose.md)和[超大規模資料庫](sql-database-service-tier-hyperscale.md)層。
- 瞭解[服務結構](../service-fabric/service-fabric-overview.md)。
- 有關高可用性和災害復原的更多選項，請參閱[業務連續性](sql-database-business-continuity.md)。
