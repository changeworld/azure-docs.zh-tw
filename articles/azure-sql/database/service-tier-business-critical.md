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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85986685"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>業務關鍵層-Azure SQL Database 和 Azure SQL 受控執行個體 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 在 DTU 購買模型中，業務關鍵層稱為 Premium。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](purchasing-models.md)。

Azure SQL Database 和 Azure SQL 受控執行個體都是以針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保即使在基礎結構失敗的情況下，仍可確保99.99% 的可用性。 使用的架構模型有三種：
- 一般目的/標準 
- 業務關鍵/進階
- 超大規模資料庫

進階/業務關鍵服務層級模型會以資料庫引擎程序的叢集作為基礎。 此架構模型依賴事實上一律有法定的可用資料庫引擎節點數，而且對您工作負載的效能影響最小 (即使在維護活動期間亦然)。 超大規模服務層級目前僅適用于 Azure SQL Database (不是 SQL 受控執行個體) ，而且是可高度擴充的儲存體和計算效能層級，它會利用 Azure 架構來向外延展 Azure SQL Database 資料庫的儲存體和計算資源，而不超過一般用途和業務關鍵服務層級的限制。

Azure 會以透明的方式，為使用者提供最短的停機時間，以明確地升級和修補基礎作業系統、驅動程式和 SQL Server 資料庫引擎。 

高階可用性是在 Premium 和業務關鍵服務層級中啟用，而且是針對因為進行中的維護作業而無法容忍任何效能影響的密集型工作負載所設計。

計算和儲存體會在 premium 模型的單一節點上整合。 此架構模型中的高可用性是藉由將計算 (SQL Server database engine 進程) 和儲存體 (本機連接的 SSD) 部署到四個節點叢集（使用類似 SQL Server [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技術）來達成。

![資料庫引擎節點的叢集](./media/service-tier-business-critical/business-critical-service-tier.png)

SQL Server 資料庫引擎進程和基礎 .mdf/.ldf 檔案都會放在具有本機連接 SSD 儲存體的相同節點上，為您的工作負載提供低延遲。 高可用性是使用類似 SQL Server [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技術來實行。 每個資料庫都是資料庫節點的叢集，其中包含一個可供客戶工作負載存取的主資料庫，以及三個包含資料複本的次要進程。 主要節點會不斷地將變更推送至次要節點，以確保當主要節點因任何原因而失敗時，次要複本上的資料可供使用。 容錯移轉是由 SQL Server 資料庫引擎處理-一個次要複本會變成主要節點，並建立新的次要複本，以確保叢集中有足夠的節點。 工作負載會自動重新導向至新的主要節點。

此外，商務關鍵性叢集具有內建的[讀取縮放](read-scale-out.md)功能，會提供免費的內建唯讀節點，該節點可用於執行不會影響主要工作負載效能的唯讀狀態 (例如報告)。

## <a name="when-to-choose-this-service-tier"></a>選擇此服務層級的時機

商務關鍵服務層級是針對需要來自基礎 SSD 儲存體之低延遲回應的應用程式所設計， (1-2 毫秒的平均) 、快速復原（如果基礎結構失敗），或需要將報表、分析和唯讀查詢從主資料庫的免費可讀取次要複本移除。

選擇商務關鍵服務層級而非一般用途層的主要原因如下：
-   **低 i/o 延遲需求** –需要儲存層快速回應的工作負載 (1-2 毫秒的平均) 應使用業務關鍵層。 
-   **頻繁地在應用程式與資料庫之間進行通訊**。 無法利用應用層快取或 [要求批次處理](../performance-improve-use-batching.md) ，以及必須傳送許多必須快速處理的 SQL 查詢的應用程式，都是適用于業務關鍵層的絕佳候選項目。
-   **大量更新** ：插入、更新和刪除作業會修改記憶體中的資料頁 (中途分頁) 必須使用作業儲存到資料檔案中 `CHECKPOINT` 。 可能的資料庫引擎進程損毀或具有大量中途頁面的資料庫容錯移轉，可能會增加一般用途層的復原時間。 如果您的工作負載會導致許多記憶體中的變更，請使用業務關鍵層。 
-   **修改資料的長時間執行交易**。 開啟較長時間的交易會導致記錄檔截斷，這可能會增加記錄檔大小和虛擬記錄檔的數目 [ (VLF) ](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)。 在容錯移轉之後，大量的 Vlf 可能會降低資料庫復原的速度。
-   **具有報告和分析查詢的工作負載** ，可重新導向至免費的次要唯讀複本。
- **更高的復原能力和更快速的失敗復原**。 如果發生系統失敗，主要實例上的資料庫將會停用，而其中一個次要複本會立即變成新的讀寫主資料庫，以準備好處理查詢。 資料庫引擎不需要分析和重做記錄檔中的交易，並載入記憶體緩衝區中的所有資料。
- **先進的資料損毀保護**。 商務關鍵性層會在幕後針對商務持續性用途使用資料庫複本，因此服務也會利用自動頁面修復，這是用於 SQL Server 資料庫 [鏡像和可用性群組](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)的相同技術。 當複本因為資料完整性問題而無法讀取頁面時，將會從另一個複本抓取頁面的全新複本，並在不遺失資料或客戶停機的情況下，取代無法讀取的頁面。 如果資料庫具有異地次要複本，這項功能適用于一般用途層。
- **高可用性** -多 AZ 設定的商務關鍵層可保證99.995% 的可用性，相較于99.99% 的一般用途層。
- **快速異地** 復原-以異地複寫設定的商務關鍵層具有保證的復原點目標 (RPO) 為5秒，而復原時間 (目標為100% 的部署時間，RTO) 30 秒。

## <a name="next-steps"></a>接下來的步驟

- 在 [SQL 受控執行個體](../managed-instance/resource-limits.md#service-tier-characteristics)、 [VCore 模型](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) 或 [dtu 模型](resource-limits-dtu-single-databases.md#premium-service-tier)中的單一資料庫，或 [VCore 模型](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) 和 [dtu 模型](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits)中的彈性集區中，尋找資源特性 (核心數目、i/o、商務關鍵層的記憶體) 。
- 了解[一般目的](service-tier-general-purpose.md)和[超大規模資料庫](service-tier-hyperscale.md)層。
- 瞭解 [Service Fabric](../../service-fabric/service-fabric-overview.md)。
- 如需高可用性和嚴重損壞修復的更多選項，請參閱 [商務持續性](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
