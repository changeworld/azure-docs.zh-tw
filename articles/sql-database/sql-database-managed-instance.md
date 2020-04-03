---
title: SQL 託管實例概述
description: 本文介紹 Azure SQL 資料庫託管實例。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: 06242af6cb00e3adebbc80da722898fb8e348e36
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585362"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>什麼是 Azure SQL 資料庫託管實例?

受控執行個體是 Azure SQL Database 的新部署選項，幾乎可與最新 SQL Server 內部部署環境 (Enterprise Edition) 資料庫引擎 100% 相容，並提供原生[虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 實作，可解決常見的安全性考量，以及提供有利於內部部署 SQL Server 客戶的[商務模型](https://azure.microsoft.com/pricing/details/sql-database/)。 受控執行個體部署模型可讓現有 SQL Server 客戶透過最少的應用程式和資料庫變更，將他們的內部部署應用程式隨即轉移至雲端。 同時，受控執行個體部署模型選項會保留 PaaS 的所有功能 (自動修補和版本的更新、[自訂備份](sql-database-automated-backups.md)、[高可用性](sql-database-high-availability.md))，可以大幅降低管理負擔和 TCO。

> [!IMPORTANT]
> 如需目前可用受控執行個體部署模型的區域清單，請參閱[支援的區域](sql-database-managed-instance-resource-limits.md#supported-regions)。

下圖概述受控執行個體的主要功能：

![主要功能](./media/sql-database-managed-instance/key-features.png)

受控執行個體部署模型專為以下客戶設計：想要盡可能輕鬆地將大量應用程式，從內部部署或 IaaS、自行建置或 ISV 提供的環境遷移至完全受控的 PaaS 雲端環境。 使用 Azure 中的完全自動化[資料遷移服務 (DMS),](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)客戶可以提升其本地 SQL Server 並將其轉移到託管實例,該實例提供與本地 SQL Server 的相容性,並且使用本機 VNet 支援完全隔離客戶實例。  使用軟體保證,您可以使用 SQL Server 的[Azure 混合權益](https://azure.microsoft.com/pricing/hybrid-benefit/)在託管實例上交換現有許可證以享受折扣費率。  對於需要高度安全性和程式設計介面豐富的 SQL Server 執行個體而言，受控執行個體是雲端中最佳的移轉目的地。

受控執行個體部署選項的目標是透過階段式發行計劃，為最新版內部部署 SQL Server 提供幾乎 100% 的介面區相容性。

若要決定使用 Azure SQL Database 部署選項：單一資料庫、集區資料庫、受控執行個體或虛擬機器中裝載的 SQL Server，請參閱[如何在 Azure 選擇正確的 SQL Server 版本](sql-database-paas-vs-sql-server-iaas.md)。

## <a name="key-features-and-capabilities"></a>重要功能

受控執行個體結合了可在 Azure SQL Database 和 SQL Server 資料庫引擎中取得的最佳功能。

> [!IMPORTANT]
> 受控執行個體能執行 SQL Server 最新版本的所有功能，包括線上作業、自動計劃修正，以及其他企業效能增強功能。 [功能比較：Azure SQL Database 與 SQL Server](sql-database-features.md)中提供可用功能比較的說明。

| **PaaS 支援** | **業務持續性** |
| --- | --- |
|無須硬體採購和管理 <br>沒有管理基礎結構的管理負擔 <br>快速佈建和服務調整 <br>自動修補和版本升級 <br>與其他 PaaS 資料服務整合 |99.99% 的 SLA 運作時間  <br>內建[高可用性](sql-database-high-availability.md) <br>使用[自動備份](sql-database-automated-backups.md)保護資料 <br>客戶可設定的備份保留期限 <br>使用者起始的[備份](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[資料庫還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)功能 |
|**安全性與合規性** | **管理**|
|隔離的環境 ([VNet 整合](sql-database-managed-instance-connectivity-architecture.md)、單一租用戶服務、專用的運算和儲存體) <br>[透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD 驗證](sql-database-aad-authentication.md)、單一登入支援 <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD 伺服器主體(登錄名)</a>  <br>與 Azure SQL 資料庫遵循相同的合規性標準 <br>[SQL 稽核](sql-database-managed-instance-auditing.md) <br>[進階威脅防護](sql-database-managed-instance-threat-detection.md) |用於自動化服務佈建與調整的 Azure Resource Manager API <br>用於手動服務佈建與調整的 Azure 入口網站功能 <br>資料移轉服務

> [!IMPORTANT]
> Azure SQL 資料庫(所有部署選項)已根據許多合規性標準進行了認證。 有關詳細資訊,請參閱 Microsoft [Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942),您可以在其中找到最新的 SQL 資料庫合規性認證清單。

下表顯示受控執行個體的主要功能：

|功能 | 描述|
|---|---|
| SQL Server 版本/組建 | SQL Server 資料庫引擎 (最新穩定版) |
| 受控自動化備份 | 是 |
| 內建執行個體和資料庫的監視與計量 | 是 |
| 自動軟體修補 | 是 |
| 最新的資料庫引擎功能 | 是 |
| 每個資料庫的資料檔案 (ROWS) 數目 | 多個 |
| 每個資料庫的記錄檔 (LOG) 數目 | 1 |
| VNet - Azure Resource Manager 部署 | 是 |
| VNet - 傳統部署模型 | 否 |
| 入口網站支援 | 是|
| 內建的整合服務 (SSIS) | 否 - SSIS 屬於 [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| 內建的 Analysis Services (SSAS) | 否 - SSAS 是個別 [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| 內建的報告服務 (SSRS) | 否 - 改用[Power BI 分頁報表](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi),或在 Azure VM 上託管 SSRS。 雖然託管實例不能將 SSRS 作為服務運行,但它可以使用 SQL Server 身份驗證為外部報告伺服器託管 SSRS 2019 目錄資料庫。 |
|||

## <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

受控執行個體中[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)提供彈性、可控制、透明及直接的方法，讓您將內部部署工作負載需求平移到雲端。 此模型可讓您根據工作負載需求，變更計算、記憶體和儲存體。 vCore 模型還有資格使用 SQL Server 的 Azure[混合權益](https://azure.microsoft.com/pricing/hybrid-benefit/)節省多達 55%。

在 V 核心模型中，您可以選擇各硬體世代。

- **第4代**邏輯 CPU 基於英特爾 E5-2673 v3 (Haswell) 2.4-GHz 處理器、附加的 SSD、物理內核、每個內核 7 GB RAM 以及 8 到 24 個 vCore 之間的計算大小。
- **第5代**邏輯 CPU 基於英特爾 E5-2673 v4 (Broadwell) 2.3-GHz 和英特爾 SP-8160 (Skylake) 處理器、快速 NVMe SSD、超線程邏輯內核以及 4 到 80 個內核之間的計算大小。

查找有關[託管實例資源限制](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)中硬體生成之間的差異的詳細資訊。

## <a name="managed-instance-service-tiers"></a>受控執行個體服務層級

有兩個服務層級可使用受控執行個體：

- **通用:** 專為具有典型性能和 IO 延遲要求的應用程式而設計。
- **業務關鍵型**:專為 IO 延遲要求低且基礎維護操作對工作負載影響最小的應用程式而設計。

這兩個服務層級均保證 99.99% 的可用性，可讓您單獨選取儲存體大小和計算容量。 如需 Azure SQL Database 高可用性架構的詳細資訊，請參閱[高可用性和 Azure SQL Database](sql-database-high-availability.md)。

### <a name="general-purpose-service-tier"></a>一般目的服務層級

下列清單說明一般用途服務層級的主要特色：

- 專為大多數有標準效能需求的商務應用程式所設計
- 高效能的 Azure Blob 儲存體 (8 TB)
- 根據可靠的 Azure Blob 儲存體和 [Azure Service Fabric](../service-fabric/service-fabric-overview.md) 內建的[高可用性](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)

如需詳細資訊，請參閱[一般用途層中的儲存體層](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c)和[受控執行個體 (一般用途) 的儲存體效能最佳做法和考量](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/) \(英文\)。

查找有關[託管實例資源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)中服務層之間的差異的詳細資訊。

### <a name="business-critical-service-tier"></a>業務關鍵服務層級

業務關鍵服務層級是為具有高 IO 需求的應用程式所建置。 使用數個分開的複本，針對失敗提供最高的復原能力。

下列清單概述業務關鍵服務層級的主要特色：

- 專為具有極高效能和 HA 需求的商務應用程式所設計
- 提供超級快速的本機 SSD 儲存體 (在 Gen4 上可達 1 TB，而在 Gen5 上可達 4 TB)
- 基於[永遠開啟的可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)與[Azure 服務結構](../service-fabric/service-fabric-overview.md)的內建[高可用性](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)。
- 內置的附加[唯讀資料庫副本](sql-database-read-scale-out.md),可用於報告和其他唯讀工作負載
- [記憶體內部 OLTP](sql-database-in-memory.md)，可用於具有高效能需求的工作負載  

查找有關[託管實例資源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)中服務層之間的差異的詳細資訊。


## <a name="managed-instance-management-operations"></a>託管實例管理員

Azure SQL Database 提供管理作業，可讓您在不再需要時，用來自動部署新的受控執行個體、更新執行個體屬性和刪除執行個體。 本節提供有關管理操作及其典型持續時間的資訊。

為了支援[Azure 虛擬網路 (VNet) 中的部署](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks),並為客戶提供隔離和安全,託管實例依賴於[虛擬群集](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture),虛擬群集表示部署在客戶虛擬網路子網內的專用隔離虛擬機集。 從本質上講,空子網中的每個託管實例部署都會導致新的虛擬群集構建。

對已部署的託管實例的後續操作也可能對其基礎虛擬群集產生影響。 這會影響管理操作的持續時間,因為部署其他虛擬機附帶了在規劃現有託管實例的新部署或更新時需要考慮的開銷。

所有管理作業都可以分類如下：

- 實例部署(新實例創建)。 
- 實例更新(更改實例屬性,如 vCore 或預留存儲)。
- 實例刪除。

通常,對虛擬群集的操作需要最長的。 虛擬叢集操作的持續時間各不相同 - 以下是您通常可以根據現有服務遙測資料預期的值:

- 虛擬群集創建。 這是實例管理操作中的同步步驟。 **90% 的操作在 4 小時內完成**。
- 虛擬群集調整大小(擴展或收縮)。 擴展是同步步驟,而收縮是非同步執行的(不會影響實例管理操作的持續時間)。 **90% 的叢集延伸在不到 2.5 小時內完成**。
- 虛擬群集刪除。 刪除是異步步驟,但也可以在空虛擬群集上[手動啟動](sql-database-managed-instance-delete-virtual-cluster.md),在這種情況下,它同步執行。 **90% 的虛擬群集刪除在 1.5 小時內完成**。

此外,實例的管理還可以包括託管資料庫上的一個操作,從而導致較長的持續時間:

- 從 Azure 儲存附加資料庫檔。 這是一個同步步驟,如計算 (vCore) 或"通用服務層"中的存儲向上或向下擴展。 **90% 的這些操作在5分鐘內完成**。
- 始終打開可用性組種子設定。 這是一個同步步驟,如業務關鍵型服務層中的計算 (vCore) 或存儲擴展,以及將服務層從通用更改為業務關鍵型(反之亦然)。 此操作的持續時間與資料庫總大小以及當前資料庫活動(活動事務數)成正比。 更新實例時的資料庫活動可能會對總持續時間引入顯著差異。 **90% 的這些操作以 220 GB / 小時或更高小時的速度執行**。

下表總結了操作和典型總體持續時間:

|類別  |作業  |長時間執行區段  |預估的持續時間  |
|---------|---------|---------|---------|
|**部署** |空子網中的第一個實體|虛擬叢集建立|90% 的操作在 4 小時內完成|
|部署 |非空子網中另一個硬體產生的第一個實體(例如,具有第 4 代實例的子網中的第一代 5 代實例)|虛擬叢集建立*|90% 的操作在 4 小時內完成|
|部署 |在空子網中建立 4 個 vCore 的第一個實體|虛擬叢集建立*|90% 的操作在 4 小時內完成|
|部署 |在非空子網(第 2 個、第 3 個等實體)中建立後續實例|虛擬叢集大小調整|90% 的操作在 2.5 小時內完成|
|**更新** |實體屬性變更 (管理員密碼、AAD 登入名稱、Azure 混合權益標誌)|N/A|長達 1 分鐘|
|更新 |實體儲存到/向下延伸(一般服務層)|附加資料庫檔案|90% 的操作在 5 分鐘內完成|
|更新 |實體儲存到/向下延伸(業務關鍵型服務層)|- 虛擬叢集調整大小<br>- 始終開啟可用的群組 feed 設定|90% 的操作在 2.5 小時內完成 + 設定所有資料庫的 torrent 時間 (220 GB / 小時)|
|更新 |實體計算 (vCores) 上下延伸 (通用)|- 虛擬叢集調整大小<br>- 附加資料庫檔案|90% 的操作在 2.5 小時內完成|
|更新 |實體計算 (vCores) 向上與向下延伸(業務關鍵)|- 虛擬叢集調整大小<br>- 始終開啟可用的群組 feed 設定|90% 的操作在 2.5 小時內完成 + 設定所有資料庫的 torrent 時間 (220 GB / 小時)|
|更新 |實體縮減為 4 個 vCore(通用)|- 虛擬群集大小調整(如果首次完成,則可能需要虛擬群集創建*)<br>- 附加資料庫檔案|90% 的操作在 4 小時 5 分鐘內完成*|
|更新 |實體縮減為 4 個 vCore(業務關鍵型)|- 虛擬群集大小調整(如果首次完成,則可能需要虛擬群集創建*)<br>- 始終開啟可用的群組 feed 設定|90% 的操作在 4 小時內完成 – 設定所有資料庫的 torrent 時間 (220 GB / 小時)|
|更新 |實例服務層更改(業務關鍵型的通用用途,反之亦然)|- 虛擬叢集調整大小<br>- 始終開啟可用的群組 feed 設定|90% 的操作在 2.5 小時內完成 + 設定所有資料庫的 torrent 時間 (220 GB / 小時)|
|**刪除**|執行個體刪除|所有資料庫的紀錄尾部備份|90% 的操作在 1 分鐘內完成。<br>注意:如果子網中的最後一個實例被刪除,此操作將在 12 小時後安排虛擬群集刪除*|
|刪除|虛擬叢集移除 (作為使用者啟動的操作)|虛擬叢集移除|90% 的操作在 1.5 小時內完成|

\*虛擬群集是每個硬體生成構建的。

\*\*4 vCores 部署選項於 2019 年 6 月發布,需要新的虛擬群集版本。 如果在目標子網中都有在 6 月 12 日之前創建的實例,則將自動部署一個新的虛擬群集來承載 4 個 vCore 實例。

\*\*\*12 小時是當前配置,但將來可能會更改,因此不要依賴它。 如果需要提前刪除虛擬群集(例如釋放子網),請參閱[刪除 Azure SQL 資料庫託管實例後刪除子網路](sql-database-managed-instance-delete-virtual-cluster.md)。

### <a name="instance-availability-during-management"></a>管理期間的實體可用性

在部署和刪除操作期間,用戶端應用程式不能使用託管實例。

託管實例在更新操作期間可用,但更新結束時發生故障轉移導致的停機時間很短,通常持續 10 秒。 這樣做的例外情況是更新通用服務層中的預留存儲空間,不會引發故障轉移,也不會影響實例可用性。

> [!IMPORTANT]
> 故障轉移的持續時間在資料庫上由於[恢復時間較長](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process)而發生長時間運行的事務時可能有很大差異。 因此,不建議擴展 Azure SQL 資料庫託管實例的計算或存儲,或者使用長時間運行的事務(資料導入、數據處理作業、索引重建等)同時更改服務層。 將在操作結束時執行的資料庫故障轉移將取消正在進行的事務,並導致較長的恢復時間。

> [!TIP]
> 更新通用服務層中的預留存儲空間不會導致故障轉移,也不會影響實例可用性。

[加速資料庫恢復](sql-database-accelerated-database-recovery.md)當前不適用於 Azure SQL 資料庫託管實例。 一旦啟用,此功能將顯著減少故障轉移時間的可變性,即使在長時間運行的事務的情況下也是如此。

### <a name="canceling-management-operations"></a>取消管理作業

下表總結了取消特定管理操作和典型總體持續時間的能力:

類別  |作業  |可取消  |估計取消持續時間  |
|---------|---------|---------|---------|
|部署 |實體建立 |否 |  |
|更新 |實體儲存到/向下延伸(一般) |否 |  |
|更新 |實體儲存到/向下延伸(業務關鍵) |是 |90% 的操作在 5 分鐘內完成 |
|更新 |實體計算 (vCores) 上下延伸 (通用) |是 |90% 的操作在 5 分鐘內完成 |
|更新 |實體計算 (vCores) 向上與向下延伸(業務關鍵) |是 |90% 的操作在 5 分鐘內完成 |
|更新 |實例服務層更改(業務關鍵型的通用用途,反之亦然) |是 |90% 的操作在 5 分鐘內完成 |
|刪除 |執行個體刪除 |否 |  |
|刪除 |虛擬叢集移除 (作為使用者啟動的操作) |否 |  |

要取消管理操作,請轉到概述邊欄選項卡,然後單擊正在進行的操作的通知框。 從右側,將顯示一個正在進行操作的屏幕,並且將顯示取消操作的按鈕。 首次按一下後,系統將要求您再次按一下並確認要取消該操作。

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

提交和處理取消請求後,如果取消提交成功,您將獲得通知。 

如果取消成功,管理操作將在幾分鐘內取消,從而導致失敗。

![取消操作結果](./media/sql-database-managed-instance/canceling-operation-result.png)

如果取消請求失敗或取消按鈕未啟動,則意味著管理操作已進入不可取消狀態,並且將在幾分鐘內完成。 管理操作將繼續執行,直到完成。

> [!IMPORTANT]
> 取消操作當前僅在門戶中支援。

## <a name="advanced-security-and-compliance"></a>進階安全性與合規性

受控執行個體部署選項結合了 Azure 雲端與 SQL Server 資料庫引擎所提供的進階安全性功能。

### <a name="managed-instance-security-isolation"></a>受控執行個體的安全性隔離

受控執行個體提供額外的安全性隔離，可與 Azure 雲端中的其他租用戶隔離。 安全性隔離包括：

- 使用 Azure 快速路由或 VPN 閘道[實現本機器虛擬網路](sql-database-managed-instance-connectivity-architecture.md)並連接到本地環境。
- 在預設部署中,SQL 終結點僅透過專用 IP 位址公開,允許從專用 Azure 或混合網路進行安全連接。
- 單一租用戶具有專用的基礎結構 (計算、儲存體)。

下圖概述您應用程式的各種連線選項：

![高可用性](./media/sql-database-managed-instance/application-deployment-topologies.png)  

如需深入了解子網路層級的 VNet 整合和網路原則強制施行，請參閱[受控執行個體的 VNet 架構](sql-database-managed-instance-connectivity-architecture.md)和[將應用程式連線到受控執行個體](sql-database-managed-instance-connect-app.md)。

> [!IMPORTANT]
> 將多個受控執行個體放在相同子網路中 (如果您的安全性需求允許的話)，因為這會帶來額外的好處。 將執行個體放在相同子網路中，可大幅簡化網路基礎結構的維護工作，並且可減少執行個體的佈建時間，因為長時間的佈建期間與在子網路中部署第一個受控執行個體的成本有關。

### <a name="azure-sql-database-security-features"></a>Azure SQL Database 安全性功能

Azure SQL Database 提供一組可用來保護資料的進階安全性功能。

- [託管實例審核](sql-database-managed-instance-auditing.md)跟蹤資料庫事件並將其寫入放置在 Azure 儲存帳戶中的審核日誌檔。 稽核有助於保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。
- 移動中資料加密 - 受控執行個體會使用傳輸層安全性對移動中的資料加密，藉此保護您的資料。 除了傳輸層安全性，受控執行個體部署選項會使用 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 來保護傳輸中、待用和查詢處理期間的敏感性資料。 Always Encrypted 是業界優先，可提供無與倫比的資料安全性，以對抗涉及重要資料竊取的入侵。 例如，透過 Always Encrypted，信用卡號碼會永遠加密儲存在資料庫中，即使在查詢處理期間，都允許需要處理該資料的已授權人員或應用程式在使用時解密。
- [高級威脅防護](sql-database-managed-instance-threat-detection.md)通過提供服務中內置的附加安全智能層來補充[審核](sql-database-managed-instance-auditing.md),這些智慧可檢測訪問或利用資料庫的異常和潛在有害嘗試。 系統會警示您有關可疑活動、潛在弱點、SQL 插入式攻擊和異常資料庫存取模式。 可以從[Azure 安全中心](https://azure.microsoft.com/services/security-center/)查看高級威脅防護警報,並提供可疑活動的詳細資訊,並建議如何調查和緩解威脅的操作。  
- [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)可藉由遮罩處理，使不具權限的使用者無法看見敏感性資料。 動態資料遮罩可讓您在應用程式層級受到最小影響的情況下指定要顯示多少敏感性資料，而協助防止未經授權者存取敏感性資料。 這項原則式安全性功能會將機密資料隱藏在指定資料庫欄位的查詢結果集內，而資料庫中的資料則不會變更。
- [資料列層級安全性](/sql/relational-databases/security/row-level-security)讓您能夠根據執行查詢之使用者的特性 (例如，依群組成員資格或執行內容) 來控制資料庫資料表中的資料列存取。 資料列層級安全性 (RLS) 可簡化應用程式安全性的設計和編碼。 RLS 可讓您實作資料的資料列存取限制。 例如，確保背景工作角色只能存取其部門相關資料列，或將資料存取權限制為僅限相關資料。
- [透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 會將受控執行個體的資料檔案加密，也稱為「待用資料加密」。 TDE 會執行資料和記錄檔的即時 I/O 加密和解密。 加密會使用資料庫加密金鑰 (DEK)，此金鑰會儲存在資料庫開機記錄中，以在復原期間提供可用性。 您可以使用透明資料加密來保護受控執行個體中的所有資料庫。 TDE 是 SQL Server 經實證的靜態加密技術，許多合規性標準都需要這項技術才能防禦儲存媒體的竊取。

透過 Azure 資料庫移轉服務 (DMS) 或原生還原，可支援將加密的資料庫遷移到受控執行個體。 如果計劃使用本機還原遷移加密資料庫,則需要將現有 TDE 證書從本地 SQL Server 或虛擬機器中的 SQL Server 移到託管實例。 如需移轉選項的詳細資訊，請參閱[將 SQL Server 執行個體移轉至受控執行個體](sql-database-managed-instance-migrate.md)。

## <a name="azure-active-directory-integration"></a>Azure Active Directory 整合

受控執行個體部署選項支援傳統的 SQL Server 資料庫引擎登入以及與 Azure Active Directory (AAD) 整合的登入。 Azure AD 伺服器主體(登入) (**公開預覽**) 是您使用於內部部署環境的 Azure 雲端版內部部署資料庫登入。 Azure AD 伺服器主體(登錄名)使您能夠將 Azure 活動目錄租戶中的使用者和組指定為真正的實例範圍主體,能夠執行任何實例級操作,包括在同一託管實例中的跨資料庫查詢。

引入新的語法來建立 Azure AD 伺服器主體(登入名),**從外部提供者**。 有關語法的詳細資訊,請參閱<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">建立登入</a>,並查看[為託管實例文章預先使用 Azure 活動目錄管理員](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 整合和多重要素驗證

受控執行個體部署選項可讓您透過 [Azure Active Directory 整合](sql-database-aad-authentication.md)，集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 這項功能簡化了權限管理並增強安全性。 Azure Active Directory 支援[多重要素驗證](sql-database-ssms-mfa-authentication-configure.md) (MFA)，以提高資料和應用程式安全性，同時支援單一登入程序。

### <a name="authentication"></a>驗證

受控執行個體驗證是指使用者連線到資料庫時如何證明他們的身分識別。 SQL Database 支援兩種驗證類型：  

- **SQL 驗證**：

  此驗證方法會使用使用者名稱和密碼。
- **Azure 的目錄身份認證**:

  此驗證方法會使用由 Azure Active Directory 管理的身分識別，並且受控網域和整合式網域都支援此驗證。 [盡可能](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)使用 Active Directory 驗證 (整合式安全性)。

### <a name="authorization"></a>授權

授權是指使用者可以在 Azure SQL Database 內執行的動作，這是由使用者帳戶的資料庫角色成員資格和物件層級權限所控制。 受控執行個體與 SQL Server 2017 具有相同的授權功能。

## <a name="database-migration"></a>資料庫移轉

受控執行個體部署選項鎖定的是透過將大量資料庫從內部部署或 IaaS 資料庫實作移轉的使用者案例。 受控執行個體支援數個資料庫移轉選項：

### <a name="back-up-and-restore"></a>備份及還原  

移轉方法會利用 SQL 備份到 Azure Blob 儲存體。 透過 [T-SQL RESTORE 命令](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)，儲存在 Azure 儲存體 Blob 的備份可以直接用來還原到受控執行個體。

- 關於如何回復寬域匯入器 - 標準資料庫備份檔案的快速入門,請參考[將備份檔回復到託管實例](sql-database-managed-instance-get-started-restore.md)。 此快速入門顯示您必須將備份檔上傳到 Azure Blob 儲存,並使用共享存取簽名 (SAS) 金鑰保護它。
- 如需從 URL 還原的資訊，請參閱[從 URL 原生還原](sql-database-managed-instance-migrate.md#native-restore-from-url)。

> [!IMPORTANT]
> 來自受控執行個體的備份只能還原至其他受控執行個體。 它們無法還原至內部部署 SQL Server，或還原至單一資料庫/彈性集區。

### <a name="data-migration-service"></a>資料移轉服務

Azure 資料庫移轉服務是一個完全受控的服務，能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低。 此服務可簡化將現有協力廠商和 SQL Server 資料庫移至 Azure SQL Database (單一資料庫、彈性集區中的集區資料庫，以及受控執行個體中的執行個體資料庫) 與 Azure VM 中的 SQL Server 所需的工作。 請參閱[如何使用 DMS 將您的內部部署資料庫遷移至受控執行個體](https://aka.ms/migratetoMIusingDMS)。

## <a name="sql-features-supported"></a>SQL 功能支援

受控執行個體部署選項的目標是在各階段中，為內部部署 SQL Server 提供幾乎 100% 的介面區相容性，直到服務正式運作為止。 如需功能和比較清單，請參閱 [SQL Database 功能比較](sql-database-features.md)，而如需受控執行個體與 SQL Server 的 T-SQL 差異清單，請參閱[受控執行個體與 SQL Server 的 T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)。

受控執行個體部署選項支援與 SQL 2008 資料庫的回溯相容性。 支援直接從 SQL 2005 資料庫伺服器進行移轉，移轉後，SQL 2005 資料庫的相容性層級會更新為 SQL 2008。
  
下圖概述受控執行個體中的介面區相容性：  

![移轉](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>SQL Server 內部部署和受控執行個體之間的主要差異

受控執行個體部署選項的優勢是其在雲端中一律是最新狀態，這表示內部部署 SQL Server 中的某些功能可能已過時、已停用或已有替代方案。 當工具需要識別特定功能以稍微不同的方式工作或服務在未完全控制的環境中運行時,在某些情況下::

- 高可用性會使用類似 [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)的技術來內建及預先設定。
- 自動備份和時間點還原。 客戶可以起始 `copy-only` 備份，這不會干擾自動備份鏈結。
- 託管實例不允許指定完整的物理路徑,因此必須以不同的方式支援所有相應的方案:RESTORE DB 不支援"隨移動",創建資料庫不允許物理路徑,BULK INSERT 僅適用於 Azure Blob 等。
- 託管實例支援[Azure AD 身份驗證](sql-database-aad-authentication.md),作為 Windows 身份驗證的雲端替代。
- 受控執行個體都會自動為包含記憶體內部 OLTP 物件的資料庫管理 XTP 檔案群組和檔案
- 受控執行個體支援 SQL Server Integration Services (SSIS)，且可主控儲存 SSIS 封裝的 SSIS 目錄 (SSISDB)，但會在 Azure Data Factory (ADF) 中的受控 Azure-SSIS Integration Runtime (IR) 上執行，請參閱[在 ADF 中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime) \(英文\)。 要比較 SQL 資料庫中的 SSIS 功能,請參考[Azure SQL 資料庫單個資料庫、彈性池和託管實例](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)。

### <a name="managed-instance-administration-features"></a>受控執行個體的管理功能

受控執行個體部署選項可讓系統管理員花較少的時間處理系統管理工作，因為 SQL Database 服務會為您執行這些設定，或大幅簡化這些工作。 例如[,OS/RDBMS 安裝和修補](sql-database-high-availability.md)、[動態實例調整大小和配置](sql-database-single-database-scale.md)、[備份](sql-database-automated-backups.md)、[資料庫複製](replication-with-sql-database-managed-instance.md)(包括系統資料庫,[高可用性設定](sql-database-high-availability.md)以及運行狀況[和效能監視](../azure-monitor/insights/azure-sql.md)資料串流的配置。

> [!IMPORTANT]
> 如需可支援、部分支援和不支援的功能清單，請參閱[SQL Database 功能](sql-database-features.md)。 有關託管實例與 SQL Server 中的 T-SQL 差異的清單,請參閱[託管實例 T-SQL 與 SQL Server 的差異](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>如何以程式設計方式識別受控執行個體

下表顯示數個透過 Transact SQL 使用的屬性，可用來檢測出應用程式正在使用受控執行個體，並擷取重要的屬性。

|屬性|值|註解|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|此值與 SQL Database 中的相同。 這並不**表示**SQL 引擎版本 12(SQL Server 2014)。 託管實例始終運行最新的穩定 SQL 引擎版本,該版本等於或高於 SQL Server 的最新可用 RTM 版本。  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|此值與 SQL Database 中的相同。|
|`SERVERPROPERTY('EngineEdition')`|8|此值只會識別出受控執行個體。|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|下列格式的完整執行個體 DNS 名稱：`<instanceName>`.`<dnsPrefix>`.database.windows.net，其中 `<instanceName>` 是客戶提供的名稱，而 `<dnsPrefix>` 是自動產生的部分名稱，確保全域 DNS 名稱是唯一的 (例如，"wcus17662feb9ce98")|範例：my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>後續步驟

- 要瞭解如何創建第一個託管實例,請參閱[快速入門指南](sql-database-managed-instance-get-started.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
- 如需 VNet 組態的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-connectivity-architecture.md)。
- 有關建立託管實例並從備份檔還原資料庫的快速入門,請參閱[建立託管實例](sql-database-managed-instance-get-started.md)。
- 有關使用 Azure 資料庫遷移服務 (DMS) 進行遷移的教學,請參考[DMS 進行託管實例遷移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 有關使用內置故障排除智慧對託管實例資料庫效能的進階監視,請參閱[使用 Azure SQL 分析監視 Azure SQL 資料庫](../azure-monitor/insights/azure-sql.md)。
- 有關定價資訊,請參閱[SQL 資料庫託管實例定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
