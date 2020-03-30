---
title: 高可用性
description: 了解 Azure SQL Database 服務的高可用性功能
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: b560cee23855d1c0e8a7b3c2cb9d82c184a1ebf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269649"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL Database

Azure SQL 資料庫中的高可用性體系結構的目標是保證資料庫啟動並運行至少 99.99% 的時間（有關不同層的特定 SLA 的詳細資訊，請參閱[Azure SQL 資料庫的 SLA），](https://azure.microsoft.com/support/legal/sla/sql-database/)而不必擔心維護操作和中斷的影響。 Azure 會自動處理關鍵服務任務，如修補、備份、Windows 和 SQL 升級，以及計畫外事件（如基礎硬體、軟體或網路故障）。  修補基礎 SQL 實例或失敗時，如果在應用中[使用重試邏輯](sql-database-develop-overview.md#resiliency)，停機時間不會明顯。 即使在最關鍵的情況下，Azure SQL Database 也可以快速復原，確保您的資料隨時可用。

高可用性解決方案旨在確保提交的資料永遠不會因故障而丟失，維護操作不會影響您的工作負載，並且資料庫不會成為軟體體系結構中的單個故障點。 在升級或維護資料庫時，不會有維護視窗或停機時間要求您停止工作負載。 

Azure SQL 資料庫中使用兩種高可用性體系結構模型：

- 基於計算和存儲分離的標準可用性模型。  它依賴于遠端存放層的高可用性和可靠性。 此體系結構面向面向預算的商務應用程式，這些應用程式可以容忍維護活動期間的某些性能下降。
- 基於資料庫引擎進程群集的高級可用性模型。 它依賴于一個事實，即始終存在可用的資料庫引擎節點的仲裁。 此體系結構面向具有高 IO 性能、高事務速率的任務關鍵型應用程式，並保證在維護活動期間對工作負載的性能影響最小。

Azure SQL 資料庫在最新的穩定版本的 SQL Server 資料庫引擎和 Windows 作業系統上運行，大多數使用者不會注意到升級是連續執行的。

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>基本、標準和一般目的服務層級可用性

這些服務層利用標準可用性體系結構。 下圖顯示了四個不同的節點，這些節點具有分離的計算層和存儲層。

![分隔計算與儲存體](media/sql-database-high-availability/general-purpose-service-tier.png)

標準可用性模型包括兩層：

- 運行`sqlservr.exe`進程的無狀態計算層，僅包含瞬態和緩存資料，如 TempDB、附加 SSD 上的模型資料庫以及規劃記憶體中的緩存、緩衝集區和列存儲池。 此無狀態節點由 Azure Service Fabric 操作，`sqlservr.exe`該結構初始化、控制節點的運行狀況，並在必要時執行容錯移轉到另一個節點。
- 存儲在 Azure Blob 存儲中的資料庫檔案 （.mdf/.ldf） 的有狀態資料層。 Azure Blob 存儲具有內置的資料可用性和冗余功能。 它保證即使 SQL Server 進程崩潰，資料檔案中的日誌檔或頁面中的每個記錄都會保留。

每當升級資料庫引擎或作業系統或檢測到故障時，Azure Service Fabric 都會將無狀態 SQL Server 進程移動到具有足夠可用容量的另一個無狀態計算節點。 Azure Blob 存儲中的資料不受移動的影響，並且資料/日誌檔將附加到新初始化的 SQL Server 進程。 此過程保證 99.99% 的可用性，但由於新的 SQL Server 實例從冷緩存開始，繁重的工作負載在轉換期間可能會遇到一些性能下降。

## <a name="premium-and-business-critical-service-tier-availability"></a>進階與商務關鍵性服務層級可用性

高級和業務關鍵型服務層利用高級可用性模型，該模型將計算資源（SQL Server 資料庫引擎進程）和存儲（本地附加的 SSD）集成在單個節點上。 通過將計算和存儲複製到創建三到四節點群集的其他節點來實現高可用性。 

![資料庫引擎節點的叢集](media/sql-database-high-availability/business-critical-service-tier.png)

基礎資料庫檔案 （.mdf/.ldf） 放置在附加的 SSD 存儲上，以便為您的工作負載提供非常低的延遲 IO。 高可用性使用類似于 SQL Server[始終在可用性組的技術](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)實現。 群集包括可供讀寫客戶工作負載訪問的單個主副本（SQL Server 進程），以及最多三個包含資料副本的輔助副本（計算和存儲）。 主節點會按順序將更改推送到輔助節點，並確保在提交每個事務之前將資料同步到至少一個輔助副本。 此過程保證，如果主節點由於任何原因崩潰，則始終有一個完全同步的節點要容錯移轉到。 容錯移轉由 Azure 服務結構啟動。 輔助副本成為新的主節點後，將創建另一個輔助副本，以確保群集具有足夠的節點（仲裁集）。 容錯移轉完成後，SQL 連接將自動重定向到新的主節點。

作為額外的好處，高級可用性模型包括能夠將唯讀 SQL 連接重定向到輔助副本之一。 此功能稱為[讀取橫向擴展](sql-database-read-scale-out.md)。它為從主副本卸載唯讀操作（如分析工作負載）提供 100% 的額外計算容量，無需額外付費。

## <a name="hyperscale-service-tier-availability"></a>超大規模服務層可用性

超大規模服務層體系結構在[分散式函數體系結構](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture)中描述。 

![超大規模功能架構](./media/sql-database-hyperscale/hyperscale-architecture.png)

超大規模中的可用性模型包括四個層：

- 運行`sqlservr.exe`進程的無狀態計算層，僅包含瞬態和緩存資料，例如附加 SSD 上的非覆蓋 RBPEX 緩存、TempDB、模型資料庫等，並在記憶體中規劃緩存、緩衝集區和列存儲池。 此無狀態層包括主計算副本，以及可選的一些輔助計算副本，這些副本可用作容錯移轉目標。
- 由頁面伺服器組成的無狀態存儲層。 此層是計算副本上運行`sqlservr.exe`的進程的分散式儲存引擎。 每個頁面伺服器僅包含瞬態和緩存資料，例如覆蓋附加 SSD 上的 RBPEX 緩存，以及緩存在記憶體中的資料頁。 每個頁面伺服器都有一個活動-主動配置的配對頁面伺服器，可提供負載平衡、冗余和高可用性。
- 運行日誌服務流程的計算節點、事務日誌登錄區域和事務日誌長期存儲形成的有狀態事務日誌存儲層。 著陸區和長期存儲使用 Azure 存儲，它為事務日誌提供可用性和[冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)，確保提交事務的資料持久性。
- 包含存儲在 Azure 存儲中的資料庫檔案 （.mdf/.ndf） 並由頁面伺服器更新的有狀態資料存儲層。 此層使用 Azure 存儲的資料可用性和[冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)功能。 它保證資料檔案中的每一頁都會保留，即使其他超大規模體系結構層中的進程崩潰，或者計算節點發生故障。

所有超大規模層中的計算節點都在 Azure Service Fabric 上運行，該結構控制每個節點的運行狀況，並在必要時對可用的正常節點執行容錯移轉。

有關超大規模中高可用性的詳細資訊，請參閱[超大規模中的資料庫高可用性](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale)。

## <a name="zone-redundant-configuration"></a>區域備援設定

預設情況下，高級可用性模型的節點群集在同一資料中心中創建。 引入 Azure[可用性區域](../availability-zones/az-overview.md)後，SQL 資料庫可以將業務關鍵型資料庫的不同副本放置到同一區域中的不同可用性區域。 為了避免發生單點失敗，系統也會跨多個區域將控制環複寫成三個閘道環 (GW)。 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) (ATM) 會控制特定閘道的路由。 由於高級或業務關鍵型服務層中的區域冗余配置不會創建額外的資料庫冗余，因此可以不花費額外費用啟用它。 通過選擇區域冗余配置，可以使高級資料庫或業務關鍵型資料庫能夠抵禦更大的故障集，包括災難性的資料中心中斷，而無需對應用程式邏輯進行任何更改。 您也可以將任何現有的進階或業務關鍵資料庫或彈性集區轉換成區域備援組態。

