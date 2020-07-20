---
title: 什麼是 Azure SQL 受控執行個體？
description: 瞭解 Azure SQL 受控執行個體如何與最新 SQL Server （Enterprise Edition）資料庫引擎提供接近100% 的相容性
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 06/25/2020
ms.openlocfilehash: 43fad6249d5c6f528353a819e03dd7401440e05d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391004"
---
# <a name="what-is-azure-sql-managed-instance"></a>什麼是 Azure SQL 受控執行個體？
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體是 Azure SQL 產品系列的一部分，它是智慧型、可擴充的雲端資料庫服務，可將最廣泛的 SQL Server 資料庫引擎相容性與完全受控且以高擴充平臺為服務的所有優點結合。 SQL 受控執行個體與最新的 SQL Server （Enterprise Edition）資料庫引擎幾乎100% 的相容性，並提供原生[虛擬網路（VNet）](../../virtual-network/virtual-networks-overview.md)來解決常見的安全性考慮，以及對現有的 SQL Server 客戶有利的[商業模型](https://azure.microsoft.com/pricing/details/sql-database/)。 SQL 受控執行個體可讓現有的 SQL Server 客戶將其內部部署應用程式隨即轉移至雲端，只需要最少的應用程式和資料庫變更。 同時，SQL 受控執行個體會保留所有 PaaS 功能（自動修補和版本更新、[自動備份](../database/automated-backups-overview.md)、[高可用性](../database/high-availability-sla.md)），以大幅降低管理負擔和 TCO。

> [!IMPORTANT]
> 如需目前可使用 SQL 受控執行個體的區域清單，請參閱[支援的區域](resource-limits.md#supported-regions)。

下圖概述 SQL 受控執行個體的主要功能：

![主要功能](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL 受控執行個體是針對想要將大量應用程式從內部部署或 IaaS、自行建立或 ISV 提供的環境遷移至完全受控 PaaS 雲端環境的客戶而設計，並盡可能減少遷移工作。 客戶可以使用完全自動化的[Azure 資料移轉服務](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)，將其現有的 SQL Server 實例隨即轉移至 SQL 受控執行個體，以提供與 SQL Server 的相容性，並以原生 VNet 支援完成客戶實例的隔離。  透過軟體保證，您可以使用[SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以折扣費率在 SQL 受控執行個體上交換現有授權。 SQL 受控執行個體是雲端中的最佳遷移目的地，適用于需要高安全性和豐富程式設計介面的 SQL Server 實例。

## <a name="key-features-and-capabilities"></a>重要功能

SQL 受控執行個體結合 Azure SQL Database 和 SQL Server 資料庫引擎中可用的最佳功能。

> [!IMPORTANT]
> SQL 受控執行個體會以最新 SQL Server 版本的所有功能執行，包括線上作業、自動計畫更正，以及其他企業效能增強功能。 [功能比較： AZURE SQL 受控執行個體與 SQL Server](../database/features-comparison.md)中會說明可用功能的比較。

| **PaaS 支援** | **業務持續性** |
| --- | --- |
|無須硬體採購和管理 <br>沒有管理基礎結構的管理負擔 <br>快速佈建和服務調整 <br>自動修補和版本升級 <br>與其他 PaaS 資料服務整合 |99.99% 的 SLA 運作時間  <br>內建[高可用性](../database/high-availability-sla.md) <br>使用[自動備份](../database/automated-backups-overview.md)保護資料 <br>客戶可設定的備份保留期限 <br>使用者起始的[備份](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[時間點資料庫還原](../database/recovery-using-backups.md#point-in-time-restore)功能 |
|**安全性與合規性** | **管理**|
|隔離的環境 ([VNet 整合](connectivity-architecture-overview.md)、單一租用戶服務、專用的運算和儲存體) <br>[透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure Active Directory （Azure AD）驗證](../database/authentication-aad-overview.md)，單一登入支援 <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD 伺服器主體（登入）</a>  <br>遵守與 Azure SQL Database 相同的合規性標準 <br>[SQL 審核](auditing-configure.md) <br>[進階威脅防護](threat-detection-configure.md) |用於自動化服務佈建與調整的 Azure Resource Manager API <br>用於手動服務佈建與調整的 Azure 入口網站功能 <br>資料移轉服務

> [!IMPORTANT]
> Azure SQL 受控執行個體已通過許多合規性標準的認證。 如需詳細資訊，請參閱[Microsoft Azure 合規性供應](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers)專案，您可以在此找到最新的 SQL 受控執行個體合規性認證清單（列在 [ **SQL Database**] 底下）。

下表顯示 SQL 受控執行個體的主要功能：

|功能 | 說明|
|---|---|
| SQL Server 版本/組建 | SQL Server 資料庫引擎（最新穩定） |
| 受控自動化備份 | Yes |
| 內建執行個體和資料庫的監視與計量 | Yes |
| 自動軟體修補 | Yes |
| 最新的資料庫引擎功能 | Yes |
| 每個資料庫的資料檔案 (ROWS) 數目 | 多個 |
| 每個資料庫的記錄檔 (LOG) 數目 | 1 |
| VNet - Azure Resource Manager 部署 | Yes |
| VNet - 傳統部署模型 | No |
| 入口網站支援 | Yes|
| 內建的整合服務 (SSIS) | 否 - SSIS 屬於 [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| 內建的 Analysis Services (SSAS) | 否 - SSAS 是個別 [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| 內建的報告服務 (SSRS) | 不會使用[Power BI 編頁報表](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi)，而是在 Azure VM 上裝載 SSRS。 雖然 SQL 受控執行個體無法將 SSRS 當做服務執行，但它可以使用 SQL Server 驗證，為安裝在 Azure 虛擬機器上的報表伺服器裝載[SSRS 類別目錄資料庫](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements)。 |
|||

## <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

適用于 SQL 受控執行個體的[vCore 為基礎的購買模型](../database/service-tiers-vcore.md)提供彈性、控制、透明度和直接的方式，將內部部署工作負載需求轉譯到雲端。 此模型可讓您根據工作負載需求，變更計算、記憶體和儲存體。 VCore 模型也符合 SQL Server 的[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) ，最高可省下55% 的費用。

在 vCore 模型中，您可以選擇硬體的世代。

- **第4代**邏輯 cpu 是以 Intel E5-2673 V3 （Haswell） 2.4-GHz 處理器、附加 SSD、實體核心、每個核心 7 GB RAM，以及8到24虛擬核心之間的計算大小為基礎。
- **第5代**邏輯 cpu 是以 Intel E5-2673 V4 （Broadwell） 2.3-GHz 和 Intel SP-8160 （Skylake）處理器、快速 NVMe SSD、超執行緒邏輯核心，以及4和80核心之間的計算大小為基礎。

尋找有關[SQL 受控執行個體資源限制](resource-limits.md#hardware-generation-characteristics)中硬體世代之間差異的詳細資訊。

## <a name="service-tiers"></a>服務層

SQL 受控執行個體提供兩個服務層級：

- **一般用途**：專為具有一般效能和 i/o 延遲需求的應用程式所設計。
- **業務關鍵**：專為低 i/o 延遲需求的應用程式所設計，以及對工作負載的基礎維護作業影響最小的情況。

這兩個服務層級均保證 99.99% 的可用性，可讓您單獨選取儲存體大小和計算容量。 如需有關 Azure SQL 受控執行個體高可用性架構的詳細資訊，請參閱[高可用性和 AZURE sql 受控執行個體](../database/high-availability-sla.md)。

### <a name="general-purpose-service-tier"></a>一般用途服務層級

下列清單描述一般用途服務層級的主要特性：

- 專為具有一般效能需求的大多數商務應用程式所設計
- 高效能的 Azure Blob 儲存體 (8 TB)
- 以可靠的 Azure Blob 儲存體和[Azure Service Fabric](../../service-fabric/service-fabric-overview.md)為基礎的內建[高可用性](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability)

如需詳細資訊，請參閱[一般用途層中的儲存層](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c)和[適用于 SQL 受控執行個體的儲存體效能最佳做法和考慮（一般用途）](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)。

尋找有關[SQL 受控執行個體資源限制](resource-limits.md#service-tier-characteristics)中服務層級差異的詳細資訊。

### <a name="business-critical-service-tier"></a>業務關鍵服務層級

商務關鍵服務層級是針對具有高 i/o 需求的應用程式所建立。 它可以使用數個隔離的複本，針對失敗提供最高的復原能力。

下列清單概述業務關鍵服務層級的主要特色：

- 專為具有極高效能和 HA 需求的商務應用程式所設計
- 提供超級快速的本機 SSD 儲存體 (在 Gen4 上可達 1 TB，而在 Gen5 上可達 4 TB)
- 根據[Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)和[Azure Service Fabric](../../service-fabric/service-fabric-overview.md)的內建[高可用性](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability)
- 可用於報告和其他唯讀工作負載的內建額外[唯讀資料庫複本](../database/read-scale-out.md)
- [記憶體內部 OLTP](../in-memory-oltp-overview.md)，可用於具有高效能需求的工作負載  

尋找有關[SQL 受控執行個體資源限制](resource-limits.md#service-tier-characteristics)中服務層級差異的詳細資訊。

## <a name="management-operations"></a>管理作業

Azure SQL 受控執行個體提供管理作業，可讓您在不再需要時，用來自動部署新的受控實例、更新實例屬性和刪除實例。 本節提供管理作業及其一般持續時間的相關資訊。

為了支援[Azure 虛擬網路內的部署](../../virtual-network/virtual-network-for-azure-services.md)，並為客戶提供隔離和安全性，SQL 受控執行個體依賴[虛擬叢集](connectivity-architecture-overview.md#high-level-connectivity-architecture)，這代表一組在客戶的虛擬網路子網內部署的專屬獨立虛擬機器。 基本上，空白子網中的每個受控實例部署都會產生新的虛擬叢集增建。

針對已部署的受控實例進行的後續作業，可能也會對基礎虛擬叢集造成影響。 這會影響管理作業的持續時間，因為部署額外的虛擬機器時，會有額外負荷，當您規劃新的部署或現有受控實例的更新時，需要加以考慮。

所有管理作業都可以分類如下：

- 實例部署（建立新的實例）。
- 實例更新（變更實例屬性，例如虛擬核心或保留的儲存體）。
- 實例刪除。

一般而言，虛擬叢集上的作業會花費最長的時間。 虛擬叢集上的作業持續時間：以下是根據現有的服務遙測資料，您通常可以預期的值。。

- **建立虛擬叢集**：這是實例管理作業中的同步步驟。 **90% 的作業會在4小時內完成**。
- **虛擬叢集調整大小（擴充或壓縮）**：展開是同步步驟，而壓縮是以非同步方式執行（不會影響實例管理作業的持續時間）。 **90% 的叢集擴充在2.5 小時內完成**。
- **虛擬叢集刪除**：刪除是非同步步驟，但也可以在空的虛擬叢集上[手動起始](virtual-cluster-delete.md)，在此情況下，它會以同步方式執行。 **90% 的虛擬叢集刪除在1.5 小時內完成**。

此外，實例的管理也可能包含託管資料庫上的其中一項作業，這會導致較長的持續時間：

- **從 Azure 儲存體附加資料庫**檔案：這是一個同步步驟（例如計算（vCore）），或是在一般用途服務層級中向上或向下調整的儲存體。 **這些作業的90% 會在5分鐘內完成**。
- **Always On 可用性群組植**入：這是一種同步步驟，例如計算（vCore），或業務關鍵服務層級中的儲存體調整，以及將服務層從一般用途變更為業務關鍵（反之亦然）。 這項作業的持續時間與資料庫總大小以及目前資料庫活動（使用中交易數目）成正比。 更新實例時的資料庫活動可能會對總持續時間產生明顯的差異。 **這些作業的90% 是以 220 GB/小時或更高的時間執行**。

下表摘要說明作業和一般的整體持續時間：

|類別  |操作  |長時間執行的區段  |預估的持續時間  |
|---------|---------|---------|---------|
|**部署** |空白子網中的第一個實例|建立虛擬叢集|90% 的作業會在4小時內完成。|
|部署 |非空白子網中另一個硬體世代的第一個實例（例如，第一個 Gen 5 實例，位於具有 Gen 4 實例的子網中）|虛擬叢集建立 *|90% 的作業會在4小時內完成。|
|部署 |在空白或非空白的子網中，第一個建立4虛擬核心的實例|虛擬叢集建立 * *|90% 的作業會在4小時內完成。|
|部署 |在非空白子網內建立的後續實例（第二、第三等實例）|虛擬叢集調整大小|90% 的作業會在2.5 小時後完成。|
|**更新** |實例屬性變更（系統管理員密碼，Azure AD 登入，Azure Hybrid Benefit 旗標）|N/A|最多1分鐘。|
|更新 |實例儲存體相應增加/減少（一般用途服務層級）|附加資料庫檔案|90% 的作業會在5分鐘內完成。|
|更新 |實例儲存體相應增加/減少（商務關鍵服務層級）|-虛擬叢集調整大小<br>-Always On 可用性群組植入|90% 的作業會在2.5 小時內完成，並將所有資料庫植入的時間（220 GB/小時）。|
|更新 |實例計算（虛擬核心）相應增加和減少（一般用途）|-虛擬叢集調整大小<br>-附加資料庫檔案|90% 的作業會在2.5 小時後完成。|
|更新 |實例計算（虛擬核心）相應增加和減少（業務關鍵）|-虛擬叢集調整大小<br>-Always On 可用性群組植入|90% 的作業會在2.5 小時內完成，並將所有資料庫植入的時間（220 GB/小時）。|
|更新 |實例相應減少為4虛擬核心（一般用途）|-虛擬叢集調整大小（如果是第一次完成，可能需要建立虛擬叢集 * *）<br>-附加資料庫檔案|90% 的作業以4小時5分鐘完成。 * *|
|更新 |實例相應減少為4虛擬核心（業務關鍵）|-虛擬叢集調整大小（如果是第一次完成，可能需要建立虛擬叢集 * *）<br>-Always On 可用性群組植入|90% 的作業會在4小時內完成，並將所有資料庫植入一次（220 GB/小時）。|
|更新 |實例服務層級變更（一般用途到業務關鍵，反之亦然）|-虛擬叢集調整大小<br>-Always On 可用性群組植入|90% 的作業會在2.5 小時內完成，並將所有資料庫植入的時間（220 GB/小時）。|
|**操作**|執行個體刪除|所有資料庫的記錄尾備份|90% 作業會在最多1分鐘內完成。<br>注意：如果刪除子網中的最後一個實例，此作業會在12小時後排程刪除虛擬叢集。 * * *|
|刪除|虛擬叢集刪除（作為使用者起始的作業）|虛擬叢集刪除|90% 的作業會在最多1.5 小時內完成。|

\*虛擬叢集是根據硬體世代而建立的。

\*\*4虛擬核心選項是在2019年6月發行，而且需要新的虛擬叢集版本。 如果您的目標子網中有實例是在6月12日之前建立，則會自動將新的虛擬叢集部署到主機4個 vCore 實例。

\*\*\*12小時是目前的設定，但未來可能會變更，因此不會對其進行硬相依性。 如果您需要稍早刪除虛擬叢集（例如，若要釋放子網），請參閱刪除[受控實例後刪除子網](virtual-cluster-delete.md)。

### <a name="instance-availability-during-management-operations"></a>管理作業期間的實例可用性

除了更新結束時所發生的容錯移轉所造成的短暫停機以外，**更新作業期間仍可使用**SQL 受控執行個體。 這通常會持續10秒，即使在中斷長時間執行的交易時，也是因為[加速資料庫](../accelerated-database-recovery.md)復原。

> [!IMPORTANT]
> 不建議您調整 Azure SQL 受控執行個體的計算或儲存體，或使用長時間執行的交易（資料匯入、資料處理作業、索引重建等等）來同時變更服務層級。 將在作業結束時執行的資料庫容錯移轉將會取消所有進行中的交易。

在部署和刪除作業期間，用戶端應用程式無法使用 SQL 受控執行個體。

### <a name="management-operations-cross-impact"></a>跨影響的管理作業

受控實例上的管理作業可能會影響放在相同虛擬叢集中之實例的其他管理作業。 這包括下列項目：

- 虛擬叢集中**長時間執行的還原作業**會放在相同子網中的其他實例建立或調整作業。<br/>**範例：** 如果有長時間執行的還原作業，而且在相同的子網中有建立或調整要求，此要求將需要較長的時間才能完成，因為它會等候還原作業完成後，再繼續進行。
    
- **後續的實例建立或調整**作業會由先前起始的實例建立或實例規模（起始虛擬叢集調整大小）暫停。<br/>**範例：** 如果相同虛擬叢集下的相同子網中有多個建立和/或調整要求，而且其中一個會起始虛擬叢集調整大小，則在需要虛擬叢集調整大小的 5 + 分鐘後，已提交的所有要求會持續超過預期，因為這些要求必須等待調整大小才能完成，然後再繼續。

- **在5分鐘的時間範圍內提交的建立/調整作業**會以平行方式進行批次處理和執行。<br/>**範例：** 在5分鐘的時間範圍內提交的所有作業，只會執行一次調整大小的虛擬叢集（從執行第一個作業要求的那一刻開始測量）。 如果提交第一個要求的時間超過5分鐘，則會在執行開始之前，等待虛擬叢集調整大小完成。

> [!IMPORTANT]
> 因為有另一個進行中的作業要繼續進行，所以在符合條件之後，就會自動繼續進行管理。 不需要使用者採取任何動作，即可繼續暫時暫停的管理作業。

### <a name="canceling-management-operations"></a>取消管理作業

下表摘要說明取消特定管理作業的能力，以及一般的整體持續時間：

類別  |操作  |取消  |估計的取消持續時間  |
|---------|---------|---------|---------|
|部署 |實例建立 |No |  |
|更新 |實例儲存體相應增加/減少（一般用途） |No |  |
|更新 |實例儲存體相應增加/減少（業務關鍵） |Yes |90% 的作業會在5分鐘內完成。 |
|更新 |實例計算（虛擬核心）相應增加和減少（一般用途） |Yes |90% 的作業會在5分鐘內完成。 |
|更新 |實例計算（虛擬核心）相應增加和減少（業務關鍵） |Yes |90% 的作業會在5分鐘內完成。 |
|更新 |實例服務層級變更（一般用途到業務關鍵，反之亦然） |Yes |90% 的作業會在5分鐘內完成。 |
|刪除 |執行個體刪除 |No |  |
|刪除 |虛擬叢集刪除（作為使用者起始的作業） |No |  |

若要取消管理作業，請移至 [總覽] 分頁，然後按一下進行中操作的 [通知] 方塊。 從右側開始會顯示含有進行中作業的畫面，而且將會出現取消作業的按鈕。 第一次按一下之後，系統會要求您再次按一下，並確認您想要取消此操作。

[![取消作業](./media/sql-managed-instance-paas-overview/canceling-operation.png)](./media/sql-managed-instance-paas-overview/canceling-operation.png#lightbox)

提交並處理取消要求之後，如果取消提交成功，您就會收到通知。

如果取消成功，管理作業會在幾分鐘內取消，而導致失敗。

![正在取消操作結果](./media/sql-managed-instance-paas-overview/canceling-operation-result.png)

如果取消要求失敗，或 [取消] 按鈕未啟用，則表示管理作業已進入不可取消的狀態，而且會在幾分鐘內完成。 管理作業會繼續執行，直到完成為止。

> [!IMPORTANT]
> 目前只有在入口網站中才支援取消作業。

## <a name="advanced-security-and-compliance"></a>進階安全性與合規性

SQL 受控執行個體隨附 Azure 平臺和 SQL Server 資料庫引擎所提供的先進安全性功能。

### <a name="security-isolation"></a>安全性隔離：

SQL 受控執行個體與 Azure 平臺上的其他租使用者提供額外的安全性隔離。 安全性隔離包括：

- 使用 Azure ExpressRoute 或 VPN 閘道[執行原生虛擬網路](connectivity-architecture-overview.md)和內部部署環境的連線。
- 在預設部署中，SQL 端點只會透過私人 IP 位址公開，並允許來自私人 Azure 或混合式網路的安全連線。
- 單一租用戶具有專用的基礎結構 (計算、儲存體)。

下圖概述您應用程式的各種連線選項：

![高可用性](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

如需深入了解子網路層級的 VNet 整合和網路原則強制施行，請參閱[受控執行個體的 VNet 架構](connectivity-architecture-overview.md)和[將應用程式連線到受控執行個體](connect-application-instance.md)。

> [!IMPORTANT]
> 將多個受控實例放在相同的子網中，無論您的安全性需求是哪一種，都能帶來額外的好處。 相同子網中的共置實例會大幅簡化網路基礎結構維護，並減少實例布建時間，因為布建期間較長會與在子網中部署第一個受控實例的成本相關聯。

### <a name="security-features"></a>安全性功能

Azure SQL 受控執行個體提供一組先進的安全性功能，可用來保護您的資料。

- [SQL 受控執行個體審核](auditing-configure.md)會追蹤資料庫事件，並將它們寫入至您 Azure 儲存體帳戶中的 audit 記錄檔。 稽核可協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。
- 移動中的資料加密-SQL 受控執行個體藉由使用傳輸層安全性為移動中的資料提供加密，來保護您的資料。 除了傳輸層安全性，SQL 受控執行個體還提供在使用[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)保護進行中、待用和查詢處理期間的敏感性資料。 Always Encrypted 提供資料安全性來對抗重要資料遭竊的漏洞。 例如，透過 Always Encrypted，信用卡號碼會永遠加密儲存在資料庫中，即使在查詢處理期間，都允許需要處理該資料的已授權人員或應用程式在使用時解密。
- [先進的威脅防護](threat-detection-configure.md)提供一層內建于服務中的額外安全情報，以偵測存取或惡意探索資料庫的異常且可能有害的嘗試，藉此補充[審核](auditing-configure.md)程式。 系統會警示您有關可疑活動、潛在弱點、SQL 插入式攻擊和異常資料庫存取模式。 您可以從 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)檢視進階威脅保護警示。 它們會提供可疑活動的詳細資料，以及如何調查和緩和威脅的建議動作。  
- [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)可藉由遮罩處理，使不具權限的使用者無法看見敏感性資料。 動態資料遮罩可讓您在應用程式層級受到最小影響的情況下指定要顯示多少敏感性資料，而協助防止未經授權者存取敏感性資料。 這項原則式安全性功能會將敏感性資料隱藏在指定資料庫欄位的查詢結果集內，而資料庫中的資料則不會變更。
- 資料[列層級安全性](/sql/relational-databases/security/row-level-security)（RLS）可讓您根據執行查詢之使用者的特性（例如，依群組成員資格或執行內容），來控制對資料庫資料表中之資料列的存取。 RLS 簡化了應用程式中安全性的設計和編碼。 RLS 可讓您實作資料的資料列存取限制。 例如，確保背景工作角色只能存取其部門相關資料列，或將資料存取權限制為僅限相關資料。
- [透明資料加密（TDE）](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)會將 SQL 受控執行個體資料檔案加密，也稱為待用資料加密。 TDE 會執行資料和記錄檔的即時 I/O 加密和解密。 加密會使用資料庫加密金鑰 (DEK)，此金鑰會儲存在資料庫開機記錄中，以在復原期間提供可用性。 您可以使用透明資料加密來保護受控執行個體中的所有資料庫。 TDE 是在 SQL Server 中經過證實的待用加密技術，許多合規性標準所需，以防止儲存媒體遭竊。

透過 Azure 資料庫移轉服務或原生還原，可支援將加密的資料庫移轉至 SQL 受控執行個體。 如果您打算使用原生還原來遷移加密的資料庫，將現有的 TDE 憑證從 SQL Server 實例遷移至 SQL 受控執行個體是必要的步驟。 如需有關遷移選項的詳細資訊，請參閱[SQL Server 遷移至 SQL 受控執行個體](migrate-to-instance-from-sql-server.md)。

## <a name="azure-active-directory-integration"></a>Azure Active Directory 整合

SQL 受控執行個體支援傳統 SQL Server 的資料庫引擎登入以及與 Azure AD 整合的登入。 Azure AD 伺服器主體（登入）（**公開預覽**）是您在內部部署環境中使用的 Azure cloud 版本內部部署資料庫登入。 Azure AD 伺服器主體（登入）可讓您將 Azure AD 租使用者中的使用者和群組指定為真正實例範圍的主體，能夠執行任何實例層級的作業，包括在相同受控實例中的跨資料庫查詢。

引進了新的語法，可**從外部提供者**建立 Azure AD 伺服器主體（登入）。 如需語法的詳細資訊，請參閱<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">建立登</a>入和參閱為[SQL 受控執行個體提供 Azure Active Directory 系統管理員一](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)文。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 整合和多重要素驗證

SQL 受控執行個體可讓您透過[Azure Active Directory 整合](../database/authentication-aad-overview.md)，集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 這項功能簡化了權限管理並增強安全性。 Azure Active Directory 支援[多重要素驗證](../database/authentication-mfa-ssms-configure.md)，以增加資料和應用程式安全性，同時支援單一登入進程。

### <a name="authentication"></a>驗證

「SQL 受控執行個體驗證」指的是使用者連接到資料庫時，如何證明他們的身分識別。 SQL 受控執行個體支援兩種類型的驗證：  

- **SQL 驗證**：

  此驗證方法會使用使用者名稱和密碼。
- **Azure Active Directory 驗證**：

  此驗證方法會使用由 Azure Active Directory 管理的身分識別，並且受控網域和整合式網域都支援此驗證。 [盡可能](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)使用 Active Directory 驗證 (整合式安全性)。

### <a name="authorization"></a>授權

授權是指使用者可以在 Azure SQL 受控執行個體中的資料庫內執行的動作，而且是由使用者帳戶的資料庫角色成員資格和物件層級許可權所控制。 SQL 受控執行個體具有與 SQL Server 2017 相同的授權功能。

## <a name="database-migration"></a>資料庫移轉

SQL 受控執行個體將目標設為從內部部署或 IaaS 資料庫執行進行大量資料庫移轉的使用者案例。 SQL 受控執行個體支援數個資料庫移轉選項：

### <a name="backup-and-restore"></a>備份與還原  

移轉方法會利用 SQL 備份到 Azure Blob 儲存體。 您可以使用[T-SQL RESTORE 命令](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)，將儲存在 Azure 儲存體 blob 中的備份直接還原至受控實例。

- 如需示範如何還原 Wide World 匯入工具-標準資料庫備份檔案的快速入門，請參閱將[備份檔案還原至受控實例](restore-sample-database-quickstart.md)。 本快速入門說明您必須將備份檔案上傳至 Azure Blob 儲存體，並使用共用存取簽章（SAS）金鑰來保護它。
- 如需從 URL 還原的資訊，請參閱[從 URL 原生還原](migrate-to-instance-from-sql-server.md#native-restore-from-url)。

> [!IMPORTANT]
> 來自受控執行個體的備份只能還原至其他受控執行個體。 它們無法還原至 SQL Server 實例或 Azure SQL Database。

### <a name="database-migration-service"></a>Database Migration Service

Azure 資料庫移轉服務是完全受控的服務，其設計目的是要在停機時間最短的情況之下，從多個資料庫來源順暢地遷移至 Azure 資料平臺。 此服務可簡化將現有協力廠商和 SQL Server 資料庫移至 Azure VM 上 Azure SQL Database、Azure SQL 受控執行個體和 SQL Server 所需的工作。 請參閱[如何使用資料庫移轉服務，將您的內部部署資料庫移轉至 SQL 受控執行個體](https://aka.ms/migratetoMIusingDMS)。

## <a name="sql-features-supported"></a>SQL 功能支援

SQL 受控執行個體的目標是透過分段發行計畫，提供與最新 SQL Server 版本的接近100% 介面區相容性。 如需功能和比較清單，請參閱[sql 受控執行個體功能比較](../database/features-comparison.md)，如需 sql 受控執行個體與 SQL Server 的 t-sql 差異清單，請參閱[sql 受控執行個體 t-sql 與 SQL Server 之間的差異](transact-sql-tsql-differences-sql-server.md)。

SQL 受控執行個體支援 SQL Server 2008 資料庫的回溯相容性。 支援從 SQL Server 2005 資料庫伺服器直接遷移，而且遷移 SQL Server 2005 資料庫的相容性層級會更新為 SQL Server 2008。
  
下圖概述 SQL 受控執行個體中的介面區相容性：  

![移轉](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>SQL Server 內部部署和 SQL 受控執行個體之間的主要差異

SQL 受控執行個體的優點是在雲端中一律是最新狀態，這表示 SQL Server 中的某些功能可能已過時、已淘汰或有替代方案。 在某些情況下，工具需要辨識特定功能的運作方式稍有不同，或服務是在您未完全控制的環境中執行。

一些主要差異：

- 高可用性是內建的，並使用類似于[Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)的技術預先設定。
- 只有自動備份和時間點還原。 客戶可以起始 `copy-only` 不會干擾自動備份鏈的備份。
- 不支援指定完整實體路徑，因此必須以不同的方式支援所有對應的案例： RESTORE DB 不支援 WITH MOVE、CREATE DB 不允許實體路徑、BULK INSERT 僅適用于 Azure blob 等等。
- SQL 受控執行個體支援[Azure AD authentication](../database/authentication-aad-overview.md) ，做為 Windows 驗證的雲端替代方案。
- SQL 受控執行個體會自動為包含記憶體內部 OLTP 物件的資料庫管理 XTP 檔案群組和檔案。
- SQL 受控執行個體支援 SQL Server Integration Services （SSIS），而且可以裝載儲存 SSIS 封裝的 SSIS 目錄（SSISDB），但它們是在 Azure Data Factory 中的受控 Azure SSIS Integration Runtime （IR）上執行。 請參閱[在 Data Factory 中建立 AZURE SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 若要比較 SSIS 功能，請參閱[比較 SQL Database 與 SQL 受控執行個體](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。

### <a name="administration-features"></a>系統管理功能

SQL 受控執行個體可讓系統管理員在管理工作上花費較少的時間，因為這項服務會為您執行這些工作，或大幅簡化這些工作。 例如， [OS/RDBMS 安裝和修補](../database/high-availability-sla.md)、[動態實例的大小調整和](../database/single-database-scale.md)設定、[備份](../database/automated-backups-overview.md)、[資料庫](replication-between-two-instances-configure-tutorial.md)複寫（包括系統資料庫）、[高可用性](../database/high-availability-sla.md)設定，以及健康情況和[效能監視](../../azure-monitor/insights/azure-sql.md)資料流程的設定。

如需詳細資訊，請參閱[支援和不支援的 sql 受控執行個體功能清單](../database/features-comparison.md)，以及[sql 受控執行個體和 SQL Server 之間的 t-sql 差異](transact-sql-tsql-differences-sql-server.md)。

### <a name="programmatically-identify-a-managed-instance"></a>以程式設計方式識別受控實例

下表顯示數個可透過 Transact-sql 存取的屬性，可讓您用來偵測應用程式是否使用 SQL 受控執行個體並抓取重要的屬性。

|屬性|值|註解|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|此值與 SQL Database 中的相同。 這**不**表示 SQL 引擎第12版（SQL Server 2014）。 SQL 受控執行個體一律會執行最新的穩定 SQL 引擎版本，其等於或高於 SQL Server 的最新可用 RTM 版本。  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|此值與 SQL Database 中的相同。|
|`SERVERPROPERTY('EngineEdition')`|8|此值只會識別出受控執行個體。|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|下列格式的完整執行個體 DNS 名稱：`<instanceName>`.`<dnsPrefix>`.database.windows.net，其中 `<instanceName>` 是客戶提供的名稱，而 `<dnsPrefix>` 是自動產生的部分名稱，確保全域 DNS 名稱是唯一的 (例如，"wcus17662feb9ce98")|範例：my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立您的第一個受控實例，請參閱[快速入門手冊](instance-create-quickstart.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](../database/features-comparison.md)。
- 如需 VNet 設定的詳細資訊，請參閱[SQL 受控執行個體 vnet](connectivity-architecture-overview.md)設定。
- 如需建立受控實例並從備份檔案還原資料庫的快速入門，請參閱[建立受控實例](instance-create-quickstart.md)。
- 如需使用 Azure 資料庫移轉服務進行遷移的教學課程，請參閱[使用資料庫移轉服務的 SQL 受控執行個體遷移](../../dms/tutorial-sql-server-to-managed-instance.md)。
- 若要使用內建的疑難排解情報來進行 SQL 受控執行個體資料庫效能的先進監視，請參閱[使用 Azure SQL 分析監視 AZURE SQL 受控執行個體](../../azure-monitor/insights/azure-sql.md)。
- 如需定價資訊，請參閱[SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
