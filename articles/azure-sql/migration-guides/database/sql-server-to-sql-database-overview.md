---
title: SQL Server 至 SQL Database：遷移總覽
description: 瞭解可用來將 SQL Server 資料庫移轉至 Azure SQL Database 的不同工具和選項。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2d668775e8d11faa0b2913cebc41e5217b49c278
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357311"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>遷移總覽： SQL Server 至 SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

瞭解將您的 SQL Server 遷移至 Azure SQL Database 的不同遷移選項和考慮。 

您可以遷移在內部部署或上執行的 SQL Server： 

- 虛擬機器上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 關係資料庫服務 (AWS RDS)  
- 計算引擎 (Google Cloud Platform GCP)   
- 適用于 SQL Server (Google Cloud Platform 的雲端 SQL-GCP)  

如需其他案例，請參閱 [資料庫移轉指南](https://datamigration.microsoft.com/)。 

## <a name="overview"></a>概觀

[Azure SQL Database](../../database/sql-database-paas-overview.md) 是 SQL Server 工作負載的建議目標選項，需要完全受控的平臺即服務 (PaaS) 。 SQL Database 處理大部分的資料庫管理功能，以及內建的高可用性、智慧型查詢處理、擴充性和效能功能，以符合許多不同的應用程式類型。 

SQL Database 可提供多種 [部署模型](../../database/sql-database-paas-overview.md#deployment-models) 和 [服務層級](../../database/service-tiers-vcore.md#service-tiers) 的彈性，以滿足不同類型的應用程式或工作負載。

遷移至 SQL Database 的主要優點之一，就是您可以利用 PaaS 功能將應用程式現代化，並消除範圍在實例層級（例如 SQL Agent 作業）之技術元件的任何相依性。

您也可以將 SQL Server 的內部部署授權遷移至 Azure SQL Database，方法是在您選擇以[vCore 為基礎的購買模型](../../database/service-tiers-vcore.md)時，使用 SQL Server [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)進行。

當您準備將 SQL Server 資料庫移轉至 Azure SQL Database 時，本指南旨在說明遷移選項和考慮。  

## <a name="considerations"></a>考量 

評估遷移選項時要考慮的重要因素取決於： 

- 伺服器和資料庫的數目
- 資料庫的大小
- 在遷移過程中可接受的商務停機時間 

本指南中所列的遷移選項會將這些因素納入考慮。 若要將邏輯資料移轉至 Azure SQL Database，遷移的時間可能取決於資料庫中的物件數目和資料庫的大小。 

不同的工具適用于不同的工作負載和使用者喜好設定。 有些工具可用來快速地使用以 UI 為基礎的工具來遷移單一資料庫，而其他工具可以遷移多個可自動化的資料庫，以大規模處理遷移。 

## <a name="choose-appropriate-target"></a>選擇適當的目標

請考慮一般指導方針，以協助您選擇正確的部署模型和服務層級的 Azure SQL Database。 您可以在部署期間選擇計算和儲存體資源，然後在之後使用  [Azure 入口網站](../../database/scale-resources.md)  變更它們，而不會導致應用程式的停機時間。


**部署模型**：瞭解您的應用程式工作負載和使用模式，以決定單一資料庫或彈性集區。 

- [單一資料庫](../../database/single-database-overview.md)代表適用于大部分新式雲端應用程式和微服務的完全受控資料庫。
- [彈性集](../../database/elastic-pool-overview.md)區是單一資料庫的集合，其中包含一組共用的資源（例如 CPU 或記憶體），適用于結合集區中的資料庫與可預測的使用模式，可有效地共用相同的資源集。

**購買模型**：選擇 VCORE、DTU 或無伺服器購買模型。 

- [VCore 模型](../../database/service-tiers-vcore.md)可讓您選擇 Azure SQL Database 的虛擬核心數目，使其在從內部部署 SQL Server 翻譯時成為最簡單的選擇。 這是唯一支援使用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)來節省授權成本的選項。 
- [DTU 模型](../../database/service-tiers-dtu.md)會將基礎計算、記憶體和 IO 資源抽象化，以提供混合的 DTU。 
- [無伺服器模型](../../database/serverless-tier-overview.md)適用于需要自動隨選調整的工作負載，以及每秒使用量計費的計算資源。 無伺服器計算層會在非使用期間自動暫停資料庫 (其中只會) 儲存體計費，並在活動傳回時自動繼續資料庫。 

**服務層**：為不同類型的應用程式選擇三個服務層級。

- [一般用途/標準服務層](../../database/service-tier-general-purpose.md) 級提供平衡的預算導向選項，並提供適合用來提供中層級應用程式的計算和儲存體，並內建在儲存層的冗余，可從失敗中復原。 專為大部分的資料庫工作負載所設計。 
- [商務關鍵性/高階服務層級](../../database/service-tier-business-critical.md) 適用于需要高交易率、低延遲 IO 和高等級復原的高階層應用程式，可用於容錯移轉和卸載讀取工作負載。
- [超大規模服務層級](../../database/service-tier-hyperscale.md) 適用于具有成長資料量的資料庫，且需要自動擴大至 100-TB 的資料庫大小。 專為非常大型的資料庫所設計。 

> [!IMPORTANT]
> [交易記錄檔速率會受](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) 限於 Azure SQL Database，以限制高的內嵌速率。 如此一來，在遷移期間，您可能需要調整目標資料庫資源 (虛擬核心/Dtu) ，以簡化 CPU 或輸送量的壓力。 選擇適當大小的目標資料庫，但視需要規劃調整資源以進行遷移。 


### <a name="sql-server-on-azure-vm-alternative"></a>Azure VM 替代方案上的 SQL Server

您的企業可能會有需求，使 [Azure 虛擬機器上的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 比 Azure SQL Database 更適合的目標。 

如果下列適用于您的企業，請考慮改為移至 Azure VM 上的 SQL Server： 

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
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) \(英文\)|Data Migration Assistant 是一種桌面工具，可提供 SQL Server 和遷移的無縫評量，以 Azure SQL Database (架構和資料) 。 此工具可以安裝在內部部署的伺服器上，或是可連接至源資料庫的本機電腦上。 遷移程式是來源和目標資料庫中的物件之間的邏輯資料移動。 </br> - (架構和資料) 遷移單一資料庫|
|[Azure 資料庫移轉服務 (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md) \(部分機器翻譯\)|第一方 Azure 服務，可將您的 SQL Server 資料庫移轉至使用 Azure 入口網站的 Azure SQL Database，或使用 PowerShell 自動進行。 Azure DMS 會要求您在布建期間選取慣用的 Azure 虛擬網路 (VNet) ，以確保有與您的來源 SQL Server 資料庫之間的連線能力。 </br> -遷移單一資料庫或大規模遷移。 |
| | |


### <a name="alternative-tools"></a>替代工具

下表列出替代的遷移工具： 

|技術 |描述  |
|---------|---------|
|[異動複寫](../../database/replication-to-sql-database.md)|藉由提供發行者-訂閱者類型遷移選項，同時維持交易一致性，將資料從來源 SQL Server 資料庫資料表 (s 複製) 至 SQL Database。 累加式資料變更會在發行者上進行時傳播至訂閱者。|
|[匯入匯出服務/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 是一個 Windows 檔案，其 `.bacpac` 副檔名會封裝資料庫的架構和資料。 BACPAC 可以用來從來源 SQL Server 匯出資料，以及將資料匯入 Azure SQL Database。 您可以使用 Azure 入口網站，將 BACPAC 檔案匯入到新的 Azure SQL Database。 </br></br> 針對大型資料庫大小或大量資料庫的規模和效能，您應該考慮使用 [SqlPackage](../../database/database-import.md#using-sqlpackage) 命令列公用程式來匯出和匯入資料庫。|
|[大量複製](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[大量複製程式 (bcp) 公用](/sql/tools/bcp-utility)程式會將 SQL Server 實例的資料複製到資料檔案中。 使用 BCP 公用程式從來源匯出資料，並將資料檔案匯入目標 SQL Database。 </br></br> 針對將資料移至 Azure SQL Database 的高速大量複製作業， [智慧型大量複製工具](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) 可用來利用平行複製工作，將傳送速率最大化。|
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)|Azure Data Factory 中的 [複製活動](../../../data-factory/copy-activity-overview.md) 會使用內建連接器和 [SQL Database](../../../data-factory/concepts-integration-runtime.md)，將資料從來源 SQL Server 資料庫 (s) 到 Integration Runtime。</br> </br> ADF 支援廣泛的 [連接器](../../../data-factory/connector-overview.md) ，以將資料從 SQL Server 來源移至 SQL Database。|
|[SQL 資料同步](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL 資料同步是以 Azure SQL Database 為基礎的服務，可讓您同步處理在內部部署和雲端中跨多個資料庫雙向選取的資料。</br>當資料需要在 Azure SQL Database 或 SQL Server 的數個資料庫之間保持更新時，資料同步會很有用。|
| | |

## <a name="compare-migration-options"></a>比較遷移選項

比較遷移選項來選擇適合您商務需求的路徑。 

### <a name="recommended-options"></a>建議的選項

下表比較建議的遷移選項： 

|移轉選項  |使用時機  |考量  |
|---------|---------|---------|
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) \(英文\) | - (架構和資料) 遷移單一資料庫。  </br> -可在資料移轉過程中容納停機時間。 </br> </br> 支援的來源： </br> -SQL Server (2005-2019) 內部部署或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server VM | -「遷移活動」會執行從來源到目標)  (資料庫物件之間的資料移動，因此建議在離峰時間執行。 </br> -DMA 會報告每個資料庫物件的遷移狀態，包括已遷移的資料列數目。  </br> -對於大型遷移 (資料庫的數目/大小) ，請使用下列 Azure 資料庫移轉服務。|
|[Azure 資料庫移轉服務 (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md) \(部分機器翻譯\)| -遷移單一資料庫或大規模遷移。 </br> -可在遷移過程中容納停機時間。 </br> </br> 支援的來源： </br> -SQL Server (2005-2019) 內部部署或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server VM | -大規模的大規模遷移可透過 [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md)自動進行。 </br> -完成遷移的時間取決於資料庫的大小和物件數目。 </br> -需要源資料庫設定為唯讀。 |
| | | |

### <a name="alternative-options"></a>替代選項

下表將比較替代的遷移選項： 

|方法/技術 |使用時機    |考量  |
|---------|---------|---------|
|[異動複寫](../../database/replication-to-sql-database.md)| -藉由持續將源資料庫資料表的變更發佈至目標 SQL Database 資料表來進行遷移。 </br> -所選資料表的完整或部分資料庫移轉 (資料庫) 的子集。  </br> </br> 支援的來源： </br> - [SQL Server (2016-2019) 但有一些限制](/sql/relational-databases/replication/replication-backward-compatibility) </br> -AWS EC2  </br> -GCP Compute SQL Server VM  | -相較于其他遷移選項，安裝程式相當複雜。   </br> -提供連續複寫選項來遷移資料 (而不需要讓資料庫離線) 。  </br> -在來源 SQL Server 上設定「發行者」時，異動複寫有一些要考慮的限制。 若要深入瞭解，請參閱 [發佈物件的限制](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) 。 </br>-您可以監視複寫 [活動](/sql/relational-databases/replication/monitor/monitoring-replication)。    |
|[匯入匯出服務/BACPAC](../../database/database-import.md)| -遷移個別的企業營運應用程式資料庫。 </br>-適用于較小的資料庫。  </br>  -不需要個別的遷移服務或工具。 </br> </br> 支援的來源： </br> -SQL Server (2005-2019) 內部部署或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server VM  |  -需要停機，因為資料需要在來源匯出並匯入目的地。   </br> -匯出/匯入中所使用的檔案格式和資料類型必須與資料表架構一致，以避免截斷/資料類型不符的錯誤。  </br> -匯出具有大量物件的資料庫所花費的時間，可能會大幅增加。       |
|[大量複製](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -遷移完整或部分資料移轉。 </br> -可以容納停機時間。 </br> </br> 支援的來源： </br> -SQL Server (2005-2019) 內部部署或 Azure VM </br> -AWS EC2 </br> -AWS RDS </br> -GCP Compute SQL Server VM   | -需要從來源匯出資料並匯入目標的停機時間。 </br> -匯出/匯入中所使用的檔案格式和資料類型必須與資料表架構一致。 |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)| -從來源 SQL Server 資料庫移轉及/或轉換資料。 </br> -將資料從多個資料來源合併到 Azure SQL Database 通常適用于商業智慧 (BI) 工作負載。  |  -需要在 ADF 中建立資料移動管線，以將資料從來源移至目的地。   </br> - [成本](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 是很重要的考慮，而且是以管線觸發程式、活動執行、資料移動的持續時間等為基礎。 |
|[SQL 資料同步](../../database/sql-data-sync-data-sql-server-sql-database.md)| -在來源與目標資料庫之間同步處理資料。</br> -適合在雙向流程中，于 Azure SQL Database 和內部部署 SQL Server 之間執行持續同步。 | -Azure SQL Database 必須是中樞資料庫，才能與內部內部部署 SQL Server 資料庫做為成員資料庫進行同步處理。</br> -相較于異動複寫，SQL 資料同步支援在內部部署和 Azure SQL Database 之間進行雙向資料同步。 </br> -視工作負載而定，可能會有較高的效能影響。|
| | | |

## <a name="feature-interoperability"></a>功能互通性 

當您遷移依賴其他 SQL Server 功能的工作負載時，還有其他考慮。

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
藉由將套件重新部署至 [Azure Data Factory](../../../data-factory/introduction.md)中的 azure ssis 執行時間，以將 SQL SERVER INTEGRATION SERVICES (SSIS) 套件遷移至 azure。 Azure Data Factory 藉由提供在 Azure 中執行 SSIS 套件所建立的執行時間， [支援 SSIS 封裝的遷移](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) 。 或者，您也可以使用 [資料流程](../../../data-factory/concepts-data-flow-overview.md)，以原生方式在 ADF 中重寫 SSIS ETL 邏輯。


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
將 SQL Server Reporting Services (SSRS) 報表遷移至 Power BI 中的編頁報表。 使用 [RDL 遷移工具](https://github.com/microsoft/RdlMigration) 來協助準備和遷移您的報表。 此工具是由 Microsoft 所開發，可協助客戶將 RDL 報表從其 SSRS 伺服器遷移至 Power BI。 它可在 GitHub 上取得，並記載遷移案例的端對端逐步解說。 

#### <a name="high-availability"></a>高可用性
手動設定 SQL Server 高可用性功能（例如 Always On 容錯移轉叢集實例和 Always On 可用性群組）會在目標 Azure SQL Database 上淘汰，因為高可用性架構已內建于 [一般用途 (標準可用性模型) ](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 和 [業務關鍵 (premium 可用性模型) ](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL Database。 商務關鍵性/高階服務層也提供讀取向外延展功能，可讓您連接到其中一個次要節點，以進行唯讀用途。 

除了 SQL Database 所包含的高可用性架構之外，還有 [自動容錯移轉群組](../../database/auto-failover-group-overview.md) 功能，可讓您管理受控實例中的資料庫複寫和容錯移轉至另一個區域。 

#### <a name="sql-agent-jobs"></a>SQL Agent 作業
Azure SQL Database 不直接支援 SQL Agent 作業，而且必須部署至 [彈性資料庫工作 (預覽) ](../../database/job-automation-overview.md#elastic-database-jobs-preview)。

#### <a name="logins-and-groups"></a>登入和群組
將 SQL 登入從來源 SQL Server 移至 Azure SQL Database 使用資料庫移轉服務 (DMS) 處於離線模式。  使用 [**遷移嚮導]** 中的 [**選取** 的登入] 分頁，將登入遷移至目標 SQL Database。 

您也可以在 [DMS 設定] 頁面中啟用對應的切換按鈕，以使用 DMS 來遷移 Windows 使用者和群組。 

或者，您可以使用由 Microsoft 資料移轉架構設計人員特別設計的 [PowerShell 公用程式工具](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) 。 公用程式會使用 PowerShell 來建立 Transact-sql (T-sql) 腳本來重新建立登入，並從來源選取資料庫使用者至目標。 此工具會自動將 Windows AD 帳戶對應到 Azure AD 帳戶，並且可以針對來源 Active Directory 的每個登入進行 UPN 查閱。 此工具會編寫自訂伺服器和資料庫角色的腳本，以及角色成員資格、資料庫角色和使用者權限。 自主資料庫尚未受到支援，而且只會編寫腳本的部分可能 SQL Server 許可權。 


#### <a name="system-databases"></a>系統資料庫
針對 Azure SQL Database，唯一適用的系統資料庫是 [master](/sql/relational-databases/databases/master-database) 和 tempdb。 若要深入瞭解，請參閱 [Azure SQL Database 中的 Tempdb](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。

## <a name="leverage-advanced-features"></a>利用先進的功能 

請務必利用 SQL Database 所提供的先進雲端式功能。 例如，您不再需要擔心管理備份的方式，因為服務會為您執行此工作。 您可以還原至 [保留期限內的任何時間點](../../database/recovery-using-backups.md#point-in-time-restore)。 

若要加強安全性，請考慮使用 [Azure Active Directory 驗證](../../database/authentication-aad-overview.md)、 [審核](../../database/auditing-overview.md)、 [威脅偵測](../../database/azure-defender-for-sql.md)、資料 [列層級安全性](/sql/relational-databases/security/row-level-security)和 [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)。

除了先進的管理和安全性功能之外，SQL Database 還提供一組可協助您 [監視和微調工作負載](../../database/monitor-tune-overview.md)的 advanced tools。 [Azure SQL 分析 (Preview) ](../../../azure-monitor/insights/azure-sql.md) 是一種先進的雲端監視解決方案，可在單一視圖中大規模地監視 Azure SQL Database 和跨多個訂用帳戶的所有資料庫效能。 Azure SQL 分析會使用內建的智慧效能疑難排解，來收集關鍵效能計量並將其視覺化。

[自動調整](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)  持續監視 SQL 執行計畫統計資料的效能，並自動修正已識別的效能問題。 


## <a name="migration-assets"></a>移轉資產 

如需其他協助，請參閱下列針對真實世界遷移專案所開發的資源。

|資產  |說明  |
|---------|---------|
|[資料工作負載評定模型及工具](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 此工具會針對指定的工作負載，提供建議的「最適合」目標平台、雲端整備，以及應用程式/資料庫補救等級。 它提供簡單、單鍵的計算和報告產生，藉由提供自動化和統一的目標平臺決策流程，協助加速大型資產的評量。|
|[DBLoader 公用程式](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader 可以用來將資料從分隔的文字檔載入 SQL Server 中。 此 Windows 主控台公用程式使用 SQL Server native client 大量載入介面，此介面適用于所有 SQL Server 版本，包括 Azure SQL Database。|
|[使用 PowerShell 建立大量資料庫](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|這包含一組三個 PowerShell 腳本，可建立資源群組 ( # A0) 、 [Azure 中的邏輯伺服器](../../database/logical-servers.md) ( # A1) 和 Azure SQL Database ( # A2) 。 腳本包含迴圈功能，因此您可以視需要逐一查看和建立任意數量的伺服器和資料庫。|
|[使用 MSSQL-Scripter & PowerShell 進行大量架構部署](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|此資產會建立資源群組、Azure 中的一部或多部 [邏輯伺服器](../../database/logical-servers.md) 來裝載 Azure SQL Database、從內部部署 SQL Server (或 (2005 +) 的多個 SQL server 匯出每個架構，然後將其匯入 Azure SQL Database。|
|[將 SQL Server Agent 作業轉換成彈性資料庫工作](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|此腳本會將您的來源 SQL Server Agent 工作遷移至彈性資料庫工作。|
|[從 Azure SQL Database 傳送郵件](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|這會提供解決方案，做為可在內部部署 SQL Server 中使用的 SendMail 功能替代方案。 解決方案會使用 Azure Functions 和 Azure SendGrid 服務，從 Azure SQL Database 傳送電子郵件。|
|[將內部部署 SQL Server 登入移至 Azure SQL Database 的公用程式](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|此 PowerShell 腳本會建立 T-sql 命令腳本來重新建立登入，並從內部部署 SQL Server 選取資料庫使用者以 Azure SQL Database。 此工具可讓您將 Windows AD 帳戶自動對應到 Azure AD 帳戶，以及選擇性地遷移 SQL Server 的原生登入。|
|[使用 Logman 進行 PerfMon 資料收集自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|此工具會收集效能監視器資料，以瞭解基準效能並協助遷移目標建議。 此工具會使用 logman.exe 來建立命令，以建立、啟動、停止及刪除遠端 SQL Server 上設定的效能計數器。|
|[白皮書-使用 BACPAC 將資料庫移轉至 Azure SQL DB](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|本白皮書提供的指引和步驟可協助您加速從 SQL Server 到使用 BACPAC 檔案 Azure SQL Database 的遷移。|

這些資源是在資料 SQL Ninja 計畫中開發，由 Azure 資料群組工程小組贊助。 資料 SQL Ninja 計畫其核心宗旨是要為複雜的現代化步驟除去障礙並加速其過程，並將資料平台移轉機會與 Microsoft 的 Azure 資料平台相比較。 如果您認為組織想參與資料 SQL Ninja 計畫，請連絡帳戶小組並要求其提交提名。


## <a name="next-steps"></a>後續步驟

若要開始將您的 SQL Server 遷移至 Azure SQL Database，請參閱 [SQL Server 以 SQL Database 遷移指南](sql-server-to-sql-database-guide.md)。

- 如需 Microsoft 和協力廠商服務的矩陣，以及可用來協助您進行各種資料庫和資料移轉案例以及專長工作的工具，請參閱 [資料移轉的服務和工具](../../../dms/dms-tools-matrix.md)。

- 若要深入瞭解 SQL Database，請參閱：
   - [Azure SQL Database 總覽](../../database/sql-database-paas-overview.md)
   - [Azure 擁有權總成本計算機](https://azure.microsoft.com/pricing/tco/calculator/) 

- 若要深入瞭解雲端遷移的架構和採用週期，請參閱
   -  [適用於 Azure 的雲端採用架構](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [將工作負載的成本與大小調整遷移至 Azure 的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 若要評估應用程式存取層，請參閱 [Data Access Migration Toolkit (預覽) ](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 如需有關如何執行資料存取層 A/B 測試的詳細資訊，請參閱 [資料庫測試助理](/sql/dea/database-experimentation-assistant-overview)。