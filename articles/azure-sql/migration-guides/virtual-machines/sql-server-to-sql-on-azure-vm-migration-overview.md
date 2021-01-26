---
title: 'SQL Server Azure VM 上的 SQL Server (遷移總覽) '
description: 當您想要將 SQL Server 遷移至 Azure Vm 上的 SQL Server 時，請瞭解不同的遷移策略。
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 0eabb48aabcb50557b342385068807eb67a9b165
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797855"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>遷移總覽： SQL Server Azure Vm 上的 SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

瞭解不同的遷移策略，以將您的 SQL Server 遷移至 Azure 虛擬機器 (Vm) 上的 SQL Server。 

您可以遷移在內部部署或上執行的 SQL Server：

- 虛擬機器上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 關係資料庫服務 (AWS RDS)  
- 計算引擎 (Google Cloud Platform GCP) 

如需其他案例，請參閱 [資料庫移轉指南](https://datamigration.microsoft.com/)。 

## <a name="overview"></a>概觀

當您想要使用熟悉的 SQL Server 環境搭配作業系統控制，並想要利用雲端提供的功能（例如內建的 VM 高可用性、[自動備份](../../virtual-machines/windows/automated-backup.md)和[自動修補](../../virtual-machines/windows/automated-patching.md)）時，請將[Azure 虛擬機器上的 SQL Server 移至 (vm) ](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 。 

藉由將您自己的授權提供給 [Azure Hybrid Benefit 授權模型](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) 來節省成本，或藉由取得 [免費的安全性更新](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)來延長 SQL Server 2008 和 SQL Server 2008 R2 的支援。 


## <a name="choose-appropriate-target"></a>選擇適當的目標

Azure 虛擬機器在 Azure 的許多不同區域中執行，也提供各種不同的 [機器大小](../../../virtual-machines/sizes.md) 和 [儲存體選項](../../../virtual-machines/disks-types.md)。 判斷 SQL Server 工作負載的 VM 和儲存體的正確大小時，請參閱 [Azure 虛擬機器上 SQL Server 的效能指導方針](../../virtual-machines/windows/performance-guidelines-best-practices.md#vm-size-guidance)。 判斷工作負載的 VM 大小和儲存體需求。 建議您透過 Performance-Based [Azure Migrate 評](../../../migrate/concepts-assessment-calculation.md#types-of-assessments)量來調整其大小。 如果這不是可用的選項，請參閱下列文章，以瞭解如何建立您自己 [的效能基準](https://azure.microsoft.com/services/virtual-machines/sql-server/)。

您也應該考慮在 VM 上正確安裝和設定 SQL Server。 建議您使用 [AZURE SQL 虛擬機器映射庫](../../virtual-machines/windows/create-sql-vm-portal.md) ，因為這可讓您使用正確的版本、版本和作業系統來建立 SQL Server VM。 這也會自動向 SQL Server [資源提供者](../../virtual-machines/windows/create-sql-vm-portal.md) 註冊 Azure VM，以啟用自動備份和自動修補等功能。

## <a name="migration-strategies"></a>移轉策略

有兩個遷移策略可將使用者資料庫移轉至 Azure Vm 上的 SQL Server 實例： **遷移** 和 **轉移**。 

適用于您企業的適當方法通常取決於下列因素： 

- 遷移的大小和規模
- 遷移的速度
- 程式碼變更的應用程式支援
- 需要變更 SQL Server 版本、作業系統或兩者。
- 現有產品的支援生命週期
- 在遷移期間應用程式停機時間的視窗

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="虛擬機器遷移停機":::

下表說明兩個遷移策略的差異：
<br />

| **移轉策略** | **說明** | **使用時機** |
| --- | --- | --- |
| **增益 & shift** | 使用隨即轉移策略，將整個實體或虛擬 SQL Server 從其目前的位置移至 Azure VM 上的 SQL Server 實例，而不需要變更作業系統或 SQL Server 版本。 若要完成隨即轉移，請參閱 [Azure Migrate](../../../migrate/migrate-services-overview.md)。 <br /><br /> 來源伺服器會維持線上和服務要求，而來源和目的地伺服器則會同步處理資料，讓您幾乎順暢地進行遷移。 | 用於單一到大規模的遷移，甚至適用于資料中心結束之類的案例。 <br /><br /> 不需要變更使用者 SQL 資料庫或應用程式的程式碼，可讓整體遷移速度更快。 <br /><br />不需要額外的步驟就能遷移商務智慧服務，例如  [SSIS](/sql/integration-services/sql-server-integration-services)、 [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)和 [SSAS](/analysis-services/analysis-services-overview)。 |
|**移轉** | 當您想要升級目標 SQL Server 和/或作業系統版本時，請使用遷移策略。 <br /> <br /> 從 Azure Marketplace 或符合來源 SQL Server 版本的備妥 SQL Server 映射中選取 Azure VM。 | 當需求或想要使用較新版本 SQL Server 中可用的功能，或需要升級不再支援的舊版 SQL Server 和/或作業系統版本時，請使用。  <br /> <br /> 可能需要一些應用程式或使用者資料庫變更，以支援 SQL Server 升級。 <br /><br />在遷移的範圍內，如果有遷移 [商業智慧](#business-intelligence) 服務，可能會有其他考慮。 |


## <a name="lift-and-shift"></a>隨即轉移  

下表詳細說明隨即 **轉移** 策略的可用方法，以將您的 SQL Server 資料庫移轉至 Azure vm 上的 SQL Server：
<br />

|**方法** | **最低來源版本** | **最小目標版本** | **來源備份大小條件約束** |  **注意事項** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Azure VM 儲存體限制](../../../index.yml) |  現有 SQL Server 要依原樣移至 Azure VM 上的 SQL Server 實例。 可以調整最多35000個 Vm 的遷移工作負載。 <br /><br /> 來源伺服器 (的) 在同步處理伺服器資料期間維持連線並提供服務要求，將停機時間降到最低。 <br /><br /> **自動化 & 腳本**： [Azure Site Recovery 腳本](../../../migrate/how-to-migrate-at-scale.md) ，以及 [適用于 Azure 的調整遷移和規劃範例](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>移轉  

由於容易設定，建議的遷移方法是在本機進行原生 SQL Server [備份](/sql/t-sql/statements/backup-transact-sql) ，然後將檔案複製到 Azure。 此方法支援較大的資料庫 ( # B0 1 TB) 適用于從2008開始的所有 SQL Server 版本，以及較大的資料庫備份 ( # B1 1 TB) 。 但是，如果資料庫是從 SQL Server 2014 開始、小於 1 TB，且對 Azure 有良好的連線能力，則 [SQL Server 備份至 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 是較佳的方法。 

將 SQL Server 資料庫移轉至 Azure Vm 上的 SQL Server 實例時，請務必選擇適合在您需要切換到目標伺服器的方法，因為這會影響應用程式停機時間範圍。

下表詳細說明將 SQL Server 資料庫移轉至 Azure Vm 上 SQL Server 的所有可用方法：
<br />

|**方法** | **最低來源版本** | **最小目標版本** | **來源備份大小條件約束** | **注意事項** |
| --- | --- | --- | --- | --- |
| **[備份至檔案](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Azure VM 儲存體限制](../../../index.yml) |  這是一種簡單且經過妥善測試的技術，可在電腦之間移動資料庫。 使用壓縮來將傳輸的備份大小降至最低。 <br /><br /> **Automation & 腳本**： [transact-sql (T-sql)](/sql/t-sql/statements/backup-transact-sql) 並 [AzCopy 至 Blob 儲存體](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[備份至 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| SQL Server 2016 為 12.8 TB，否則為 1 TB | 使用 Azure 儲存體將備份檔案移至 VM 的替代方式。 使用壓縮來將傳輸的備份大小降至最低。 <br /><br /> **Automation & 腳本**：  [t-sql 或維護計畫](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Database Migration Assistant (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Azure VM 儲存體限制](../../../index.yml) |  [DMA](/sql/dma/dma-overview)會評估 SQL Server 內部部署，然後順暢地升級至較新版本的 SQL Server 或遷移至 azure vm 上的 SQL Server，Azure SQL Database 或 azure SQL 受控執行個體。 <br /><br /> 不應該在啟用 Filestream 的使用者資料庫上使用。<br /><br /> DMA 也包含遷移 [SQL 和 Windows](/sql/dma/dma-migrateserverlogins) 登入和評定 [SSIS 封裝](/sql/dma/dma-assess-ssis)的功能。 <br /><br /> **Automation & 腳本**： [命令列介面](/sql/dma/dma-commandline) |
| **[卸離和附加](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Azure VM 儲存體限制](../../../index.yml) | 當您想要 [使用 Azure Blob 儲存體服務來儲存這些](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) 檔案，並將它們附加至 azure VM 上的 SQL Server 實例時，請使用這個方法，特別適用于非常大型的資料庫，或備份和還原的時間太長。 <br /><br /> **Automation & 腳本**：  [t-sql](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) 和 [AzCopy 至 Blob 儲存體](../../../storage/common/storage-use-azcopy-v10.md)|
|**[記錄傳送](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (僅限 Windows)  | SQL Server 2008 SP4 (僅限 Windows)  | [Azure VM 儲存體限制](../../../index.yml) | 記錄傳送會將交易記錄檔從內部部署複寫至 Azure VM 上的 SQL Server 實例。 <br /><br /> 這可在容錯移轉期間提供最短的停機時間，而且設定負荷會比設定 Always On 可用性群組更少。 <br /><br /> **Automation & 腳本**： [t-sql](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[分散式可用性群組](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Azure VM 儲存體限制](../../../index.yml) |  [分散式可用性群組](/sql/database-engine/availability-groups/windows/distributed-availability-groups)是一種特殊類型的可用性群組，可跨越兩個不同的可用性群組。 參與分散式可用性群組的可用性群組不需要位於相同的位置，並且包含跨網域支援。 <br /><br /> 當您在內部部署環境中設定可用性群組時，此方法可將停機時間降至最低。 <br /><br /> **Automation & 腳本**： [t-sql](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> 針對限制為沒有網路選項的大型資料傳輸，請參閱連線 [能力有限的大型資料傳輸](../../../storage/common/storage-solution-large-dataset-low-network.md)。
> 

### <a name="considerations"></a>考量

以下是查看遷移方法時要考慮的重點清單：

- 為了達到最佳的資料傳輸效能，請使用壓縮的備份檔案，將資料庫和檔案遷移至 Azure VM 上的 SQL Server 實例。 針對較大的資料庫，除了壓縮外，還會 [將備份檔案分割成較小](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) 的檔案，以提升備份和傳輸期間的效能。 
- 如果從 SQL Server 2014 或更高版本進行遷移，請考慮 [加密備份](/sql/relational-databases/backup-restore/backup-encryption) ，以在網路傳輸期間保護資料。
- 若要將資料庫移轉期間的停機時間降到最低，請使用 Always On 可用性群組選項。 
- 若要將停機時間降到最低，而不需要設定可用性群組的額外負荷，請使用記錄傳送選項。 
- 針對僅限網路選項，請使用離線遷移方法，例如備份和還原，或 Azure 中提供的 [磁片傳輸服務](../../../storage/common/storage-solution-large-dataset-low-network.md) 。
- 若要同時變更 Azure VM 上 SQL Server 的 SQL Server 版本，請參閱 [變更 SQL Server 版本](../../virtual-machines/windows/change-sql-server-edition.md)。

## <a name="business-intelligence"></a>商業智慧 

在使用者資料庫移轉的範圍外遷移 SQL Server 商務智慧服務時，可能會有其他考慮。 

這些服務包括：

- [**SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>支援的版本

當您準備將 SQL Server 資料庫移轉至 Azure Vm 上的 SQL Server 時，請務必考慮支援的 SQL Server 版本。 如需 Azure Vm 上目前支援的 SQL Server 版本清單，請參閱 [Azure vm 上的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms)。

## <a name="migration-assets"></a>移轉資產 

如需其他協助，請參閱下列針對真實世界遷移專案所開發的資源。

|資產  |說明  |
|---------|---------|
|[資料工作負載評定模型及工具](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Data%20Workload%20Assessment%20Model%20and%20Tool)| 此工具會針對指定的工作負載，提供建議的「最適合」目標平台、雲端整備，以及應用程式/資料庫補救等級。 此工具提供簡單的按一下即計算與報告產生功能，其可透過提供和自動化的統一目標平台決策程序來協助加速大型資產評估。|
|[使用 Logman 進行 Perfmon 資料收集自動化](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|此工具會收集執行資料以瞭解有助於遷移目標建議的基準效能。 此工具會使用 logman.exe 建立命令，該命令會建立、啟動、停止及刪除遠端 SQL Server 上設定的效能計數器。|
|[Azure 中的 SQL Server 部署](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/SQL%20Server%20Deployment%20in%20Azure%20.pdf)|本指引白皮書可協助您查看各種選項，將您的 SQL Server 工作負載移至 Azure，包括功能比較、高可用性和備份/儲存體考慮。 |
|[內部部署 SQL Server 至 Azure 虛擬機器](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/OnPremise%20SQL%20Server%20to%20Azure%20VM.pdf)|本白皮書概述使用範例腳本，從內部部署 SQL Server 備份和還原資料庫至 Azure 虛擬機器上 SQL Server 的步驟。|
|[多個-SQL-VM-VNet-ILB](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/ARM%20Templates/Multiple-SQL-VM-VNet-ILB)|本白皮書概述在 SQL Server Always On 可用性群組設定中設定多部 Azure 虛擬機器的步驟。|
|[支援每個區域 Ultra SSD 的 Azure 虛擬機器](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Find%20Azure%20VMs%20supporting%20Ultra%20SSD)|這些 PowerShell 腳本提供的程式設計選項，可讓您取得支援支援 Ultra Ssd 之 Azure 虛擬機器的區域清單。|

這些資源是在資料 SQL Ninja 計畫中開發，由 Azure 資料群組工程小組贊助。 資料 SQL Ninja 計畫其核心宗旨是要為複雜的現代化步驟除去障礙並加速其過程，並將資料平台移轉機會與 Microsoft 的 Azure 資料平台相比較。 如果您認為組織想參與資料 SQL Ninja 計畫，請連絡帳戶小組並要求其提交提名。

## <a name="next-steps"></a>後續步驟

若要開始將 SQL Server 資料庫移轉至 Azure Vm 上的 SQL Server，請參閱 [個別的資料庫移轉指南](sql-server-to-sql-on-azure-vm-individual-databases-guide.md)。 

- 如需 Microsoft 和協力廠商服務的矩陣，以及可用來協助您瞭解各種資料庫和資料移轉案例以及專長工作的工具，請參閱檔 [和工具以進行資料移轉。](../../../dms/dms-tools-matrix.md)

- 若要深入瞭解 Azure SQL，請參閱：
   - [部署選項](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure VM 上的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 擁有權總成本計算機](https://azure.microsoft.com/pricing/tco/calculator/) 


- 若要深入瞭解雲端遷移的架構和採用週期，請參閱
   -  [適用於 Azure 的雲端採用架構](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [將工作負載的成本與大小調整遷移至 Azure 的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 如需授權的詳細資訊，請參閱
   - [將您自己的授權提供給 Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [取得 SQL Server 2008 和 SQL Server 2008 R2 的免費延伸支援](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- 若要評估應用程式存取層，請參閱 [Data Access Migration Toolkit (預覽) ](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 如需有關如何執行資料存取層 A/B 測試的詳細資訊，請參閱 [資料庫測試助理](/sql/dea/database-experimentation-assistant-overview)。