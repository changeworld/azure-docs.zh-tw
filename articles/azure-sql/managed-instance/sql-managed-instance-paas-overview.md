---
title: 什麼是 Azure SQL 受控執行個體？
description: 瞭解 Azure SQL 受控執行個體如何與最新的 SQL Server (Enterprise Edition) database engine 提供接近100% 的相容性
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, vanto
ms.date: 08/14/2020
ms.openlocfilehash: ce86f4e6ed5f29be3e36959e0f9db76edaab4982
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273019"
---
# <a name="what-is-azure-sql-managed-instance"></a>什麼是 Azure SQL 受控執行個體？
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體是智慧型、可擴充的雲端資料庫服務，結合了最廣泛的 SQL Server 資料庫引擎相容性，以及完全受控和長平臺即服務的所有優點。 SQL 受控執行個體與最新的 SQL Server (Enterprise Edition) database engine 之間有接近100% 的相容性，提供原生 [虛擬網路 (VNet) ](../../virtual-network/virtual-networks-overview.md) 實作為解決常見的安全性考慮，以及對現有 SQL Server 客戶有利的 [商業模型](https://azure.microsoft.com/pricing/details/sql-database/) 。 SQL 受控執行個體可讓現有的 SQL Server 客戶將其內部部署應用程式隨即轉移至雲端，並進行最少量的應用程式和資料庫變更。 同時，SQL 受控執行個體會保留所有 PaaS 功能 (自動修補和版本更新、 [自動備份](../database/automated-backups-overview.md)、 [高可用性](../database/high-availability-sla.md)) ，以大幅降低管理負擔和 TCO。

如果您不熟悉 Azure SQL 受控執行個體，請參閱 Azure sql *受控執行個體* 影片，深入瞭解 azure sql [影片系列](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)：
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Managed-Instance-Overview-6-of-61/player]

