---
title: SQL 受控實例總覽
description: 本文說明 Azure SQL Database 受控實例。
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
ms.openlocfilehash: 04b07ff60c882501c49ad58607db867e7e99897c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80879066"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>什麼是 Azure SQL Database 受控實例？

受控執行個體是 Azure SQL Database 的新部署選項，幾乎可與最新 SQL Server 內部部署環境 (Enterprise Edition) 資料庫引擎 100% 相容，並提供原生[虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 實作，可解決常見的安全性考量，以及提供有利於內部部署 SQL Server 客戶的[商務模型](https://azure.microsoft.com/pricing/details/sql-database/)。 受控執行個體部署模型可讓現有 SQL Server 客戶透過最少的應用程式和資料庫變更，將他們的內部部署應用程式隨即轉移至雲端。 同時，受控執行個體部署模型選項會保留 PaaS 的所有功能 (自動修補和版本的更新、[自訂備份](sql-database-automated-backups.md)、[高可用性](sql-database-high-availability.md))，可以大幅降低管理負擔和 TCO。

> [!IMPORTANT]
> 如需目前可用受控執行個體部署模型的區域清單，請參閱[支援的區域](sql-database-managed-instance-resource-limits.md#supported-regions)。

下圖概述受控執行個體的主要功能：

![主要功能](./media/sql-database-managed-instance/key-features.png)

受控執行個體部署模型專為以下客戶設計：想要盡可能輕鬆地將大量應用程式，從內部部署或 IaaS、自行建置或 ISV 提供的環境遷移至完全受控的 PaaS 雲端環境。 透過在 Azure 中使用完全自動化的[資料移轉服務（DMS）](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) ，客戶可以將其內部部署 SQL Server 隨即轉移至受控實例，以提供與內部部署 SQL Server 的相容性，以及完全隔離具有原生 VNet 支援的客戶實例。  透過軟體保證，您可以使用[SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以折扣費率在受控實例上交換您現有的授權。  對於需要高度安全性和程式設計介面豐富的 SQL Server 執行個體而言，受控執行個體是雲端中最佳的移轉目的地。

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
|隔離的環境 ([VNet 整合](sql-database-managed-instance-connectivity-architecture.md)、單一租用戶服務、專用的運算和儲存體) <br>[透明資料加密（TDE）](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD 驗證](sql-database-aad-authentication.md)、單一登入支援 <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD 伺服器主體（登入）</a>  <br>與 Azure SQL 資料庫遵循相同的合規性標準 <br>[SQL 稽核](sql-database-managed-instance-auditing.md) <br>[進階威脅防護](sql-database-managed-instance-threat-detection.md) |用於自動化服務佈建與調整的 Azure Resource Manager API <br>用於手動服務佈建與調整的 Azure 入口網站功能 <br>資料移轉服務

> [!IMPORTANT]
> Azure SQL Database （所有部署選項）已通過許多合規性標準的認證。 如需詳細資訊，請參閱[Microsoft Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，您可以在此找到最新的 SQL Database 合規性認證清單。

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
| 內建的報告服務 (SSRS) | 不會使用[Power BI 編頁報表](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi)，而是在 Azure VM 上裝載 SSRS。 雖然受控執行個體無法將 SSRS 當做服務執行，但它可以使用 SQL Server authentication 來裝載外部報表服務器的 SSRS 2019 目錄資料庫。 |
|||

## <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

受控執行個體中[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)提供彈性、可控制、透明及直接的方法，讓您將內部部署工作負載需求平移到雲端。 此模型可讓您根據工作負載需求，變更計算、記憶體和儲存體。 VCore 模型也符合[SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，最高可省下55% 的費用。

在 V 核心模型中，您可以選擇各硬體世代。

- **第4代**邏輯 Cpu 是以 Intel E5-2673 v3 （Haswell） 2.4-GHz 處理器、附加 SSD、實體核心、每個核心 7 GB RAM，以及8到24虛擬核心之間的計算大小為基礎。
- **第5代**邏輯 Cpu 是以 Intel E5-2673 v4 （Broadwell） 2.3-GHz 和 Intel SP-8160 （Skylake）處理器、快速 NVMe SSD、超執行緒邏輯核心，以及4到80核心之間的計算大小為基礎。

在[受控實例資源限制](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)中尋找硬體世代之間差異的詳細資訊。

## <a name="managed-instance-service-tiers"></a>受控執行個體服務層級

有兩個服務層級可使用受控執行個體：

- **一般用途**：專為具有一般效能和 IO 延遲需求的應用程式所設計。
- **業務關鍵**：專為具有低 IO 延遲需求的應用程式所設計，以及對工作負載的基礎維護作業影響最小的情況。

這兩個服務層級均保證 99.99% 的可用性，可讓您單獨選取儲存體大小和計算容量。 如需 Azure SQL Database 高可用性架構的詳細資訊，請參閱[高可用性和 Azure SQL Database](sql-database-high-availability.md)。

### <a name="general-purpose-service-tier"></a>一般目的服務層級

下列清單說明一般用途服務層級的主要特色：

- 專為大多數有標準效能需求的商務應用程式所設計
- 高效能的 Azure Blob 儲存體 (8 TB)
- 根據可靠的 Azure Blob 儲存體和 [Azure Service Fabric](../service-fabric/service-fabric-overview.md) 內建的[高可用性](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)

如需詳細資訊，請參閱[一般用途層中的儲存體層](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c)和[受控執行個體 (一般用途) 的儲存體效能最佳做法和考量](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/) \(英文\)。

在[受控實例資源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)中尋找服務層級之間差異的詳細資訊。

### <a name="business-critical-service-tier"></a>業務關鍵服務層級

業務關鍵服務層級是為具有高 IO 需求的應用程式所建置。 使用數個分開的複本，針對失敗提供最高的復原能力。

下列清單概述業務關鍵服務層級的主要特色：

- 專為具有極高效能和 HA 需求的商務應用程式所設計
- 提供超級快速的本機 SSD 儲存體 (在 Gen4 上可達 1 TB，而在 Gen5 上可達 4 TB)
- 根據[Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)和[Azure Service Fabric](../service-fabric/service-fabric-overview.md)的內建[高可用性](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)。
- 可用於報告和其他唯讀工作負載的內建額外[唯讀資料庫複本](sql-database-read-scale-out.md)
- [記憶體內部 OLTP](sql-database-in-memory.md)，可用於具有高效能需求的工作負載  

在[受控實例資源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)中尋找服務層級之間差異的詳細資訊。


## <a name="managed-instance-management-operations"></a>受控實例管理作業

Azure SQL Database 提供管理作業，可讓您在不再需要時，用來自動部署新的受控執行個體、更新執行個體屬性和刪除執行個體。 本節提供管理作業及其一般持續時間的相關資訊。

為了支援[Azure 虛擬網路（vnet）內的部署](../virtual-network/virtual-network-for-azure-services.md)，並為客戶提供隔離和安全性，受控實例會依賴[虛擬叢集](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)，這代表一組在客戶的虛擬網路子網內部署的專屬獨立虛擬機器。 基本上，空白子網中的每個受控實例部署都會產生新的虛擬叢集增建。

已部署的受控實例上的後續作業可能也會對其基礎虛擬叢集造成影響。 這會影響管理作業的持續時間，因為部署額外的虛擬機器時，會有額外負荷，當您規劃新的部署或現有受控實例的更新時，需要加以考慮。

所有管理作業都可以分類如下：

- 實例部署（建立新的實例）。 
- 實例更新（變更實例屬性，例如虛擬核心或保留的儲存體）。
- 實例刪除。

一般而言，虛擬叢集上的作業會花費最長的時間。 虛擬叢集上的作業持續時間：以下是您通常可以預期的值（根據現有的服務遙測資料而定）。。

- 建立虛擬叢集。 這是實例管理作業中的同步步驟。 **90% 的作業會在4小時內完成**。
- 虛擬叢集調整大小（擴充或壓縮）。 展開是同步步驟，而壓縮是以非同步方式執行（不會影響實例管理作業的持續時間）。 **90% 的叢集擴充在2.5 小時內完成**。
- 刪除虛擬叢集。 刪除是非同步步驟，但也可以在空的虛擬叢集上[手動起始](sql-database-managed-instance-delete-virtual-cluster.md)，在此情況下，它會以同步方式執行。 **90% 的虛擬叢集刪除在1.5 小時內完成**。

此外，實例的管理也可能包含託管資料庫上的其中一項作業，這會導致較長的持續時間：

- 從 Azure 儲存體附加資料庫檔案。 這是一個同步步驟，例如計算（vCore），或在一般用途服務層級中向上或向下調整的儲存體。 **這些作業的90% 會在5分鐘內完成**。
- Always On 可用性群組植入。 這是一種同步步驟，例如計算（vCore），或業務關鍵服務層級中的儲存體調整，以及將服務層從一般用途變更為業務關鍵（反之亦然）。 這項作業的持續時間與資料庫總大小以及目前資料庫活動（使用中交易數目）成正比。 更新實例時的資料庫活動可能會對總持續時間產生明顯的差異。 **這些作業的90% 是以 220 GB/小時或更高的時間執行**。

下表摘要說明作業和一般的整體持續時間：

|類別  |作業  |長時間執行的區段  |預估的持續時間  |
|---------|---------|---------|---------|
|**部署** |空白子網中的第一個實例|建立虛擬叢集|90% 的作業在4小時內完成|
|部署 |非空白子網中另一個硬體世代的第一個實例（例如，第一個 Gen 5 實例，位於具有 Gen 4 實例的子網中）|虛擬叢集建立 *|90% 的作業在4小時內完成|
|部署 |在空白或非空白的子網中，第一個建立4虛擬核心的實例|虛擬叢集建立 * *|90% 的作業在4小時內完成|
|部署 |在非空白子網內建立的後續實例（第二、第三等實例）|虛擬叢集調整大小|90% 的作業在2.5 小時內完成|
|**更新** |實例屬性變更（管理員密碼、AAD 登入、Azure Hybrid Benefit 旗標）|N/A|最多1分鐘|
|更新 |實例儲存體相應增加/減少（一般用途服務層級）|附加資料庫檔案|90% 的作業會在5分鐘內完成|
|更新 |實例儲存體相應增加/減少（商務關鍵服務層級）|-虛擬叢集調整大小<br>-Always On 可用性群組植入|90% 的作業會在2.5 小時內完成，並將所有資料庫植入的時間（220 GB/小時）|
|更新 |實例計算（虛擬核心）相應增加和減少（一般用途）|-虛擬叢集調整大小<br>-附加資料庫檔案|90% 的作業在2.5 小時內完成|
|更新 |實例計算（虛擬核心）相應增加和減少（業務關鍵）|-虛擬叢集調整大小<br>-Always On 可用性群組植入|90% 的作業會在2.5 小時內完成，並將所有資料庫植入的時間（220 GB/小時）|
|更新 |實例相應減少為4虛擬核心（一般用途）|-虛擬叢集調整大小（如果是第一次完成，可能需要建立虛擬叢集 * *）<br>-附加資料庫檔案|90% 的作業以4小時5分鐘完成|
|更新 |實例相應減少為4虛擬核心（業務關鍵）|-虛擬叢集調整大小（如果是第一次完成，可能需要建立虛擬叢集 * *）<br>-Always On 可用性群組植入|90% 的作業會在4小時內完成，並將所有資料庫植入的時間（220 GB/小時）|
|更新 |實例服務層級變更（一般用途到業務關鍵，反之亦然）|-虛擬叢集調整大小<br>-Always On 可用性群組植入|90% 的作業會在2.5 小時內完成，並將所有資料庫植入的時間（220 GB/小時）|
|**操作**|執行個體刪除|所有資料庫的記錄尾備份|90% 作業會在最多1分鐘內完成。<br>注意：如果刪除子網中的最後一個實例，此作業會在12小時後排程刪除虛擬叢集 * * *|
|刪除|虛擬叢集刪除（作為使用者起始的作業）|虛擬叢集刪除|90% 的作業會在最多1.5 小時內完成|

\*虛擬叢集是根據硬體世代而建立的。

\*\*4虛擬核心部署選項已于2019年6月發行，而且需要新的虛擬叢集版本。 如果您的目標子網中有實例是在6月12日之前建立，則會自動將新的虛擬叢集部署到主機4個 vCore 實例。

\*\*\*12小時是目前的設定，但未來可能會變更，因此不會對其進行硬相依性。 如果您需要稍早刪除虛擬叢集（例如，為了釋放子網），請參閱刪除[Azure SQL Database 受控實例之後，刪除子網](sql-database-managed-instance-delete-virtual-cluster.md)。

### <a name="instance-availability-during-management"></a>管理期間的實例可用性

在部署和刪除作業期間，用戶端應用程式無法使用受控實例。

在更新作業期間可以使用受控實例，但在更新結束時（通常會持續10秒），會發生短暫的停機時間。 例外狀況是更新一般用途服務層級中的保留儲存空間，這不會產生容錯移轉，也不會影響實例可用性。

> [!IMPORTANT]
> 容錯移轉的持續時間可能會因為在資料庫上發生長時間執行的交易而有很大的差異，因為復原[時間](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process)過長。 因此，不建議您調整 Azure SQL Database 受控實例的計算或儲存體，或同時使用長時間執行的交易（資料匯入、資料處理作業、索引重建等）來變更服務層級。 將在作業結束時執行的資料庫容錯移轉將會取消進行中的交易，並導致長時間復原。

> [!TIP]
> 一般用途服務層級中的保留儲存空間更新不會產生容錯移轉，也不會影響實例可用性。

Azure SQL Database 受控實例目前無法使用[加速資料庫](sql-database-accelerated-database-recovery.md)復原。 一旦啟用，這項功能會大幅降低容錯移轉時間的變動性，即使是長時間執行的交易也一樣。

### <a name="canceling-management-operations"></a>取消管理作業

下表摘要說明取消特定管理作業的能力，以及一般的整體持續時間：

類別  |作業  |取消  |估計的取消持續時間  |
|---------|---------|---------|---------|
|部署 |實例建立 |否 |  |
|更新 |實例儲存體相應增加/減少（一般用途） |否 |  |
|更新 |實例儲存體相應增加/減少（業務關鍵） |是 |90% 的作業會在5分鐘內完成 |
|更新 |實例計算（虛擬核心）相應增加和減少（一般用途） |是 |90% 的作業會在5分鐘內完成 |
|更新 |實例計算（虛擬核心）相應增加和減少（業務關鍵） |是 |90% 的作業會在5分鐘內完成 |
|更新 |實例服務層級變更（一般用途到業務關鍵，反之亦然） |是 |90% 的作業會在5分鐘內完成 |
|刪除 |執行個體刪除 |否 |  |
|刪除 |虛擬叢集刪除（作為使用者起始的作業） |否 |  |

若要取消管理作業，請移至 [總覽] 分頁，然後按一下 [進行中作業的通知方塊]。 右側會出現具有進行中作業的畫面，而且會有取消作業的按鈕。 第一次按一下之後，系統會要求您再次按一下，並確認您想要取消此操作。

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

提交並處理取消要求之後，如果取消提交成功，您就會收到通知。 

如果取消成功，管理作業會在幾分鐘內取消，導致失敗。

![正在取消操作結果](./media/sql-database-managed-instance/canceling-operation-result.png)

如果 [取消要求失敗] 或 [取消] 按鈕未啟用，這表示管理作業已進入 [無法取消] 狀態，而且會在幾分鐘內完成。 管理作業會繼續執行，直到完成為止。

> [!IMPORTANT]
> 目前只有在入口網站中才支援取消作業。

## <a name="advanced-security-and-compliance"></a>進階安全性與合規性

受控執行個體部署選項結合了 Azure 雲端與 SQL Server 資料庫引擎所提供的進階安全性功能。

### <a name="managed-instance-security-isolation"></a>受控執行個體的安全性隔離

受控執行個體提供額外的安全性隔離，可與 Azure 雲端中的其他租用戶隔離。 安全性隔離包括：

- 使用 Azure Express Route 或 VPN 閘道[執行原生虛擬網路](sql-database-managed-instance-connectivity-architecture.md)和內部部署環境的連線。
- 在預設部署中，SQL 端點只會透過私人 IP 位址公開，並允許來自私人 Azure 或混合式網路的安全連線。
- 單一租用戶具有專用的基礎結構 (計算、儲存體)。

下圖概述您應用程式的各種連線選項：

![高可用性](./media/sql-database-managed-instance/application-deployment-topologies.png)  

如需深入了解子網路層級的 VNet 整合和網路原則強制施行，請參閱[受控執行個體的 VNet 架構](sql-database-managed-instance-connectivity-architecture.md)和[將應用程式連線到受控執行個體](sql-database-managed-instance-connect-app.md)。

> [!IMPORTANT]
> 將多個受控執行個體放在相同子網路中 (如果您的安全性需求允許的話)，因為這會帶來額外的好處。 將執行個體放在相同子網路中，可大幅簡化網路基礎結構的維護工作，並且可減少執行個體的佈建時間，因為長時間的佈建期間與在子網路中部署第一個受控執行個體的成本有關。

### <a name="azure-sql-database-security-features"></a>Azure SQL Database 安全性功能

Azure SQL Database 提供一組可用來保護資料的進階安全性功能。

- 「[受控實例」審核](sql-database-managed-instance-auditing.md)會追蹤資料庫事件，並將它們寫入至您 Azure 儲存體帳戶中的 audit 記錄檔。 稽核有助於保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。
- 移動中資料加密 - 受控執行個體會使用傳輸層安全性對移動中的資料加密，藉此保護您的資料。 除了傳輸層安全性，受控執行個體部署選項會使用 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 來保護傳輸中、待用和查詢處理期間的敏感性資料。 Always Encrypted 是業界優先，可提供無與倫比的資料安全性，以對抗涉及重要資料竊取的入侵。 例如，透過 Always Encrypted，信用卡號碼會永遠加密儲存在資料庫中，即使在查詢處理期間，都允許需要處理該資料的已授權人員或應用程式在使用時解密。
- [先進的威脅防護](sql-database-managed-instance-threat-detection.md)提供一層內建于服務中的額外安全情報，以偵測存取或惡意探索資料庫的異常且可能有害的嘗試，藉此補充[審核](sql-database-managed-instance-auditing.md)程式。 系統會警示您有關可疑活動、潛在弱點、SQL 插入式攻擊和異常資料庫存取模式。 您可以從[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)查看先進的威脅防護警示，並提供可疑活動的詳細資料，以及如何調查和緩和威脅的建議動作。  
- [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)可藉由遮罩處理，使不具權限的使用者無法看見敏感性資料。 動態資料遮罩可讓您在應用程式層級受到最小影響的情況下指定要顯示多少敏感性資料，而協助防止未經授權者存取敏感性資料。 這項原則式安全性功能會將敏感性資料隱藏在指定資料庫欄位的查詢結果集內，而資料庫中的資料則不會變更。
- [資料列層級安全性](/sql/relational-databases/security/row-level-security)讓您能夠根據執行查詢之使用者的特性 (例如，依群組成員資格或執行內容) 來控制資料庫資料表中的資料列存取。 資料列層級安全性 (RLS) 可簡化應用程式安全性的設計和編碼。 RLS 可讓您實作資料的資料列存取限制。 例如，確保背景工作角色只能存取其部門相關資料列，或將資料存取權限制為僅限相關資料。
- [透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 會將受控執行個體的資料檔案加密，也稱為「待用資料加密」。 TDE 會執行資料和記錄檔的即時 I/O 加密和解密。 加密會使用資料庫加密金鑰 (DEK)，此金鑰會儲存在資料庫開機記錄中，以在復原期間提供可用性。 您可以使用透明資料加密來保護受控執行個體中的所有資料庫。 TDE SQL Server 是由許多合規性標準所需的經證實的加密 rest 技術，可防止儲存媒體遭竊。

透過 Azure 資料庫移轉服務 (DMS) 或原生還原，可支援將加密的資料庫遷移到受控執行個體。 如果您想要使用原生還原來遷移加密的資料庫，請將現有的 TDE 憑證從內部部署 SQL Server 或虛擬機器中的 SQL Server 遷移至受控實例，是必要的步驟。 如需移轉選項的詳細資訊，請參閱[將 SQL Server 執行個體移轉至受控執行個體](sql-database-managed-instance-migrate.md)。

## <a name="azure-active-directory-integration"></a>Azure Active Directory 整合

受控執行個體部署選項支援傳統的 SQL Server 資料庫引擎登入以及與 Azure Active Directory (AAD) 整合的登入。 Azure AD 伺服器主體(登入) (**公開預覽**) 是您使用於內部部署環境的 Azure 雲端版內部部署資料庫登入。 Azure AD 伺服器主體（登入）可讓您將 Azure Active Directory 租使用者中的使用者和群組指定為真正實例範圍的主體，能夠執行任何實例層級的作業，包括在相同受控實例中的跨資料庫查詢。

引進了新的語法，可**從外部提供者**建立 Azure AD 伺服器主體（登入）。 如需語法的詳細資訊，請參閱<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">建立登</a>入和參閱為[受控實例布建 Azure Active Directory 系統管理員一](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)文。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 整合和多重要素驗證

受控執行個體部署選項可讓您透過 [Azure Active Directory 整合](sql-database-aad-authentication.md)，集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 這項功能簡化了權限管理並增強安全性。 Azure Active Directory 支援[多重要素驗證](sql-database-ssms-mfa-authentication-configure.md) (MFA)，以提高資料和應用程式安全性，同時支援單一登入程序。

### <a name="authentication"></a>驗證

受控執行個體驗證是指使用者連線到資料庫時如何證明他們的身分識別。 SQL Database 支援兩種驗證類型：  

- **SQL 驗證**：

  此驗證方法會使用使用者名稱和密碼。
- **Azure Active Directory 驗證**：

  此驗證方法會使用由 Azure Active Directory 管理的身分識別，並且受控網域和整合式網域都支援此驗證。 [盡可能](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)使用 Active Directory 驗證 (整合式安全性)。

### <a name="authorization"></a>授權

授權是指使用者可以在 Azure SQL Database 內執行的動作，這是由使用者帳戶的資料庫角色成員資格和物件層級權限所控制。 受控執行個體與 SQL Server 2017 具有相同的授權功能。

## <a name="database-migration"></a>資料庫移轉

受控執行個體部署選項鎖定的是透過將大量資料庫從內部部署或 IaaS 資料庫實作移轉的使用者案例。 受控執行個體支援數個資料庫移轉選項：

### <a name="back-up-and-restore"></a>備份及還原  

移轉方法會利用 SQL 備份到 Azure Blob 儲存體。 透過 [T-SQL RESTORE 命令](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)，儲存在 Azure 儲存體 Blob 的備份可以直接用來還原到受控執行個體。

- 如需示範如何還原 Wide World 匯入工具-標準資料庫備份檔案的快速入門，請參閱將[備份檔案還原至受控實例](sql-database-managed-instance-get-started-restore.md)。 本快速入門說明您必須將備份檔案上傳至 Azure blob 儲存體，並使用共用存取簽章（SAS）金鑰來保護它。
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

受控執行個體部署選項的優勢是其在雲端中一律是最新狀態，這表示內部部署 SQL Server 中的某些功能可能已過時、已停用或已有替代方案。 在某些情況下，當工具需要辨識特定功能的運作方式稍有不同，或服務正在環境中執行時，您無法完全控制：

- 高可用性會使用類似 [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)的技術來內建及預先設定。
- 自動備份和時間點還原。 客戶可以起始 `copy-only` 備份，這不會干擾自動備份鏈結。
- 受控實例不允許指定完整的實體路徑，因此必須以不同的方式支援所有對應的案例： RESTORE DB 不支援 WITH MOVE、CREATE DB 不允許實體路徑、BULK INSERT 僅適用于 Azure Blob 等等。
- 受控實例支援[Azure AD 驗證](sql-database-aad-authentication.md)做為 Windows 驗證的雲端替代方案。
- 受控執行個體都會自動為包含記憶體內部 OLTP 物件的資料庫管理 XTP 檔案群組和檔案
- 受控執行個體支援 SQL Server Integration Services (SSIS)，且可主控儲存 SSIS 封裝的 SSIS 目錄 (SSISDB)，但會在 Azure Data Factory (ADF) 中的受控 Azure-SSIS Integration Runtime (IR) 上執行，請參閱[在 ADF 中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime) \(英文\)。 若要比較 SQL Database 中的 SSIS 功能，請參閱[比較 Azure SQL Database 單一資料庫、彈性集區和受控實例](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)。

### <a name="managed-instance-administration-features"></a>受控執行個體的管理功能

受控執行個體部署選項可讓系統管理員花較少的時間處理系統管理工作，因為 SQL Database 服務會為您執行這些設定，或大幅簡化這些工作。 例如， [OS/RDBMS 安裝和修補](sql-database-high-availability.md)、[動態實例的大小調整和](sql-database-single-database-scale.md)設定、[備份](sql-database-automated-backups.md)、[資料庫](replication-with-sql-database-managed-instance.md)複寫（包括系統資料庫）、[高可用性](sql-database-high-availability.md)設定，以及健康情況和[效能監視](../azure-monitor/insights/azure-sql.md)資料流程的設定。

> [!IMPORTANT]
> 如需可支援、部分支援和不支援的功能清單，請參閱[SQL Database 功能](sql-database-features.md)。 如需受控實例與 SQL Server 的 T-sql 差異清單，請參閱[受控實例與 SQL Server 的 t-sql 差異](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>如何以程式設計方式識別受控執行個體

下表顯示數個透過 Transact SQL 使用的屬性，可用來檢測出應用程式正在使用受控執行個體，並擷取重要的屬性。

|屬性|值|註解|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|此值與 SQL Database 中的相同。 這**不**表示 SQL 引擎第12版（SQL Server 2014）。 受控實例一律會執行最新的穩定 SQL 引擎版本，其等於或高於 SQL Server 的最新可用 RTM 版本。  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|此值與 SQL Database 中的相同。|
|`SERVERPROPERTY('EngineEdition')`|8|此值只會識別出受控執行個體。|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|下列格式的完整執行個體 DNS 名稱：`<instanceName>`.`<dnsPrefix>`.database.windows.net，其中 `<instanceName>` 是客戶提供的名稱，而 `<dnsPrefix>` 是自動產生的部分名稱，確保全域 DNS 名稱是唯一的 (例如，"wcus17662feb9ce98")|範例：my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立您的第一個受控實例，請參閱[快速入門手冊](sql-database-managed-instance-get-started.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
- 如需 VNet 組態的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-connectivity-architecture.md)。
- 如需建立受控實例並從備份檔案還原資料庫的快速入門，請參閱[建立受控實例](sql-database-managed-instance-get-started.md)。
- 如需使用 Azure 資料庫移轉服務（DMS）進行遷移的教學課程，請參閱[使用 DMS 的受控實例遷移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 如需使用內建的疑難排解智慧進行受控實例資料庫效能的先進監視，請參閱[使用 Azure SQL 分析監視 Azure SQL Database](../azure-monitor/insights/azure-sql.md)。
- 如需定價資訊，請參閱[SQL Database 受控實例定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
