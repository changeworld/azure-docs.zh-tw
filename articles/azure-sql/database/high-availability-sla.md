---
title: 高可用性
titleSuffix: Azure SQL Database and SQL Managed Instance
description: 瞭解 Azure SQL Database 和 SQL 受控執行個體服務高可用性功能和功能
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: sstein, sashan
ms.date: 08/12/2020
ms.openlocfilehash: 93e9ad28b14a51432fd9ccd32d1a155eaff2e190
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427123"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 和 SQL 受控執行個體的高可用性
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

在 Azure SQL Database 和 SQL 受控執行個體中，高可用性架構的目標是保證您的資料庫已啟動並執行至少99.99% 的時間 (如需有關不同層的特定 SLA 的詳細資訊，請參閱 [Azure SQL Database 和 SQL 受控執行個體](https://azure.microsoft.com/support/legal/sla/sql-database/)) 的 sla，而不必擔心維護作業和中斷的影響。 Azure 會自動處理重要的服務工作，例如修補、備份、Windows 和 Azure SQL 升級，以及基礎硬體、軟體或網路失敗等非計畫事件。  當 Azure SQL Database 中的基礎資料庫進行修補或容錯移轉時，如果您在應用程式中 [使用重試邏輯](develop-overview.md#resiliency) ，則停機時間並不明顯。 即使在最重要的情況下，SQL Database 和 SQL 受控執行個體也可以快速復原，以確保您的資料一律可供使用。

高可用性解決方案的設計是為了確保認可的資料永遠不會因為失敗而遺失，而維護作業不會影響您的工作負載，而且資料庫不會是您的軟體架構中的單一失敗點。 在升級或維護資料庫時，不會有維護視窗或停機時間要求您停止工作負載。

有兩個高可用性架構模型：

- 以計算和儲存體分隔為基礎的**標準可用性模型**。  它依賴遠端儲存層的高可用性和可靠性。 此架構的目標是以預算導向的商務應用程式，可容忍維護活動期間的效能降低。
- 以資料庫引擎進程叢集為基礎的高階**可用性模型**。 這是因為永遠會有一個可用資料庫引擎節點的仲裁。 此架構以高 IO 效能、高交易率為目標的任務關鍵性應用程式，並保證在維護活動期間對您的工作負載造成最少的效能影響。

SQL Database 和 SQL 受控執行個體都是在最新穩定版本的 SQL Server Database engine 和 Windows 作業系統上執行，大部分的使用者都不會注意到升級會持續執行。

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>基本、標準和一般用途服務層級本機冗余可用性

基本、標準和一般用途服務層級會利用適用于無伺服器和已布建計算的標準可用性架構。 下圖顯示具有個別計算和儲存層的四個不同節點。

![分隔計算與儲存體](./media/high-availability-sla/general-purpose-service-tier.png)

標準可用性模型包含兩個層級：

- 一種無狀態計算層，可執行 `sqlservr.exe` 處理常式，且僅包含暫時性和快取的資料，例如 TempDB、附加 SSD 上的模型資料庫，以及記憶體中的計畫快取、緩衝集區和資料行存放區集區。 此無狀態節點是由可初始化 `sqlservr.exe` 、控制節點健康情況的 Azure Service Fabric 操作，並在必要時執行容錯移轉至另一個節點。
- 具狀態資料層，其中包含儲存在 Azure Blob 儲存體中的資料庫檔案 ( .mdf/.ldf) 。 Azure blob 儲存體具有內建的資料可用性和冗余功能。 它保證即使處理常式當機，也會保留資料檔案中記錄檔或頁面中的每筆記錄 `sqlservr.exe` 。

每次升級資料庫引擎或作業系統，或偵測到失敗時，Azure Service Fabric 都會將無狀態 `sqlservr.exe` 進程移至具有足夠可用容量的另一個無狀態計算節點。 Azure Blob 儲存體中的資料不會受到移動影響，且資料/記錄檔會附加到新初始化的 `sqlservr.exe` 進程。 此程式可保證99.99% 的可用性，但繁重的工作負載可能會在轉換期間發生效能降低的情況，因為新的進程是以 `sqlservr.exe` 冷快取啟動。

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>一般用途服務層區域冗余可用性 (預覽) 

一般用途服務層的區域冗余設定會利用 [Azure 可用性區域](../../availability-zones/az-overview.md)   來跨 Azure 區域內的多個實體位置複寫資料庫。藉由選取 [區域冗余]，您可以讓新的和現有的一般用途單一資料庫和彈性集區復原到較大的一組失敗，包括重大的資料中心中斷，而不需要變更應用程式邏輯。

一般用途層的區域冗余設定有兩個層級：  

- 具有資料庫檔案的具狀態資料層 ( .mdf/.ldf) 儲存在 ZRS PFS (區域冗余 [儲存體 premium 檔案共用](../../storage/files/storage-how-to-create-premium-fileshare.md)。 使用 [區域多餘的儲存體](../../storage/common/storage-redundancy.md) 時，資料和記錄檔會在三個實際隔離的 Azure 可用性區域間同步複製。
- 執行 sqlservr.exe 進程的無狀態計算層，其中只包含暫時性和快取的資料，例如 TempDB、附加 SSD 上的模型資料庫，以及記憶體中的計畫快取、緩衝集區和資料行存放區集區。 此無狀態節點是由 Azure Service Fabric 所操作，可初始化 sqlservr.exe、控制節點的健康情況，並在必要時執行容錯移轉至另一個節點。 針對區域冗余的一般用途資料庫，具有備用容量的節點可立即在其他可用性區域進行容錯移轉。

下圖說明一般用途服務層級的高可用性架構的區域冗余版本：

![適用于一般用途的區域冗余設定](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> 如需支援區域重複資料庫之區域的最新資訊，請參閱 [依區域的服務支援](../../availability-zones/az-region.md)。 只有在選取第5代計算硬體時，才可使用區域冗余設定。 SQL 受控執行個體中無法使用此功能。

> [!NOTE]
> 大小為 80 vcore 的一般用途資料庫，可能會在區域冗余設定時遇到效能降低的情況。 備份、還原、資料庫複製及設定異地 DR 關聯性之類的作業，可能會針對大於 1 TB 的單一資料庫體驗較慢的效能。 

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Premium 和業務關鍵服務層級本機冗余可用性

Premium 和業務關鍵服務層級會利用高階可用性模型，將計算資源整合 (`sqlservr.exe` 進程) 和儲存體 (本機連接的 SSD) 在單一節點上。 藉由將計算和儲存體複寫至建立三到四個節點叢集的其他節點，即可達到高可用性。

![資料庫引擎節點的叢集](./media/high-availability-sla/business-critical-service-tier.png)

基礎資料庫檔案 ( .mdf/.ldf) 放置在附加的 SSD 儲存體上，以提供非常低的延遲 IO 給您的工作負載。 高可用性是使用類似 SQL Server [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技術來實行。 叢集包含可供讀寫客戶工作負載存取的單一主要複本，以及最多三個次要複本 (計算和儲存體) 包含資料複本。 主要節點會依序不斷地將變更推送至次要節點，並確保在認可每個交易之前，資料會同步處理至至少一個次要複本。 此程式可保證如果主要節點因為任何原因而損毀，一律會有完全同步處理的節點以進行容錯移轉。 Azure Service Fabric 會起始容錯移轉。 次要複本變成新的主要節點之後，就會建立另一個次要複本，以確保叢集有足夠的節點 (仲裁集) 。 完成容錯移轉之後，Azure SQL 連接會自動重新導向至新的主要節點。

額外的好處是，premium 可用性模型包含將唯讀 Azure SQL 連接重新導向至其中一個次要複本的能力。 這項功能稱為「 [讀取相應](read-scale-out.md)放大」。它提供100% 的額外計算容量，不需額外費用，就能從主要複本關閉並載入唯讀作業，例如分析工作負載。

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Premium 和業務關鍵服務層級區域冗余可用性 

依預設，會在相同的資料中心內建立高階可用性模型的節點叢集。 隨著 [Azure 可用性區域](../../availability-zones/az-overview.md)的推出，SQL Database 可以將業務關鍵資料庫的不同複本放置到相同區域中的不同可用性區域。 為了避免發生單點失敗，系統也會跨多個區域將控制環複寫成三個閘道環 (GW)。 [Azure 流量管理員](../../traffic-manager/traffic-manager-overview.md) (ATM) 會控制特定閘道的路由。 因為 Premium 或業務關鍵服務層中的區域多餘設定不會建立額外的資料庫冗余，所以您可以不需要額外的成本來啟用它。 藉由選取區域冗余設定，您可以讓高階或業務關鍵資料庫復原到較大的一組失敗，包括重大的資料中心中斷，而不會變更應用程式邏輯。 您也可以將任何現有的進階或業務關鍵資料庫或彈性集區轉換成區域備援組態。

因為區域冗余資料庫在不同的資料中心內有複本之間有一些距離，所以增加的網路延遲可能會增加認可時間，因而影響某些 OLTP 工作負載的效能。 您一律可以停用區域備援設定來回到單一區域設定。 此程式是與一般服務層級升級類似的線上操作。 在此程序結束時，資料庫或集區會從區域備援環移轉成單一區域環，或反之亦然。

> [!IMPORTANT]
> 區域冗余資料庫和彈性集區目前僅在精選區域中的 Premium 和業務關鍵服務層級支援。 使用業務關鍵層時，只有在選取第5代計算硬體時，才可使用區域冗余設定。 如需支援區域重複資料庫之區域的最新資訊，請參閱 [依區域的服務支援](../../availability-zones/az-region.md)。

> [!NOTE]
> SQL 受控執行個體中無法使用此功能。

下圖說明區域備援版的高可用性架構：

![高可用性架構區域備援](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>超大規模服務層級可用性

超大規模服務層架構在分散式函式 [架構](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture) 中有描述，而且目前僅適用于 SQL Database，而非 SQL 受控執行個體。

![超大規模功能架構](./media/high-availability-sla/hyperscale-architecture.png)

超大規模中的可用性模型包含四個層級：

- 執行進程的無狀態計算層， `sqlservr.exe` 只包含暫時性和快取的資料，例如在連接的 SSD 上的非涵蓋 RBPEX 快取、TempDB、模型資料庫等等，以及記憶體中的計畫快取、緩衝集區和資料行存放區集區。 此無狀態層包含主要計算複本，並選擇性地包含一些可作為容錯移轉目標的次要計算複本。
- 頁面伺服器所形成的無狀態儲存層。 這一層是在計算複本上執行之進程的分散式儲存引擎 `sqlservr.exe` 。 每一頁伺服器僅包含暫時性和快取的資料，例如涵蓋連接 SSD 上的 RBPEX 快取，以及在記憶體中快取的資料頁。 每部頁面伺服器在主動-主動設定中都有成對的頁面伺服器，以提供負載平衡、冗余和高可用性。
- 由執行記錄服務處理常式、交易記錄登陸區域，以及交易記錄長期儲存的計算節點所形成的具狀態交易記錄儲存層。 登陸區域和長期儲存使用 Azure 儲存體，可為交易記錄提供可用性和 [冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy) ，以確保認可交易的資料耐久性。
- 具有資料庫檔案的具狀態資料儲存層 ( .mdf/ndf) 儲存在 Azure 儲存體中，並由頁面伺服器更新。 這一層會使用 Azure 儲存體的資料可用性和 [冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 功能。 它保證資料檔中的每個頁面都會保留，即使超大規模架構的其他層級中的進程損毀，或計算節點失敗也一樣。

所有超大規模層中的計算節點會在 Azure Service Fabric 上執行，以控制每個節點的健康情況，並視需要執行容錯移轉至可用狀況良好的節點。

如需超大規模中高可用性的詳細資訊，請參閱 [超大規模中的資料庫高可用性](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale)。


## <a name="accelerated-database-recovery-adr"></a>加速資料庫復原 (ADR)

[加速資料庫復原 (ADR) ](../accelerated-database-recovery.md) 是一項新的資料庫引擎功能，可大幅提升資料庫可用性，尤其是在有長時間執行的交易時。 ADR 目前適用于 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics (先前的 SQL 資料倉儲) 。

## <a name="testing-application-fault-resiliency"></a>測試應用程式錯誤復原

高可用性是 SQL Database 和 SQL 受控執行個體平台的基礎部分，可為您的資料庫應用程式提供透明的運作。 不過，我們認為您可能想要測試在計劃性或非計劃性事件期間起始的自動容錯移轉作業對應用程式有何影響，然後再將其部署到生產環境。 您可以手動觸發容錯移轉，方法是呼叫特殊 API 來重新開機資料庫、彈性集區或受控實例。 在區域冗余資料庫或彈性集區的情況下，API 呼叫會導致將用戶端連線重新導向至可用性區域中的新主資料庫，而不同于舊的主要複本的可用性區域。 因此，除了測試容錯移轉如何影響現有的資料庫會話，您也可以確認它是否因為網路延遲的變更而變更了端對端效能。 由於重新開機作業是侵入式的，且有大量的作業可能會讓平臺壓力，因此每個資料庫、彈性集區或受控實例只能每隔30分鐘執行一次容錯移轉呼叫。

您可以使用 PowerShell、REST API 或 Azure CLI 來起始容錯移轉：

|部署類型|PowerShell|REST API| Azure CLI|
|:---|:---|:---|:---|
|資料庫|[Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover)|[資料庫容錯移轉](/rest/api/sql/databases(failover)/failover/)|[az rest](https://docs.microsoft.com/cli/azure/reference-index#az-rest) 可用來從 Azure CLI 叫用 REST API 呼叫|
|彈性集區|[Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[彈性集區容錯移轉](/rest/api/sql/elasticpools(failover)/failover/)|[az rest](https://docs.microsoft.com/cli/azure/reference-index#az-rest) 可用來從 Azure CLI 叫用 REST API 呼叫|
|受控執行個體|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[受控實例-容錯移轉](https://docs.microsoft.com/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi 容錯移轉](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> 超大規模資料庫的可讀取次要複本無法使用容錯移轉命令。

## <a name="conclusion"></a>結論

Azure SQL Database 和 Azure SQL 受控執行個體提供與 Azure 平臺緊密整合的內建高可用性解決方案。 這取決於故障偵測和復原的 Service Fabric、用於資料保護的 Azure Blob 儲存體，以及可用性區域以取得更高的容錯 (（如稍早在不適用於 Azure SQL) 受控執行個體的檔中所述）。 此外，SQL Database 和 SQL 受控執行個體利用 SQL Server 實例的 Always On 可用性群組技術進行複寫和容錯移轉。 這些技術的組合可讓應用程式完全實現混合儲存模型的優點，並支援最嚴苛的 Sla。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 可用性區域](../../availability-zones/az-overview.md)
- 瞭解 [Service Fabric](../../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理員](../../traffic-manager/traffic-manager-overview.md)
- 瞭解 [如何在 SQL 受控執行個體上起始手動容錯移轉](../managed-instance/user-initiated-failover.md)
- 如需其他高可用性和災害復原的選項，請參閱[商務持續性](business-continuity-high-availability-disaster-recover-hadr-overview.md)
