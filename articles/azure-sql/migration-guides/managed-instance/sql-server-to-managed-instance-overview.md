---
title: SQL Server 至 SQL 受控執行個體-遷移總覽
description: 瞭解可用來將 SQL Server 資料庫移轉至 Azure SQL 受控執行個體的不同工具和選項。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2241049e5c3cb5039a73c0f7637f7e3553d2e227
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326864"
---
# <a name="migration-overview-sql-server-to-sql-managed-instance"></a>遷移總覽： SQL Server 至 SQL 受控執行個體
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

瞭解將您的 SQL Server 遷移至 Azure SQL 受控執行個體的不同遷移選項和考慮。 

您可以遷移在內部部署或上執行的 SQL Server： 

- 虛擬機器上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 關係資料庫服務 (AWS RDS)  
- 計算引擎 (Google Cloud Platform GCP)   
- 適用于 SQL Server (Google Cloud Platform 的雲端 SQL-GCP)  

如需其他案例，請參閱 [資料庫移轉指南](https://datamigration.microsoft.com/)。 

## <a name="overview"></a>概觀

如果您需要完全受控的服務，而不需要管理虛擬機器或其作業系統，則建議使用[AZURE SQL 受控執行個體](../../managed-instance/sql-managed-instance-paas-overview.md)作為 SQL Server 工作負載的目標選項。 SQL 受控執行個體可讓您將內部部署應用程式隨即轉移至 Azure，只需進行應用程式或資料庫的變更，同時將您的實例與原生虛擬網路 (VNet) 支援完全隔離。 

## <a name="considerations"></a>考量 

評估遷移選項時要考慮的重要因素取決於： 
- 伺服器和資料庫的數目
- 資料庫的大小
- 在遷移過程中可接受的商務停機時間 

將 SQL Server 遷移至 SQL 受控執行個體的主要優點之一，就是您可以選擇遷移整個實例，或只是個別資料庫的子集。 請仔細規劃在您的遷移程式中包含下列各項： 
- 所有需要共存于相同實例的資料庫 
- 應用程式所需的實例層級物件，包括登入、認證、SQL Agent 作業和操作員，以及伺服器層級的觸發程式。 

> [!NOTE]
> Azure SQL 受控執行個體可保證99.99% 的可用性（即使在重大情況下），因此無法停用 SQL MI 中某些功能所造成的額外負荷。 如需詳細資訊，請參閱 [SQL Server 和 AZURE SQL 受控執行個體 blog 可能導致不同效能的根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) 。 


## <a name="choose-appropriate-target"></a>選擇適當的目標

一些一般指導方針，可協助您選擇適當的 SQL 受控執行個體服務層級和特性，以協助符合您的 [效能基準](sql-server-to-managed-instance-performance-baseline.md)： 

- 使用 [CPU 使用量] 基準來布建受控實例，以符合您 SQL Server 的實例所使用的核心數目。 可能需要調整資源以符合 [硬體產生特性](../../managed-instance/resource-limits.md#hardware-generation-characteristics)。 
- 使用 [記憶體使用量] 基準來選擇適當符合記憶體配置的 [vCore 選項](../../managed-instance/resource-limits.md#service-tier-characteristics) 。 
- 您可以使用檔案子系統的基準 IO 延遲，在一般用途 (延遲超過5毫秒) 和業務關鍵 (延遲低於 3 ms) 服務層級。 
- 使用基準輸送量來預先配置資料和記錄檔的大小，以達到預期的 IO 效能。 

您可以在部署期間選擇計算和儲存體資源，然後在使用 [Azure 入口網站](../../database/scale-resources.md) 之後變更它們，而不會導致您的應用程式停機。 

> [!IMPORTANT]
> [受控實例虛擬網路需求](../../managed-instance/connectivity-architecture-overview.md#network-requirements)中的任何差異都可能會讓您無法建立新的實例，或使用現有的實例。 深入瞭解如何 [建立新](../../managed-instance/virtual-network-subnet-create-arm-template.md?branch=release-ignite-arc-data)   的網路和設定 [現有](../../managed-instance/vnet-existing-add-subnet.md?branch=release-ignite-arc-data)的   網路。 

### <a name="sql-server-vm-alternative"></a>SQL Server VM 替代方案

您的企業可能會有需求，讓 Azure Vm 上的 SQL Server 比 Azure SQL 受控執行個體更適合的目標。 

如果您的企業適用下列情況，請考慮改為移至 SQL Server VM： 

- 如果您需要直接存取作業系統或檔案系統，例如使用 SQL Server 在相同的虛擬機器上安裝協力廠商或自訂代理程式。 
- 如果您對仍不支援的功能具有嚴格的相依性，例如 FileStream/FileTable、PolyBase 和跨實例交易。 
- 如果您絕對需要保持在特定版本的 SQL Server (2012，例如) 。 
- 如果您的計算需求遠低於受控實例供應專案 (一個 vCore，例如) ，而且資料庫匯總不是可接受的選項。 


## <a name="migration-tools"></a>移轉工具


建議的遷移工具是 Data Migration Assistant 和 Azure 資料庫的遷移服務。 還有其他可用的替代方法。 

### <a name="recommended-tools"></a>建議工具

下表列出建議的遷移工具： 

|技術 | 描述|
|---------|---------|
|[Azure 資料庫移轉服務 (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) \(部分機器翻譯\)  | 第一方 Azure 服務支援在離線模式下針對可在遷移過程中承受停機的應用程式，進行遷移。 不同于線上模式的連續遷移，離線模式遷移會執行從來源到目標的完整資料庫備份的一次性還原。 | 
|[原生備份和還原](../../managed-instance/restore-sample-database-quickstart.md) | SQL 受控執行個體支援 () .bak 檔案還原原生 SQL Server 資料庫備份，讓可將完整資料庫備份提供給 Azure 儲存體的客戶成為最簡單的遷移選項。 在本文稍後的「 [遷移資產」一節](#migration-assets) 中，也支援並記載完整和差異備份。| 
| | |

### <a name="alternative-tools"></a>替代工具

下表列出替代的遷移工具： 

|技術 |描述  |
|---------|---------|
|[異動複寫](../../managed-instance/replication-transactional-overview.md) | 提供發行者-訂閱者類型遷移選項，同時維持交易一致性，以將資料從來源 SQL Server 資料庫資料表 (s) 複寫至 SQL 受控執行個體。 |  |
|[大量複製](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [大量複製程式 (bcp) 公用](/sql/tools/bcp-utility)程式會將 SQL Server 實例的資料複製到資料檔案中。 使用 BCP 公用程式從來源匯出資料，並將資料檔案匯入目標 SQL 受控執行個體。</br></br> 針對將資料移至 Azure SQL Database 的高速大量複製作業， [智慧型大量複製工具](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) 可用來利用平行複製工作，將傳送速率最大化。 | 
|[匯入匯出 Wizard/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 是一個 Windows 檔案，其 `.bacpac` 副檔名會封裝資料庫的架構和資料。 BACPAC 可以用來從來源 SQL Server 匯出資料，以及將檔案匯回 Azure SQL 受控執行個體。  |  
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| Azure Data Factory 中的 [複製活動](../../../data-factory/copy-activity-overview.md) 會使用內建的連接器和 [受控執行個體](../../../data-factory/concepts-integration-runtime.md)，將資料從來源 SQL Server 資料庫 (s) 遷移至 SQL Integration Runtime。</br> </br> ADF 支援各種不同的 [連接器](../../../data-factory/connector-overview.md) ，可將資料從 SQL Server 來源移至 SQL 受控執行個體。 |
| | |

## <a name="compare-migration-options"></a>比較遷移選項

比較遷移選項來選擇適合您商務需求的路徑。 

### <a name="recommended-options"></a>建議的選項

下表比較建議的遷移選項： 

|移轉選項  |使用時機  |考量  |
|---------|---------|---------|
|[Azure 資料庫移轉服務 (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) \(部分機器翻譯\) | -大規模遷移單一資料庫或多個資料庫。 </br> -可在遷移過程中容納停機時間。 </br> </br> 支援的來源： </br> -SQL Server (2005-2019) 內部部署或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server VM |  -大規模的大規模遷移可透過 [PowerShell](../../../dms/howto-sql-server-to-azure-sql-mi-powershell.md)自動進行。 </br> -完成遷移的時間取決於資料庫大小，並且受備份和還原時間的影響。 </br> -可能需要足夠的停機時間。 |
|[原生備份和還原](../../managed-instance/restore-sample-database-quickstart.md) | -遷移個別的企業營運應用程式資料庫 (s) 。  </br> -快速且輕鬆地進行遷移，而不需要個別的遷移服務或工具。  </br> </br> 支援的來源： </br> -SQL Server (2005-2019) 內部部署或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server VM | -資料庫備份會使用多個執行緒來優化資料傳輸至 Azure Blob 儲存體，但 ISV 頻寬和資料庫大小可能會影響傳送速率。 </br> -停機時間應能容納執行完整備份和還原 (所需的時間，這是) 資料作業的大小。| 
| | | |

### <a name="alternative-options"></a>替代選項

下表將比較替代的遷移選項： 

|方法/技術 |使用時機 |考量  |
|---------|---------|---------|
|[異動複寫](../../managed-instance/replication-transactional-overview.md) | -持續將源資料庫資料表的變更發佈至目標 SQL 受控執行個體資料庫資料表，以進行遷移。 </br> -所選資料表的完整或部分資料庫移轉 (資料庫) 的子集。  </br> </br> 支援的來源： </br> -SQL Server (2012-2019) 但有一些限制 </br> -AWS EC2  </br> -GCP Compute SQL Server VM | </br> -相較于其他遷移選項，安裝程式相當複雜。   </br> -提供連續複寫選項來遷移資料 (而不需要讓資料庫離線) 。</br> -在來源 SQL Server 上設定「發行者」時，異動複寫有一些要考慮的限制。 若要深入瞭解，請參閱 [發佈物件的限制](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) 。  </br> -可以使用 [監視複寫活動](/sql/relational-databases/replication/monitor/monitoring-replication) 的功能。    |
|[大量複製](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -遷移完整或部分資料移轉。 </br> -可以容納停機時間。 </br> </br> 支援的來源： </br> -SQL Server (2005-2019) 內部部署或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server VM   | -需要從來源匯出資料並匯入目標的停機時間。 </br> -匯出/匯入中所使用的檔案格式和資料類型必須與資料表架構一致。 |
|[匯入匯出 Wizard/BACPAC](../../database/database-import.md)| -遷移個別的企業營運應用程式資料庫 (s) 。 </br>-適用于較小的資料庫。  </br>  不需要個別的遷移服務或工具。 </br> </br> 支援的來源： </br> -SQL Server (2005-2019) 內部部署或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server VM  |   </br> -需要停機，因為資料需要在來源匯出並匯入目的地。   </br> -匯出/匯入中所使用的檔案格式和資料類型必須與資料表架構一致，以避免截斷/資料類型不符的錯誤。 </br> -匯出具有大量物件的資料庫所花費的時間，可能會大幅增加。 |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| -從 SQL Server 資料庫 (s) 的來源遷移和/或轉換資料。</br> -將資料從多個資料來源合併到 Azure SQL 受控執行個體通常適用于商業智慧 (BI) 工作負載。   </br> -需要在 ADF 中建立資料移動管線，以將資料從來源移至目的地。   </br> - [成本](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 是很重要的考慮，而且是以管線觸發程式、活動執行、資料移動的持續時間等為基礎。 |
| | | |

## <a name="feature-interoperability"></a>功能互通性 

當您遷移依賴其他 SQL Server 功能的工作負載時，還有其他考慮。 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

使用 [Azure 資料庫移轉服務 (DMS) ](../../../dms/how-to-migrate-ssis-packages-managed-instance.md)，將 SSISDB 中的 SQL SERVER INTEGRATION SERVICES (SSIS) 套件和專案遷移至 azure SQL 受控執行個體。 

僅支援從 SQL Server 2012 開始的 SSISDB 中的 SSIS 套件來進行遷移。 在遷移之前轉換舊版 SSIS 套件。 若要深入瞭解，請參閱 [專案轉換教學](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) 課程。 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

SQL Server Reporting Services (SSRS) 報表可以遷移至 Power BI 中的分頁報表。 使用 [RDL 遷移工具](https://github.com/microsoft/RdlMigration) 來協助準備和遷移您的報表。 此工具是由 Microsoft 所開發，可協助客戶將 RDL 報表從其 SSRS 伺服器遷移至 Power BI。 它可在 GitHub 上取得，並記載遷移案例的端對端逐步解說。 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

從 SQL Server 2012 和更新版本 SQL Server Analysis Services 表格式模型可以遷移至 Azure Analysis Services，這是在 Azure 中 Analysis Services 表格式模型的 PaaS 部署模型。 您可以在這段 [影片教學](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/)課程中深入瞭解如何將內部內部部署模型遷移至 Azure Analysis Services。

或者，您也可以考慮 [使用新的 XMLA 讀取/寫入端點](/power-bi/admin/service-premium-connect-tools)，將內部部署 Analysis Services 表格式模型遷移至 Power BI Premium。 
> [!NOTE]
> Power BI XMLA 讀取/寫入端點功能目前處於公開預覽狀態，在功能正式推出之前，不應考慮生產工作負載。

#### <a name="high-availability"></a>高可用性

SQL Server 高可用性功能 Always On 容錯移轉叢集實例和 Always On 可用性群組會在目標 Azure SQL 受控執行個體上淘汰，因為高可用性架構已內建于 [一般用途 (標準可用性模型) ](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 和 [業務關鍵 (premium 可用性模型) ](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL 受控執行個體。 高階可用性模型也提供讀取向外延展功能，可讓您連接到其中一個次要節點，以進行唯讀用途。     

除了 SQL 受控執行個體中包含的高可用性架構之外，還有 [自動容錯移轉群組](../../database/auto-failover-group-overview.md) 功能，可讓您管理將受控實例中的資料庫複寫和容錯移轉至另一個區域的功能。 

#### <a name="sql-agent-jobs"></a>SQL Agent 作業

使用離線 Azure 資料庫移轉服務 (DMS) 選項來遷移 [SQL Agent 工作](../../../dms/howto-sql-server-to-azure-sql-mi-powershell.md#offline-migrations)。 否則，請使用 SQL Server Management Studio 編寫 Transact-sql (T-sql) 中的作業腳本，然後在目標 SQL 受控執行個體上手動重新建立它們。 

> [!IMPORTANT]
> Azure DMS 目前僅支援具有 T-sql 子系統步驟的作業。 具有 SSIS 封裝步驟的作業必須以手動方式遷移。 

#### <a name="logins-and-groups"></a>登入和群組

來源 SQL Server 的 SQL 登入可以使用資料庫移轉服務，在離線模式中 (DMS) 移至 Azure SQL 受控執行個體。  使用 [**遷移嚮導]** 中的 [**[選取](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** 登入] 分頁，將登入遷移至目標 SQL 受控執行個體。 

根據預設，Azure 資料庫移轉服務僅支援遷移 SQL 登入。 不過，您可以藉由下列方式啟用遷移 Windows 登入的功能：

確定目標 SQL 受控執行個體具有 Azure AD 讀取存取權，可由具有 **公司系統管理員** 或 **全域管理員** 角色的使用者透過 Azure 入口網站進行設定。
設定您的 Azure 資料庫移轉服務執行個體，以啟用 Windows 使用者/群組登入移轉，此功能是透過 Azure 入口網站在 [設定] 頁面上設定。 啟用此設定之後，請重新啟動服務，變更才會生效。

重新啟動服務之後，Windows 使用者/群組登入就會出現在可供移轉的登入清單中。 對於您遷移的任何 Windows 使用者/群組登入，系統會提示您提供相關聯的網域名稱。 不支援服務使用者帳戶 (具有 NT AUTHORITY 網域名稱的帳戶) 和虛擬使用者帳戶 (具有 NT SERVICE 網域名稱的帳戶)。

若要深入瞭解，請參閱 [如何使用 t-sql 將 SQL Server 實例中的 windows 使用者和群組遷移至 AZURE SQL 受控執行個體](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md)。

或者，您可以使用由 Microsoft 資料移轉架構設計人員特別設計的 [PowerShell 公用程式工具](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) 。 此公用程式會使用 PowerShell 來建立 T-sql 腳本，以重新建立登入，並從來源選取資料庫使用者至目標。 此工具會自動將 Windows AD 帳戶對應到 Azure AD 帳戶，並且可以針對來源 Active Directory 的每個登入進行 UPN 查閱。 此工具會編寫自訂伺服器和資料庫角色的腳本，以及角色成員資格、資料庫角色和使用者權限。 目前不支援自主資料庫，而且只會編寫腳本的部分可能 SQL Server 許可權。 

#### <a name="encryption"></a>加密

使用原生還原選項將受  [透明資料加密](../../database/transparent-data-encryption-tde-overview.md)保護的資料庫移轉   到受控實例時，請先將 [對應的憑證](../../managed-instance/tde-certificate-migrate.md)從來源 SQL Server 遷移至目標 SQL 受控執行個體，*再* 進行資料庫還原。 

#### <a name="system-databases"></a>系統資料庫

不支援系統資料庫還原。 若要遷移 (儲存在 master 或 msdb 資料庫) 的實例層級物件，請使用 Transact-sql (T-sql) 編寫腳本，然後在目標受控實例上重新建立這些物件。 

## <a name="leverage-advanced-features"></a>利用先進的功能 

請務必利用 SQL 受控執行個體所提供的先進雲端式功能。 例如，您不再需要擔心管理備份的方式，因為服務會為您執行此工作。 您可以還原至 [保留期限內的任何時間點](../../database/recovery-using-backups.md#point-in-time-restore)。 此外，您不需要擔心如何設定高可用性，因為 [內建高可用性](../../database/high-availability-sla.md)。 

若要加強安全性，請考慮使用 [Azure Active Directory 驗證](../../database/authentication-aad-overview.md)、 [審核](../../managed-instance/auditing-configure.md)、 [威脅偵測](../../database/azure-defender-for-sql.md)、資料 [列層級安全性](/sql/relational-databases/security/row-level-security)和 [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)。

除了先進的管理和安全性功能之外，SQL 受控執行個體還提供一組可協助您 [監視和微調工作負載](../../database/monitor-tune-overview.md)的 advanced tools。 [Azure SQL 分析](../../../azure-monitor/insights/azure-sql.md)可讓您以集中方式監視大型的受控實例集。 [自動調整](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)  在受控實例中，會持續監視 SQL 計畫執行統計資料的效能，並自動修正已識別的效能問題。 

只有當 [資料庫相容性層級](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) 變更為最新相容性層級 (150) 時，才可使用某些功能。 

## <a name="migration-assets"></a>移轉資產 

如需其他協助，請參閱下列針對真實世界遷移專案所開發的資源。

|資產  |說明  |
|---------|---------|
|[資料工作負載評定模型及工具](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 此工具會針對指定的工作負載，提供建議的「最適合」目標平台、雲端整備，以及應用程式/資料庫補救等級。 此工具提供簡單的按一下即計算與報告產生功能，其可透過提供和自動化的統一目標平台決策程序來協助加速大型資產評估。|
|[DBLoader 公用程式](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader 可以用來將資料從分隔的文字檔載入 SQL Server 中。 此 Windows 主控台公用程式會使用 SQL Server native client 大量載入介面，此介面適用于所有 SQL Server 版本，包括 Azure SQL MI。|
|[將內部部署 SQL Server 登入移至 Azure SQL 受控執行個體的公用程式](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|此 PowerShell 腳本會建立 T-sql 命令腳本來重新建立登入，並從內部部署 SQL Server 選取 Azure SQL 受控執行個體的資料庫使用者。 此工具可讓您將 Windows AD 帳戶自動對應到 Azure AD 帳戶，以及選擇性地遷移 SQL Server 的原生登入。|
|[使用 Logman 進行 Perfmon 資料收集自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|此工具會收集執行資料以瞭解有助於遷移目標建議的基準效能。 此工具會使用 logman.exe 建立命令，該命令會建立、啟動、停止及刪除遠端 SQL Server 上設定的效能計數器。|
|[白皮書-還原完整和差異備份來將資料庫移轉至 Azure SQL 受控執行個體](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|本白皮書提供的指引和步驟可協助您加快從 SQL Server 到 Azure SQL 的遷移受控執行個體如果您只 (完整備份和差異備份，且沒有) 的記錄備份功能。|

這些資源是在資料 SQL Ninja 計畫中開發，由 Azure 資料群組工程小組贊助。 資料 SQL Ninja 計畫其核心宗旨是要為複雜的現代化步驟除去障礙並加速其過程，並將資料平台移轉機會與 Microsoft 的 Azure 資料平台相比較。 如果您認為組織想參與資料 SQL Ninja 計畫，請連絡帳戶小組並要求其提交提名。


## <a name="next-steps"></a>後續步驟

若要開始將您的 SQL Server 遷移至 Azure SQL 受控執行個體，請參閱 [SQL Server 至 SQL 受控執行個體遷移指南](sql-server-to-managed-instance-guide.md)。

- 如需 Microsoft 和協力廠商服務的矩陣，以及可用來協助您進行各種資料庫和資料移轉案例以及專長工作的工具，請參閱 [資料移轉的服務和工具](../../../dms/dms-tools-matrix.md)。

- 若要深入瞭解 Azure SQL 受控執行個體請參閱：
   - [Azure SQL 受控執行個體中的服務層級](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server 與 Azure SQL 受控執行個體之間的差異](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 擁有權總成本計算機](https://azure.microsoft.com/pricing/tco/calculator/) 


- 若要深入瞭解雲端遷移的架構和採用週期，請參閱
   -  [適用於 Azure 的雲端採用架構](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [將工作負載的成本與大小調整遷移至 Azure 的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 若要評估應用程式存取層，請參閱 [Data Access Migration Toolkit (預覽) ](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 如需有關如何執行資料存取層 A/B 測試的詳細資訊，請參閱 [資料庫測試助理](/sql/dea/database-experimentation-assistant-overview)。