由於區域冗余資料庫在不同的資料中心中具有副本，它們之間具有一定距離，因此增加的網路延遲可能會增加提交時間，從而影響某些 OLTP 工作負載的性能。 您一律可以停用區域備援設定來回到單一區域設定。 此過程是類似于常規服務層升級的線上操作。 在此程序結束時，資料庫或集區會從區域備援環移轉成單一區域環，或反之亦然。

> [!IMPORTANT]
> 區域冗余資料庫和彈性池目前僅在選定區域的高級和業務關鍵型服務層中支援。 使用"業務關鍵型"層時，區域冗余配置僅在選擇 Gen5 計算硬體時可用。 有關支援區域冗余資料庫的區域的最新資訊，請參閱[按區域劃分的服務支援](../availability-zones/az-overview.md#services-support-by-region)。  
> 此功能在託管實例中不可用。

下圖說明區域備援版的高可用性架構：

![高可用性架構區域備援](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>加速資料庫復原 (ADR)

[加速資料庫恢復 （ADR）](sql-database-accelerated-database-recovery.md)是一種新的 SQL 資料庫引擎功能，可大大提高資料庫可用性，尤其是在存在長時間運行的事務的情況下。 ADR 目前適用於單一資料庫、彈性集區和 Azure SQL 資料倉儲。

## <a name="testing-application-fault-resiliency"></a>測試應用程式故障恢復能力

高可用性是 Azure SQL 資料庫平臺的基本部分，該平臺可透明地適用于資料庫應用程式。 但是，我們認識到，在將應用程式部署到生產階段之前，您可能需要測試在計畫或計畫外事件期間啟動的自動容錯移轉操作對應用程式的影響。 您可以調用特殊 API 來重新開機資料庫或彈性池，這反過來將引發容錯移轉。 在區域冗余資料庫或彈性池中，API 呼叫將導致將用戶端連接重定向到可用性區域中的新主資料庫，而可用性區域不同于舊主資料庫的可用性區域。 因此，除了測試容錯移轉如何影響現有資料庫會話外，還可以驗證容錯移轉是否由於網路延遲的變化而更改端到端性能。 由於重新開機操作具有侵入性，並且大量操作可能會給平臺帶來壓力，因此每個資料庫或彈性池每 30 分鐘只允許進行一次容錯移轉調用。 

可以使用 REST API 或 PowerShell 啟動容錯移轉。 有關 REST API，請參閱[資料庫容錯移轉](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover)和[彈性池容錯移轉](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover)。 有關 PowerShell，請參閱[調用-AzSql資料庫容錯移轉](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover)和[調用-AzSql彈性池容錯移轉](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)。 REST API 呼叫也可以使用[az rest](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)命令從 Azure CLI 進行。

> [!IMPORTANT]
> 容錯移轉命令當前在超大規模服務層和託管實例中不可用。

## <a name="conclusion"></a>結論

Azure SQL 資料庫具有內置的高可用性解決方案，該解決方案與 Azure 平臺深度集成。 它依賴于服務結構進行故障檢測和恢復，依賴于 Azure Blob 存儲進行資料保護，並且取決於可用性區域，以便提高容錯能力。 此外，Azure SQL 資料庫利用 SQL Server 的"始終打開可用性組"技術進行複製和容錯移轉。 這些技術的結合使應用程式能夠充分實現混合存儲模型的優勢，並支援要求最高的 SL。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 可用性區域](../availability-zones/az-overview.md)
- 瞭解[服務結構](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)
- 如需其他高可用性和災害復原的選項，請參閱[商務持續性](sql-database-business-continuity.md)
