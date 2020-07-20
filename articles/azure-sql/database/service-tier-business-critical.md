---
title: 業務關鍵服務層級
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 瞭解 Azure SQL Database 和 Azure SQL 受控執行個體的商務關鍵服務層級。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 0067811316a8afd26828050d81215ecb5748c841
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986685"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>業務關鍵層-Azure SQL Database 和 Azure SQL 受控執行個體 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 業務關鍵層在 DTU 購買模型中稱為 Premium。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](purchasing-models.md)。

Azure SQL Database 和 Azure SQL 受控執行個體都是以針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保99.99% 的可用性（即使在基礎結構失敗的情況下）。 使用的架構模型有三種：
- 一般目的/標準 
- 業務關鍵/進階
- 超大規模資料庫

進階/業務關鍵服務層級模型會以資料庫引擎程序的叢集作為基礎。 此架構模型依賴事實上一律有法定的可用資料庫引擎節點數，而且對您工作負載的效能影響最小 (即使在維護活動期間亦然)。 超大規模資料庫服務層級目前僅適用于 Azure SQL Database （非 SQL 受控執行個體），而且是可高度擴充的儲存體和計算效能層，可利用 Azure 架構將資料庫的儲存體和計算資源向外延展，而 Azure SQL Database 遠遠超出一般用途和業務關鍵服務層級的限制。

Azure 會針對一般使用者以最短的停機時間，明確地升級和修補基礎作業系統、驅動程式和 SQL Server 資料庫引擎。 

Premium 和商務關鍵服務層級中已啟用 premium 可用性，其設計目的是為了耗費大量工作負載，因為進行中的維護作業，無法容忍任何效能影響。

計算和儲存體會在 premium 模型的單一節點上整合。 使用類似于 SQL Server [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技術，複寫部署到四個節點叢集的計算（SQL Server 資料庫引擎進程）和儲存體（本機連結的 SSD），即可達到此架構模型中的高可用性。

![資料庫引擎節點的叢集](./media/service-tier-business-critical/business-critical-service-tier.png)

SQL Server 資料庫引擎進程和基礎 .mdf/.ldf 檔案都放在具有本機連接 SSD 儲存體的相同節點上，為您的工作負載提供低延遲。 高可用性會使用類似于 SQL Server [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技術來執行。 每個資料庫都是資料庫節點的叢集，其中有一個主資料庫可供客戶工作負載存取，以及三個包含資料複本的次要進程。 主要節點會持續將變更推送至次要節點，以確保如果主要節點因任何原因而失敗，次要複本上的資料就可供使用。 容錯移轉是由 SQL Server 資料庫引擎處理–一個次要複本成為主要節點，並建立新的次要複本，以確保叢集中有足夠的節點。 工作負載會自動重新導向至新的主要節點。

此外，商務關鍵性叢集具有內建的[讀取縮放](read-scale-out.md)功能，會提供免費的內建唯讀節點，該節點可用於執行不會影響主要工作負載效能的唯讀狀態 (例如報告)。

## <a name="when-to-choose-this-service-tier"></a>選擇此服務層級的時機

商務關鍵服務層級是針對需要基礎 SSD 儲存體的低延遲回應（平均1-2 毫秒）的應用程式所設計，如果基礎結構失敗，則快速復原，或需要將報告、分析和唯讀查詢，載入到主資料庫的免費可讀取次要複本。

您應該選擇「商務關鍵服務層級」而非「一般用途層」的主要原因如下：
-   **低 i/o 延遲需求**–需要儲存層快速回應的工作負載（平均1-2 毫秒）應使用業務關鍵層。 
-   **應用程式與資料庫之間的頻繁通訊**。 無法利用應用層快取或[要求批次處理](../performance-improve-use-batching.md)，而且需要傳送許多必須快速處理之 SQL 查詢的應用程式，都是適用于業務關鍵層的絕佳候選項目。
-   **大量更新**–插入、更新和刪除作業會修改記憶體中的資料頁（中途分頁），必須使用作業儲存到資料檔案中 `CHECKPOINT` 。 可能是資料庫引擎進程損毀，或具有大量中途分頁的資料庫容錯移轉，可能會增加一般用途層的復原時間。 如果您的工作負載會導致許多記憶體中的變更，請使用「業務關鍵層」。 
-   **修改資料的長時間執行交易**。 已開啟較長時間的交易會導致記錄檔截斷，這可能會增加記錄檔大小和[虛擬記錄檔（VLF）](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)的數目。 在容錯移轉之後，大量的 Vlf 可能會減緩資料庫復原的速度。
-   **具有報表和分析查詢的工作負載**，可重新導向至免費的次要唯讀複本。
- **更高的復原能力和更快速的損毀修復**。 在系統失敗的情況下，將會停用主要實例上的資料庫，而且其中一個次要複本會立即成為新的讀寫主資料庫，準備好處理查詢。 資料庫引擎不需要分析和重做記錄檔中的交易，而且會載入記憶體緩衝區中的所有資料。
- **先進的資料損毀保護**。 業務關鍵層會針對商務持續性目的，利用幕後的資料庫複本，因此服務也會利用自動修復頁面，這是用於 SQL Server 資料庫[鏡像和可用性群組](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)的相同技術。 當複本因為資料完整性問題而無法讀取頁面時，將會從另一個複本抓取頁面的全新複本，取代無法讀取的頁面，而不會遺失資料或客戶停機時間。 如果資料庫具有異地次要複本，這項功能適用于一般用途層。
- 相較于一般用途層的99.99%，多重 AZ 設定中的**高可用性**-商務關鍵層保證99.995% 的可用性。
- 以異地複寫設定的**快速異地**復原-商務關鍵層，其保證的復原點目標（RPO）為5秒，而30秒的復原時間目標（RTO）為100% 的已部署時數。

## <a name="next-steps"></a>後續步驟

- 尋找[SQL 受控執行個體](../managed-instance/resource-limits.md#service-tier-characteristics)中業務關鍵層的資源特性（核心數目、i/o、記憶體）、 [vCore 模型](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4)中的單一資料庫或[dtu 模型](resource-limits-dtu-single-databases.md#premium-service-tier)，或[VCore 模型](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4)和[dtu 模型](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits)中的彈性集區。
- 了解[一般目的](service-tier-general-purpose.md)和[超大規模資料庫](service-tier-hyperscale.md)層。
- 深入瞭解[Service Fabric](../../service-fabric/service-fabric-overview.md)。
- 如需高可用性和嚴重損壞修復的更多選項，請參閱[商務持續性](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