> [!IMPORTANT]
> 如需目前可使用 SQL 受控執行個體的區域清單，請參閱 [支援的區域](resource-limits.md#supported-regions)。

下圖概述 SQL 受控執行個體的主要功能：

![主要功能](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL 受控執行個體是專為想要將大量應用程式從內部部署或 IaaS、自行建立或 ISV 提供的環境遷移至完全受控的 PaaS 雲端環境的客戶所設計，並盡可能減少遷移工作。 客戶可以使用完全自動化的 [Azure 資料移轉服務](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)，將其現有的 SQL Server 實例隨即轉移至 SQL 受控執行個體，以提供與 SQL Server 的相容性，並透過原生 VNet 支援完成客戶實例的隔離。  使用軟體保證，您可以使用 [SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，在 SQL 受控執行個體上以折扣費率交換現有授權。 在雲端中，SQL 受控執行個體是最適合需要高安全性的 SQL Server 實例，以及豐富的可程式性介面的最佳遷移目的地。

## <a name="key-features-and-capabilities"></a>重要功能

SQL 受控執行個體結合了 Azure SQL Database 和 SQL Server Database engine 中提供的最佳功能。

> [!IMPORTANT]
> SQL 受控執行個體會以最新版本的 SQL Server 的所有功能來執行，包括線上作業、自動計畫修正，以及其他企業效能增強功能。 功能比較中會說明可用功能的比較 [： AZURE SQL 受控執行個體與 SQL Server](../database/features-comparison.md)。

| **PaaS 支援** | **業務持續性** |
| --- | --- |
|無須硬體採購和管理 <br>沒有管理基礎結構的管理負擔 <br>快速佈建和服務調整 <br>自動修補和版本升級 <br>與其他 PaaS 資料服務整合 |99.99% 的 SLA 運作時間  <br>內建 [高可用性](../database/high-availability-sla.md) <br>使用[自動備份](../database/automated-backups-overview.md)保護資料 <br>客戶可設定的備份保留期限 <br>使用者起始的[備份](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current&preserve-view=true) <br>[時間點資料庫還原](../database/recovery-using-backups.md#point-in-time-restore) 功能 |
|**安全性與合規性** | **管理**|
|隔離的環境 ([VNet 整合](connectivity-architecture-overview.md)、單一租用戶服務、專用的運算和儲存體) <br>[透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure Active Directory (Azure AD) 驗證](../database/authentication-aad-overview.md)、單一登入支援 <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD 伺服器主體 (登入) </a>  <br>遵循與 Azure SQL Database 相同的合規性標準 <br>[SQL 審核](auditing-configure.md) <br>[進階威脅防護](threat-detection-configure.md) |用於自動化服務佈建與調整的 Azure Resource Manager API <br>用於手動服務佈建與調整的 Azure 入口網站功能 <br>資料移轉服務

> [!IMPORTANT]
> Azure SQL 受控執行個體已經過許多合規性標準的認證。 如需詳細資訊，請參閱 [Microsoft Azure 合規性供應](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers)專案，您可以在其中找到 **SQL Database**下所列的最新 SQL 受控執行個體合規性認證清單。

下表顯示 SQL 受控執行個體的主要功能：

|功能 | 說明|
|---|---|
| SQL Server 版本/組建 | SQL Server database engine (最新的穩定)  |
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
| 內建的報告服務 (SSRS) | 不使用 [Power BI 編頁報表](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) ，或在 Azure VM 上裝載 SSRS。 雖然 SQL 受控執行個體無法以服務的形式執行 SSRS，但它可以使用 SQL Server 驗證，為安裝在 Azure 虛擬機器上的報表伺服器裝載 [ssrs 類別目錄資料庫](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) 。 |
|||

## <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

適用于 SQL 受控執行個體的 [vCore 為基礎的購買模型](../database/service-tiers-vcore.md) 提供彈性、控制、透明及直接的方式，將內部部署工作負載需求轉譯至雲端。 此模型可讓您根據工作負載需求，變更計算、記憶體和儲存體。 VCore 模型也符合 SQL Server 的 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) ，最多可省下55% 的費用。

在 vCore 模型中，您可以在硬體世代之間進行選擇。

- **第4代** 邏輯 cpu 是以 Intel® E5-2673 V3 (Haswell) 2.4 GHz 處理器、附加 SSD、實體核心、每個核心 7 GB RAM，以及介於8到24虛擬核心之間的計算大小。
- **第5代** 邏輯 cpu 是以 Intel® E5-2673 V4 (Broadwell) 2.3 GHz、INTEL® SP-8160 (Skylake) 和 INTEL® 8272CL (Cascade Lake) 2.5 GHz 處理器、快速 NVMe SSD、超執行緒邏輯核心，以及4和80核心之間的計算大小。

請在 [SQL 受控執行個體資源限制](resource-limits.md#hardware-generation-characteristics)中尋找硬體世代之間差異的詳細資訊。

## <a name="service-tiers"></a>服務層

SQL 受控執行個體有兩個服務層級：

- **一般用途**：專為具有一般效能和 i/o 延遲需求的應用程式所設計。
- **商務關鍵性**：專為具有低 i/o 延遲需求的應用程式而設計，且對工作負載的基礎維護作業影響最小。

這兩個服務層級均保證 99.99% 的可用性，可讓您單獨選取儲存體大小和計算容量。 如需 Azure SQL 受控執行個體高可用性架構的詳細資訊，請參閱 [高可用性和 AZURE sql 受控執行個體](../database/high-availability-sla.md)。

### <a name="general-purpose-service-tier"></a>一般用途服務層級

下列清單說明一般用途服務層級的主要特性：

- 專為大部分具有一般效能需求的商務應用程式所設計
- 高效能的 Azure Blob 儲存體 (8 TB)
- 根據可靠的 Azure Blob 儲存體和[Azure Service Fabric](../../service-fabric/service-fabric-overview.md)內建的[高可用性](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability)

如需詳細資訊，請參閱 [一般用途層中的儲存層](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) ，以及 [SQL 受控執行個體 (一般用途) 的儲存體效能最佳做法和考慮 ](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)。

尋找 [SQL 受控執行個體資源限制](resource-limits.md#service-tier-characteristics)中服務層之間差異的詳細資訊。

### <a name="business-critical-service-tier"></a>業務關鍵服務層級

商務關鍵服務層級是針對具有高 i/o 需求的應用程式所建立。 它使用數個隔離的複本，為失敗提供最高的復原能力。

下列清單概述業務關鍵服務層級的主要特色：

- 專為具有極高效能和 HA 需求的商務應用程式所設計
- 提供超級快速的本機 SSD 儲存體 (在 Gen4 上可達 1 TB，而在 Gen5 上可達 4 TB)
- 根據[Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)和[Azure Service Fabric](../../service-fabric/service-fabric-overview.md)內建的[高可用性](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability)
- 內建的其他 [唯讀資料庫複本](../database/read-scale-out.md) ，可用於報告和其他唯讀工作負載
- [記憶體內部 OLTP](../in-memory-oltp-overview.md)，可用於具有高效能需求的工作負載  

尋找 [SQL 受控執行個體資源限制](resource-limits.md#service-tier-characteristics)中服務層之間差異的詳細資訊。

## <a name="management-operations"></a>管理作業

Azure SQL 受控執行個體提供管理作業，可讓您在不再需要時，用來自動部署新的受控實例、更新實例屬性和刪除實例。 如需管理作業的詳細說明，請參閱 [受控實例管理操作總覽](management-operations-overview.md) 頁面。

## <a name="advanced-security-and-compliance"></a>進階安全性與合規性

SQL 受控執行個體隨附 Azure 平臺和 SQL Server database engine 提供的先進安全性功能。

### <a name="security-isolation"></a>安全性隔離

SQL 受控執行個體可為 Azure 平臺上的其他租使用者提供額外的安全性隔離。 安全性隔離包括：

- 使用 Azure ExpressRoute 或 VPN 閘道的[原生虛擬網路](connectivity-architecture-overview.md)，以及內部部署環境的連線能力。
- 在預設部署中，SQL 端點只會透過私人 IP 位址公開，並允許來自私人 Azure 或混合式網路的安全連線。
- 單一租用戶具有專用的基礎結構 (計算、儲存體)。

下圖概述您應用程式的各種連線選項：

![高可用性](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

如需深入了解子網路層級的 VNet 整合和網路原則強制施行，請參閱[受控執行個體的 VNet 架構](connectivity-architecture-overview.md)和[將應用程式連線到受控執行個體](connect-application-instance.md)。

> [!IMPORTANT]
> 將多個受管理的實例放在相同的子網中，不論您的安全性需求所允許的位置，這會讓您有額外的好處。 將實例共置在相同的子網中，可大幅簡化網路基礎結構維護及減少實例布建時間，因為長期布建期間與在子網中部署第一個受控實例的成本相關。

### <a name="security-features"></a>安全性功能

Azure SQL 受控執行個體提供一組可以用來保護資料的先進安全性功能。

- [SQL 受控執行個體審核](auditing-configure.md) 會追蹤資料庫事件，並將其寫入 Azure 儲存體帳戶中所放置的 audit 記錄檔。 稽核可協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。
- 移動中的資料加密-SQL 受控執行個體藉由使用傳輸層安全性為移動中的資料提供加密，來保護您的資料。 除了傳輸層安全性之外，SQL 受控執行個體還提供使用 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)來保護執行中、待用和查詢處理期間的敏感性資料。 Always Encrypted 針對涉及重要資料遭竊的漏洞提供資料安全性。 例如，透過 Always Encrypted，信用卡號碼會永遠加密儲存在資料庫中，即使在查詢處理期間，都允許需要處理該資料的已授權人員或應用程式在使用時解密。
- [先進的威脅防護](threat-detection-configure.md) 藉由提供服務內建的額外安全情報層，來偵測存取或惡意探索資料庫的不尋常且可能有害的嘗試，藉此補充 [審核](auditing-configure.md) 。 系統會警示您有關可疑活動、潛在弱點、SQL 插入式攻擊和異常資料庫存取模式。 您可以從 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)檢視進階威脅保護警示。 它們提供可疑活動的詳細資料，以及如何調查和緩和威脅的建議動作。  
- [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)可藉由遮罩處理，使不具權限的使用者無法看見敏感性資料。 動態資料遮罩可讓您在應用程式層級受到最小影響的情況下指定要顯示多少敏感性資料，而協助防止未經授權者存取敏感性資料。 這項原則式安全性功能會將敏感性資料隱藏在指定資料庫欄位的查詢結果集內，而資料庫中的資料則不會變更。
- 資料[列層級安全性](/sql/relational-databases/security/row-level-security) (RLS) 可讓您根據執行 (查詢的使用者特性，例如依群組成員資格或執行內容) ，來控制資料庫資料表中資料列的存取權。 RLS 可簡化應用程式中安全性的設計和編碼。 RLS 可讓您實作資料的資料列存取限制。 例如，確保背景工作角色只能存取其部門相關資料列，或將資料存取權限制為僅限相關資料。
- [透明資料加密 (TDE) ](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 會將 SQL 受控執行個體資料檔案加密，也稱為「待用資料加密」。 TDE 會執行資料和記錄檔的即時 I/O 加密和解密。 加密會使用資料庫加密金鑰 (DEK)，此金鑰會儲存在資料庫開機記錄中，以在復原期間提供可用性。 您可以使用透明資料加密來保護受控執行個體中的所有資料庫。 TDE 是在 SQL Server 中經過證實的待用加密技術，可防範許多合規性標準來防止儲存體媒體遭竊。

透過 Azure 資料庫移轉服務或原生還原，可支援將加密的資料庫移轉至 SQL 受控執行個體。 如果您打算使用原生還原來遷移加密的資料庫，將現有的 TDE 憑證從 SQL Server 實例遷移至 SQL 受控執行個體是必要的步驟。 如需有關遷移選項的詳細資訊，請參閱 [SQL Server 遷移至 SQL 受控執行個體](migrate-to-instance-from-sql-server.md)。

## <a name="azure-active-directory-integration"></a>Azure Active Directory 整合

SQL 受控執行個體支援與 Azure AD 整合的傳統 SQL Server database engine 登入和登入。 Azure AD 伺服器主體 (登入)  (**公開預覽**) 是您在內部部署環境中使用的 Azure cloud 版本內部部署資料庫登入。 Azure AD 伺服器主體 (登入) 可讓您將 Azure AD 租使用者中的使用者和群組指定為真正實例範圍的主體，以執行任何實例層級的作業，包括在相同受控實例中的跨資料庫查詢。

引進了新的語法，可 **從外部提供者**建立 Azure AD 伺服器主體 (登入) ）。 如需語法的詳細資訊，請參閱 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">建立登</a>入，並參閱為 [SQL 提供 Azure Active Directory 管理員受控執行個體](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) 一文。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 整合和多重要素驗證

SQL 受控執行個體可讓您使用 [Azure Active Directory 整合](../database/authentication-aad-overview.md)，集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 這項功能簡化了權限管理並增強安全性。 Azure Active Directory 支援 [多重要素驗證](../database/authentication-mfa-ssms-configure.md) ，以增加資料和應用程式安全性，同時支援單一登入精靈。

### <a name="authentication"></a>驗證

SQL 受控執行個體 authentication 指的是使用者連接到資料庫時，如何證明他們的身分識別。 SQL 受控執行個體支援兩種類型的驗證：  

- **SQL 驗證**：

  此驗證方法會使用使用者名稱和密碼。
- **Azure Active Directory 驗證**：

  此驗證方法會使用由 Azure Active Directory 管理的身分識別，並且受控網域和整合式網域都支援此驗證。 [盡可能](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)使用 Active Directory 驗證 (整合式安全性)。

### <a name="authorization"></a>授權

授權是指使用者可以在 Azure SQL 受控執行個體中的資料庫內進行的作業，而且是由使用者帳戶的資料庫角色成員資格和物件層級許可權所控制。 SQL 受控執行個體有與 SQL Server 2017 相同的授權功能。

## <a name="database-migration"></a>資料庫移轉

SQL 受控執行個體會以使用者案例為目標，並從內部部署或 IaaS 資料庫的執行中進行大量資料庫移轉。 SQL 受控執行個體支援數個資料庫移轉選項：

### <a name="backup-and-restore"></a>備份與還原  

移轉方法會利用 SQL 備份到 Azure Blob 儲存體。 您可以使用 [T-SQL RESTORE 命令](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current&preserve-view=true)，將儲存在 Azure 儲存體 blob 中的備份直接還原到受控實例。

- 如需示範如何還原 Wide World 匯入工具-標準資料庫備份檔案的快速入門，請參閱將 [備份檔案還原到受控實例](restore-sample-database-quickstart.md)。 本快速入門說明您必須將備份檔案上傳至 Azure Blob 儲存體，並使用 (SAS) 金鑰的共用存取簽章來保護它。
- 如需從 URL 還原的資訊，請參閱[從 URL 原生還原](migrate-to-instance-from-sql-server.md#native-restore-from-url)。

> [!IMPORTANT]
> 來自受控執行個體的備份只能還原至其他受控執行個體。 它們無法還原至 SQL Server 實例或 Azure SQL Database。

### <a name="database-migration-service"></a>Database Migration Service

Azure 資料庫移轉服務是一個完全受控的服務，能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低。 此服務可簡化將現有協力廠商和 SQL Server 資料庫移至 Azure VM 上 Azure SQL Database、Azure SQL 受控執行個體和 SQL Server 所需的工作。 瞭解 [如何使用資料庫移轉服務，將您的內部部署資料庫移轉至 SQL 受控執行個體](https://aka.ms/migratetoMIusingDMS)。

## <a name="sql-features-supported"></a>SQL 功能支援

SQL 受控執行個體的目標是透過階段式發行計畫，為最新的 SQL Server 版本提供接近100% 的介面區相容性。 如需功能和比較清單，請參閱 [sql 受控執行個體功能比較](../database/features-comparison.md)，以及 sql 受控執行個體與 SQL Server 的 t-sql 差異清單，請參閱 [sql 受控執行個體 t-sql 與 SQL Server 之間的差異](transact-sql-tsql-differences-sql-server.md)。

SQL 受控執行個體支援 SQL Server 2008 資料庫的回溯相容性。 支援從 SQL Server 2005 資料庫伺服器直接遷移，而遷移 SQL Server 2005 資料庫的相容性層級會更新為 SQL Server 2008。
  
下圖概述 SQL 受控執行個體中的介面區相容性：  

![介面區相容性](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>SQL Server 內部部署與 SQL 受控執行個體之間的主要差異

SQL 受控執行個體的優點是在雲端中一律是最新狀態，這表示 SQL Server 中的某些功能可能已過時、已淘汰或有替代方案。 在某些情況下，工具需要辨識特定功能的運作方式稍有不同，或是服務是在您無法完全控制的環境中執行。

一些主要差異：

- 高可用性是內建的，而且是使用類似于 [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)的技術預先設定的。
- 只有自動備份和時間點還原。 客戶可以起始 `copy-only` 不幹擾自動備份鏈的備份。
- 不支援指定完整的實體路徑，因此必須以不同方式支援所有對應的案例： RESTORE DB 不支援 MOVE、CREATE DB 不允許實體路徑、BULK INSERT 僅適用于 Azure blob 等等。
- SQL 受控執行個體支援 [Azure AD 驗證](../database/authentication-aad-overview.md) ，作為 Windows 驗證的雲端替代方案。
- SQL 受控執行個體會自動為包含記憶體內部 OLTP 物件的資料庫管理 XTP 檔案群組和檔案。
- SQL 受控執行個體支援 SQL Server Integration Services (SSIS) ，而且可以裝載儲存 SSIS 套件 (SSISDB) 的 SSIS 目錄，但它們是在 Azure-SSIS Integration Runtime 中的受控 () IR Azure Data Factory 上執行。 請參閱 [Data Factory 中的建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 若要比較 SSIS 功能，請參閱 [比較 SQL Database 與 SQL 受控執行個體](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。

### <a name="administration-features"></a>管理功能

SQL 受控執行個體可讓系統管理員花較少的時間處理系統管理工作，因為服務會為您執行，或大幅簡化這些工作。 例如， [OS/RDBMS 安裝和修補](../database/high-availability-sla.md)、 [動態實例調整大小和](../database/single-database-scale.md)設定、 [備份](../database/automated-backups-overview.md)、 [資料庫](replication-between-two-instances-configure-tutorial.md) 複寫 (包括系統資料庫) 、 [高可用性](../database/high-availability-sla.md)設定，以及健康情況和 [效能監視](../../azure-monitor/insights/azure-sql.md) 資料流程的設定。

如需詳細資訊，請參閱 [支援和不支援的 sql 受控執行個體功能](../database/features-comparison.md)，以及 [sql 受控執行個體和 SQL Server 之間的 t-sql 差異](transact-sql-tsql-differences-sql-server.md)清單。

### <a name="programmatically-identify-a-managed-instance"></a>以程式設計方式識別受控實例

下表顯示數個可透過 Transact-sql 存取的屬性，可讓您用來偵測應用程式是否正在使用 SQL 受控執行個體並取得重要屬性。

|屬性|值|註解|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|此值與 SQL Database 中的相同。 這 **並不** 表示 SQL 引擎第12版 (SQL Server 2014) 。 SQL 受控執行個體一律會執行最新的穩定 SQL 引擎版本，其等於或高於最新的可用 RTM 版本 SQL Server。  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|此值與 SQL Database 中的相同。|
|`SERVERPROPERTY('EngineEdition')`|8|此值只會識別出受控執行個體。|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|下列格式的完整執行個體 DNS 名稱：`<instanceName>`.`<dnsPrefix>`.database.windows.net，其中 `<instanceName>` 是客戶提供的名稱，而 `<dnsPrefix>` 是自動產生的部分名稱，確保全域 DNS 名稱是唯一的 (例如，"wcus17662feb9ce98")|範例：my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立您的第一個受控實例，請參閱 [快速入門手冊](instance-create-quickstart.md)。
- 如需功能與比較清單，請參閱 [SQL 的一般功能](../database/features-comparison.md)。
- 如需 VNet 設定的詳細資訊，請參閱 [SQL 受控執行個體 vnet](connectivity-architecture-overview.md)設定。
- 如需建立受控實例，並從備份檔案還原資料庫的快速入門，請參閱 [建立受控實例](instance-create-quickstart.md)。
- 如需使用 Azure 資料庫移轉服務進行遷移的教學課程，請參閱 [SQL 受控執行個體使用資料庫移轉服務進行遷移](../../dms/tutorial-sql-server-to-managed-instance.md)。
- 若要使用內建的疑難排解智慧來進行 SQL 受控執行個體資料庫效能的 advanced monitoring，請參閱 [使用 Azure SQL 分析監視 AZURE SQL 受控執行個體](../../azure-monitor/insights/azure-sql.md)。
- 如需定價資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
