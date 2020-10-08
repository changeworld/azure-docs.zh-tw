---
title: 什麼是 Azure SQL 受控執行個體？
description: 了解 Azure SQL 受控執行個體如何與最新的 SQL Server (企業版) 資料庫引擎有接近 100% 的相容性
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: bonova
ms.author: bonova
ms.reviewer: sstein, vanto
ms.date: 08/14/2020
ms.openlocfilehash: c98e377ec216bea6c1d4a96b15b3741aa52672e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91618123"
---
# <a name="what-is-azure-sql-managed-instance"></a>什麼是 Azure SQL 受控執行個體？
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體是是可調整的智慧型雲端資料庫服務，結合了最廣泛的 SQL Server 資料庫引擎相容性，以及所有完全受控和常保最新狀態的平台即服務優點。 SQL 受控執行個體與最新的 SQL Server (企業版) 資料庫引擎有接近 100% 的相容性，並提供可因應常見安全性考量的原生[虛擬網路 (VNet)](../../virtual-network/virtual-networks-overview.md) 實作，以及有利於現有 SQL Server 客戶的[商務模型](https://azure.microsoft.com/pricing/details/sql-database/)。 SQL 受控執行個體可讓現有 SQL Server 客戶透過最少的應用程式和資料庫變更，將他們的內部部署應用程式隨即轉移至雲端。 同時，SQL 受控執行個體會保留 PaaS 的所有功能 (自動修補和版本的更新、[自訂備份](../database/automated-backups-overview.md)、[高可用性](../database/high-availability-sla.md))，可以大幅降低管理負擔和 TCO。

如果您不熟悉 Azure SQL 受控執行個體，請在我們深入說明的 [Azure SQL 影片系列](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)中查看 *Azure SQL 受控執行個體*影片：
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Managed-Instance-Overview-6-of-61/player]

> [!IMPORTANT]
> 如需目前可使用受控執行個體的區域清單，請參閱[支援的區域](resource-limits.md#supported-regions)。

下圖概述 SQL 受控執行個體的主要功能：

![主要功能](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL 受控執行個體專為以下客戶設計：想要盡可能輕鬆地將大量應用程式，從內部部署或 IaaS、自行建置或 ISV 提供的環境遷移至完全受控的 PaaS 雲端環境。 利用 Azure 中完全自動化的 [Azure 資料移轉服務](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)，客戶可以將現有 SQL Server 執行個體隨即移轉至 SQL 受控執行個體，受控執行個體可與 SQL Server 相容，並透過原生 VNet 支援來完全隔離客戶執行個體。  您可以透過軟體保證使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以折扣優惠在 SQL 受控執行個體上交換執行個體的現有授權。 對於需要高度安全性和程式設計介面豐富的 SQL Server 執行個體而言，SQL 受控執行個體是雲端中最佳的移轉目的地。

## <a name="key-features-and-capabilities"></a>重要功能

SQL 受控執行個體結合了可在 Azure SQL Database 和 SQL Server 資料庫引擎中取得的最佳功能。

> [!IMPORTANT]
> SQL 受控執行個體能執行 SQL Server 最新版本的所有功能，包括線上作業、自動計劃修正，以及其他企業效能增強功能。 如需可用功能的比較說明，請參閱[功能比較：Azure SQL 受控執行個體與 SQL Server](../database/features-comparison.md)。

| **PaaS 支援** | **業務持續性** |
| --- | --- |
|無須硬體採購和管理 <br>沒有管理基礎結構的管理負擔 <br>快速佈建和服務調整 <br>自動修補和版本升級 <br>與其他 PaaS 資料服務整合 |99.99% 的 SLA 運作時間  <br>內建[高可用性](../database/high-availability-sla.md) <br>使用[自動備份](../database/automated-backups-overview.md)保護資料 <br>客戶可設定的備份保留期限 <br>使用者起始的[備份](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current&preserve-view=true) <br>[資料庫還原時間點](../database/recovery-using-backups.md#point-in-time-restore)功能 |
|**安全性與合規性** | **管理**|
|隔離的環境 ([VNet 整合](connectivity-architecture-overview.md)、單一租用戶服務、專用的運算和儲存體) <br>[透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure Active Directory (Azure AD) 驗證](../database/authentication-aad-overview.md)、單一登入支援 <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD 伺服器主體 (登入)</a>  <br>與 Azure SQL Database 遵循相同的合規性標準 <br>[SQL 稽核](auditing-configure.md) <br>[進階威脅防護](threat-detection-configure.md) |用於自動化服務佈建與調整的 Azure Resource Manager API <br>用於手動服務佈建與調整的 Azure 入口網站功能 <br>資料移轉服務

> [!IMPORTANT]
> Azure SQL 受控執行個體已通過許多合規性標準的認證。 如需詳細資訊，請參閱 [Microsoft Azure 合規性供應項目](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers)，您可以在其中的 **SQL Database** 底下，找到最新的 SQL 受控執行個體合規性認證清單。

下表顯示 SQL 受控執行個體的主要功能：

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
| 內建的報告服務 (SSRS) | 否 - 改用 [Power BI 分頁報告](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi)或是在 Azure VM 上裝載 SSRS。 雖然 SQL 受控執行個體無法將 SSRS 當做服務執行，但是可以使用 SQL Server 驗證，為安裝在 Azure 虛擬機器上的報表伺服器裝載 [SSRS 目錄資料庫](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements)。 |
|||

## <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

SQL 受控執行個體中[以虛擬核心為基礎的購買模型](../database/service-tiers-vcore.md)提供彈性、可控制、透明及直接的方法，讓您將內部部署工作負載需求平移到雲端。 此模型可讓您根據工作負載需求，變更計算、記憶體和儲存體。 虛擬核心模型也能夠透過[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，最多節省 55% 的成本。

在虛擬核心模型中，您可以選擇各硬體世代。

- **Gen4** 邏輯 CPU 具備 Intel® E5-2673 v3 (Haswell) 2.4 GHz 處理器、附加 SSD、實體核心、每核心 7 GB RAM，以及介於 8 到 24 個虛擬核心的計算大小。
- **Gen5** 邏輯 CPU 具備 Intel® E5-2673 v4 (Broadwell) 2.3 GHz、Intel® SP-8160 (Skylake) 及 Intel® 8272CL (Cascade Lake) 2.5 GHz 處理器、快速 NVMe SSD、超執行緒邏輯核心，以及介於 4 到 80 個虛擬核心的計算大小。

在 [SQL 受控執行個體資源限制](resource-limits.md#hardware-generation-characteristics)中尋找關於硬體世代之間差異的詳細資訊。

## <a name="service-tiers"></a>服務層

有兩個服務層級可使用 SQL 受控執行個體：

- **一般用途**：專為具有標準效能和 I/O 延遲需求的應用程式所設計。
- **業務關鍵**：專為具有低 I/O 延遲需求且對工作負載的基礎維護作業影響最小的應用程式所設計。

這兩個服務層級均保證 99.99% 的可用性，可讓您單獨選取儲存體大小和計算容量。 如需 Azure SQL 受控執行個體高可用性架構的詳細資訊，請參閱[高可用性和 Azure SQL 受控執行個體](../database/high-availability-sla.md)。

### <a name="general-purpose-service-tier"></a>一般用途服務層級

下列清單說明一般用途服務層級的主要特色：

- 專為大多數有標準效能需求的商務應用程式所設計
- 高效能的 Azure Blob 儲存體 (8 TB)
- 根據可靠的 Azure Blob 儲存體和 [Azure Service Fabric](../../service-fabric/service-fabric-overview.md) 內建的[高可用性](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability)

如需詳細資訊，請參閱[一般用途層中的儲存體層](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c)和 [SQL 受控執行個體 (一般用途) 的儲存體效能最佳做法和考量](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/) (英文)。

在 [SQL 受控執行個體資源限制](resource-limits.md#service-tier-characteristics)中尋找關於服務層級之間差異的詳細資訊。

### <a name="business-critical-service-tier"></a>業務關鍵服務層級

業務關鍵服務層級是為具有高 I/O 需求的應用程式所建置。 其使用數個隔離的複本來提供最高失敗復原能力。

下列清單概述業務關鍵服務層級的主要特色：

- 專為具有極高效能和 HA 需求的商務應用程式所設計
- 提供超級快速的本機 SSD 儲存體 (在 Gen4 上可達 1 TB，而在 Gen5 上可達 4 TB)
- 根據 [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)和 [Azure Service Fabric](../../service-fabric/service-fabric-overview.md) 內建的[高可用性](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability)
- 可用於報告和其他唯讀工作負載的其他內建[唯讀資料庫複本](../database/read-scale-out.md)
- [記憶體內部 OLTP](../in-memory-oltp-overview.md)，可用於具有高效能需求的工作負載  

在[受控執行個體資源限制](resource-limits.md#service-tier-characteristics)中尋找關於服務層級之間差異的詳細資訊。

## <a name="management-operations"></a>管理作業

Azure SQL 受控執行個體提供管理作業，可讓您在不再需要時，用來自動部署新的受控執行個體、更新執行個體屬性和刪除執行個體。 如需管理作業的詳細說明，請參閱＜[受控執行個體管理作業概觀](management-operations-overview.md)＞頁面。

## <a name="advanced-security-and-compliance"></a>進階安全性與合規性

SQL 受控執行個體隨附 Azure 平台和 SQL Server 資料庫引擎所提供的進階安全性功能。

### <a name="security-isolation"></a>安全性隔離

SQL 受控執行個體提供額外的安全性隔離，可與 Azure 平台中的其他租用戶隔離。 安全性隔離包括：

- [實作原生虛擬網路](connectivity-architecture-overview.md)和使用 Azure ExpressRoute 或 VPN 閘道與內部部署環境連線。
- 在預設部署中，SQL 端點只會透過私人 IP 位址公開，並允許來自私人 Azure 或混合式網路的安全連線。
- 單一租用戶具有專用的基礎結構 (計算、儲存體)。

下圖概述您應用程式的各種連線選項：

![高可用性](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

如需深入了解子網路層級的 VNet 整合和網路原則強制施行，請參閱[受控執行個體的 VNet 架構](connectivity-architecture-overview.md)和[將應用程式連線到受控執行個體](connect-application-instance.md)。

> [!IMPORTANT]
> 將多個受控執行個體放在相同子網路中 (如果您的安全性需求允許的話)，因為這會帶來額外的好處。 將執行個體放在相同子網路中，可大幅簡化網路基礎結構的維護工作，並且可減少執行個體的佈建時間，因為長時間的佈建期間與在子網路中部署第一個受控執行個體的成本有關。

### <a name="security-features"></a>安全性功能

Azure SQL 受控執行個體提供一組可用來保護資料的進階安全性功能。

- [SQL 受控執行個體稽核](auditing-configure.md)會追蹤資料庫事件並將事件寫入您 Azure 儲存體帳戶中的稽核記錄檔。 稽核可協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。
- 移動中資料加密 - SQL 受控執行個體會使用傳輸層安全性對移動中的資料加密，藉此保護您的資料。 除了傳輸層安全性，SQL 受控執行個體會使用 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 來保護傳輸中、待用和查詢處理期間的敏感性資料。 Always Encrypted 會提供資料安全性來對抗重要資料遭竊的漏洞。 例如，透過 Always Encrypted，信用卡號碼會永遠加密儲存在資料庫中，即使在查詢處理期間，都允許需要處理該資料的已授權人員或應用程式在使用時解密。
- [進階威脅防護](threat-detection-configure.md)會提供服務內建的額外安全情報層，此情報層可偵測到不尋常且有危害的資料庫存取或攻擊動作，藉此補充[稽核](auditing-configure.md)的不足之處。 系統會警示您有關可疑活動、潛在弱點、SQL 插入式攻擊和異常資料庫存取模式。 您可以從 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)檢視進階威脅保護警示。 其提供可疑活動的詳細資料，以及如何調查與降低威脅的建議。  
- [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)可藉由遮罩處理，使不具權限的使用者無法看見敏感性資料。 動態資料遮罩可讓您在應用程式層級受到最小影響的情況下指定要顯示多少敏感性資料，而協助防止未經授權者存取敏感性資料。 這項原則式安全性功能會將敏感性資料隱藏在指定資料庫欄位的查詢結果集內，而資料庫中的資料則不會變更。
- [資料列層級安全性](/sql/relational-databases/security/row-level-security) (RLS) 讓您能夠根據執行查詢之使用者的特性 (例如，依群組成員資格或執行內容) 來控制資料庫資料表中的資料列存取。 RLS 可簡化應用程式中安全性的設計和編碼。 RLS 可讓您實作資料的資料列存取限制。 例如，確保背景工作角色只能存取其部門相關資料列，或將資料存取權限制為僅限相關資料。
- [透明資料加密 (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 會將 SQL 受控執行個體的資料檔案加密，也稱為「待用資料加密」。 TDE 會執行資料和記錄檔的即時 I/O 加密和解密。 加密會使用資料庫加密金鑰 (DEK)，此金鑰會儲存在資料庫開機記錄中，以在復原期間提供可用性。 您可以使用透明資料加密來保護受控執行個體中的所有資料庫。 TDE 是 SQL Server 中經實證的待用加密技術，許多合規性標準都需要這項技術才能防禦儲存媒體的竊取。

透過 Azure 資料庫移轉服務或原生還原，可支援將加密的資料庫遷移到 SQL 受控執行個體。 如果您打算使用原生還原來遷移加密的資料庫，必須執行將現有 TDE 憑證從 SQL Server 執行個體遷移至 SQL 受控執行個體的步驟。 如需移轉選項的詳細資訊，請參閱[將 SQL Server 遷移至 SQL 受控執行個體](migrate-to-instance-from-sql-server.md)。

## <a name="azure-active-directory-integration"></a>Azure Active Directory 整合

SQL 受控執行個體支援傳統的 SQL Server 資料庫引擎登入以及與 Azure AD 整合的登入。 Azure AD 伺服器主體(登入) (**公開預覽**) 是您使用於內部部署環境的 Azure 雲端版內部部署資料庫登入。 Azure AD 伺服器主體 (登入) 可讓您從 Azure AD 租用戶指定使用者和群組作為實際執行個體範圍的主體，能夠執行任何執行個體層級的作業，包括在相同受控執行個體中的跨資料庫查詢。

為了建立 Azure AD 伺服器主體 (登入)，引進了新的語法 **FROM EXTERNAL PROVIDER**。 如需有關語法的詳細資訊，請參閱 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>，並檢閱[為 SQL 受控執行個體佈建 Azure Active Directory 系統管理員](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)文章。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 整合和多重要素驗證

SQL 受控執行個體可讓您透過 [Azure Active Directory 整合](../database/authentication-aad-overview.md)，集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 這項功能簡化了權限管理並增強安全性。 Azure Active Directory 支援[多重要素驗證](../database/authentication-mfa-ssms-configure.md)，以提高資料和應用程式安全性，同時支援單一登入程序。

### <a name="authentication"></a>驗證

SQL 受控執行個體驗證是指使用者連線到資料庫時如何證明他們的身分識別。 SQL 受控執行個體支援兩種類型的驗證：  

- **SQL 驗證**：

  此驗證方法會使用使用者名稱和密碼。
- **Azure Active Directory 驗證**：

  此驗證方法會使用由 Azure Active Directory 管理的身分識別，並且受控網域和整合式網域都支援此驗證。 [盡可能](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)使用 Active Directory 驗證 (整合式安全性)。

### <a name="authorization"></a>授權

授權是指使用者可以在 Azure SQL 受控執行個體的資料庫中執哪些動作，這是由使用者帳戶的資料庫角色成員資格和物件層級權限所控制。 SQL 受控執行個體與 SQL Server 2017 具有相同的授權功能。

## <a name="database-migration"></a>資料庫移轉

SQL 受控執行個體鎖定的是將大量資料庫從內部部署或 IaaS 資料庫實作移轉的使用者案例。 SQL 受控執行個體支援數個資料庫移轉選項：

### <a name="backup-and-restore"></a>備份與還原  

移轉方法會利用 SQL 備份到 Azure Blob 儲存體。 透過 [T-SQL RESTORE 命令](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current&preserve-view=true)，儲存在 Azure 儲存體 Blob 的備份可以直接用來還原到受控執行個體。

- 如需示範如何還原 Wide World Importers - 標準資料庫備份檔案的快速入門，請參閱[還原備份檔案至受控執行個體](restore-sample-database-quickstart.md)。 本快速入門顯示，您必須將備份檔案上傳到 Azure Blob 儲存體，並使用共用存取簽章 (SAS) 金鑰保護其安全。
- 如需從 URL 還原的資訊，請參閱[從 URL 原生還原](migrate-to-instance-from-sql-server.md#native-restore-from-url)。

> [!IMPORTANT]
> 來自受控執行個體的備份只能還原至其他受控執行個體。 其無法還原至 SQL Server 執行個體或 Azure SQL Database。

### <a name="database-migration-service"></a>Database Migration Service

Azure 資料庫移轉服務是一個完全受控的服務，能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低。 此服務可簡化將現有第三方和 SQL Server 資料庫移至 Azure SQL Database、Azure SQL 受控執行個體與 Azure VM 中的 SQL Server 所需的工作。 請參閱[如何使用資料庫移轉服務將內部部署資料庫遷移至 SQL 受控執行個體](https://aka.ms/migratetoMIusingDMS)。

## <a name="sql-features-supported"></a>SQL 功能支援

SQL 受控執行個體的目標是透過階段式發行計劃，為最新版 SQL Server 提供幾乎 100% 的介面區相容性。 如需功能和比較清單，請參閱 [SQL 受控執行個體功能比較](../database/features-comparison.md)，而如需 SQL 受控執行個體與 SQL Server 的 T-SQL 差異清單，請參閱 [SQL受控執行個體與 SQL Server 的 T-SQL 差異](transact-sql-tsql-differences-sql-server.md)。

SQL 受控執行個體支援與 SQL Server 2008 資料庫的回溯相容性。 支援從 SQL Server 2005 資料庫伺服器進行直接移轉，移轉後，SQL Server 2005 資料庫的相容性層級會更新為 SQL Server 2008。
  
下圖概述 SQL 受控執行個體中的介面區相容性：  

![介面區相容性](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>SQL Server 內部部署和 SQL 受控執行個體之間的主要差異

SQL 受控執行個體的優勢是其在雲端中一律是最新狀態，這表示 SQL Server 中的某些功能可能已過時、已停用或已有替代方案。 在某些情況，當工具必須辨識特定功能的運作方式稍有不同，或是服務在某個環境中執行時，您無法完全控制。

一些主要差異：

- 高可用性會使用類似 [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)的技術來內建及預先設定。
- 只有自動備份和時間點還原。 客戶可以起始 `copy-only` 備份，這不會干擾自動備份鏈結。
- 不支援指定完整實體路徑，因此必須以不同方式支援所有對應的案例：RESTORE DB 不支援 WITH MOVE、CREATE DB 不允許實體路徑、BULK INSERT 僅適用於 Azure Blob 等等。
- SQL 受控執行個體支援以 [Azure AD 驗證](../database/authentication-aad-overview.md) 作為 Windows 驗證的雲端替代方案。
- SQL 受控執行個體都會自動為包含記憶體內部 OLTP 物件的資料庫管理 XTP 檔案群組和檔案。
- SQL 受控執行個體支援 SQL Server Integration Services (SSIS)，且可主控儲存 SSIS 套件的 SSIS 目錄 (SSISDB)，但會在 Azure Data Factory (ADF) 中的受控 Azure-SSIS Integration Runtime (IR) 上執行。 請參閱[在 Data Factory 中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 若要比較 SSIS 功能，請參閱[比較 SQL Database 與 SQL 受控執行個體](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。

### <a name="administration-features"></a>系統管理功能

SQL 受控執行個體可讓系統管理員花較少的時間處理系統管理工作，因為服務會為您執行這些設定，或大幅簡化這些工作。 例如，[OS/RDBMS 安裝和修補](../database/high-availability-sla.md)、[動態執行個體的大小調整和設定](../database/single-database-scale.md)、[備份](../database/automated-backups-overview.md)、[資料庫複寫](replication-between-two-instances-configure-tutorial.md) (包括系統資料庫)、[高可用性設定](../database/high-availability-sla.md)，以及健康情況和[效能監](../../azure-monitor/insights/azure-sql.md)視資料流的設定。

如需詳細資訊，請參閱[支援和不支援的 SQL 受控執行個體功能清單](../database/features-comparison.md)，以及 [SQL 受控執行個體和 SQL Server 之間的 T-SQL 差異](transact-sql-tsql-differences-sql-server.md)。

### <a name="programmatically-identify-a-managed-instance"></a>以程式設計方式識別受控執行個體

下表顯示數個透過 Transact SQL 使用的屬性，可用來檢測出應用程式正在使用 SQL 受控執行個體，並擷取重要的屬性。

|屬性|值|註解|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|此值與 SQL Database 中的相同。 這**並非**表示 SQL 引擎第 12 版 (SQL Server 2014)。 SQL 受控執行個體一律會執行最新穩定 SQL 引擎版本，其等於或高於 SQL Server 的最新可用 RTM 版本。  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|此值與 SQL Database 中的相同。|
|`SERVERPROPERTY('EngineEdition')`|8|此值只會識別出受控執行個體。|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|下列格式的完整執行個體 DNS 名稱：`<instanceName>`.`<dnsPrefix>`.database.windows.net，其中 `<instanceName>` 是客戶提供的名稱，而 `<dnsPrefix>` 是自動產生的部分名稱，確保全域 DNS 名稱是唯一的 (例如，"wcus17662feb9ce98")|範例：my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>後續步驟

- 若要了解如何建立您的第一個受控執行個體，請參閱[快速入門指南](instance-create-quickstart.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](../database/features-comparison.md)。
- 如需 VNet 組態的詳細資訊，請參閱 [SQL 受控執行個體 VNet 組態](connectivity-architecture-overview.md)。
- 如需建立受控執行個體，並從備份檔案還原資料庫的快速入門，請參閱[建立受控執行個體](instance-create-quickstart.md)。
- 如需使用 Azure 資料庫移轉服務進行移轉的教學課程，請參閱[使用資料庫移轉服務的 SQL 受控執行個體移轉](../../dms/tutorial-sql-server-to-managed-instance.md)。
- 若要使用內建的疑難排解智慧對 SQL 受控執行個體的資料庫效能進行進階監視，請參閱[使用 Azure SQL 分析監視 Azure SQL 受控執行個體](../../azure-monitor/insights/azure-sql.md)。
- 如需價格資訊，請參閱 [SQL Database 價格](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
