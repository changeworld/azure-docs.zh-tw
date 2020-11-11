---
title: 'SQL Server Azure Vm 上的 SQL Server (遷移指南) '
description: 遵循本指南，將您的個別 SQL Server 資料庫移轉至 Azure 虛擬機器上的 SQL Server， (Vm) 。
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: c7a62bb3ed07ffbd8cfef520e5d504c810d11e5a
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496686"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>遷移指南：在 Azure Vm 上 SQL Server 至 SQL Server 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

此遷移指南將引導您 **探索** 、 **評估** 和 **遷移** 您的使用者資料庫，從 SQL Server 到 Azure 虛擬機器上的 SQL Server 實例， () vm 使用備份和還原和記錄傳送，利用 [資料庫 Migration Assistant (DMA)](/sql/dma/dma-overview) 進行評量。 

您可以遷移在內部部署或上執行的 SQL Server：

- 虛擬機器上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 關係資料庫服務 (AWS RDS)  
- 計算引擎 (Google Cloud Platform GCP) 

如需其他遷移策略的詳細資訊，請參閱 [SQL SERVER VM 遷移總覽](sql-server-to-sql-on-azure-vm-migration-overview.md)。

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="遷移程式流程":::

## <a name="prerequisites"></a>先決條件

遷移至 Azure Vm 上的 SQL Server 需要下列各項： 

- [資料庫 Migration Assistant (DMA) ](https://www.microsoft.com/download/details.aspx?id=53595)。
- [Azure Migrate 專案](/azure/migrate/create-manage-projects)。
- [AZURE VM 上](/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal)已備妥的目標 SQL Server，與來源 SQL Server 相同或更高的版本。
- [Azure 與內部部署之間](/architecture/reference-architectures/hybrid-networking)的連線能力。
- [選擇適當的遷移策略](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)。

## <a name="pre-migration"></a>移轉前

開始進行遷移之前，請先探索您的 SQL 環境拓撲，並評估您預期的遷移可行性。 

### <a name="discover"></a>探索

Azure Migrate 會評估內部部署電腦的遷移適用性、執行以效能為基礎的大小調整，並提供在內部部署環境中執行的成本估計。 若要規劃遷移，請使用 Azure Migrate 來 [識別現有的資料來源，以及 SQL Server 實例所使用之功能的詳細資料](../../../migrate/concepts-assessment-calculation.md) 。 此程式牽涉到掃描網路，以使用所使用的版本和功能來識別您組織中所有的 SQL Server 實例。 

> [!IMPORTANT]
> 為您的 SQL Server 實例選擇目標 Azure 虛擬機器時，請務必考慮 [Azure vm 上 SQL Server 的效能指導方針](../../virtual-machines/windows/performance-guidelines-best-practices.md)。

如需其他探索工具，請參閱適用于資料移轉案例的 [服務和工具](../../../dms/dms-tools-matrix.md#business-justification-phase) 。


### <a name="assess"></a>評定

探索所有資料來源之後，請使用 [Data Migration Assistant (DMA) ](/dma/dma-overview) 來評估內部部署 SQL Server 實例 () 遷移至 Azure VM 上的 SQL Server 實例，以瞭解來源與目標實例之間的差距。 


> [!NOTE]
> 如果您 _未_ 升級 SQL Server 版本，請略過此步驟，並移至 [ [遷移](#migrate) ] 區段。 


#### <a name="assess-user-databases"></a>評估使用者資料庫 

Data Migration Assistant (DMA) 藉由偵測可能影響新版本 SQL Server 中資料庫功能的相容性問題，來協助您遷移至新式資料平臺。 DMA 針對您的目標環境建議效能和可靠性改善，也可讓您將架構、資料和登入物件從來源伺服器移至目標伺服器。

若要深入瞭解，請參閱 [評](/sql/dma/dma-migrateonpremsql) 量。 


> [!IMPORTANT]
>根據評量的類型而定，來源 SQL Server 所需的許可權可能會不同。 
   > - 針對「 **功能** 同位檢查」，為連接到來源 SQL Server 資料庫提供的認證必須是 *系統管理員（sysadmin* ）伺服器角色的成員。
   > - 針對相容性問題 advisor，提供的認證必須至少具有 `CONNECT SQL` `VIEW SERVER STATE` 和 `VIEW ANY DEFINITION` 許可權。
   > - DMA 將會醒目提示所選 advisor 在執行評量前所需的許可權。


#### <a name="assess-applications"></a>評估應用程式

一般而言，應用層會存取使用者資料庫，以保存和修改資料。  DMA 可以用兩種方式來評估應用程式的資料存取層： 

- 使用已捕捉的 [擴充事件](/sql/relational-databases/extended-events/extended-events) 或 SQL Server Profiler 的使用者資料庫 [追蹤 ](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) 。 您也可以使用 [資料庫測試助理](/sql/dea/database-experimentation-assistant-capture-trace) 來建立也可以用於 a/B 測試的追蹤記錄檔。

- 使用 [Data Access Migration Toolkit (preview) ](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) (DAMT) ，這會在程式碼內提供 SQL 查詢的探索和評量，並用於將應用程式原始程式碼從某個資料庫平臺遷移至另一個資料庫平臺。 這項工具支援各種常見的檔案類型，包括 c # 和 JAVA、XML 和 Plaint 文字。 如需有關如何執行 DAMT 評估的指南，請參閱 [使用 DMAT](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) 的 blog。

在使用者資料庫的評量期間，使用 DMA 匯 [入](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) 已捕捉的追蹤檔案或 DAMT 檔案。 


#### <a name="scale-assessments"></a>調整評量

如果您有多部伺服器需要 DMA 評估，您可以透過 [命令列介面](/sql/dma/dma-commandline)將程式自動化。 您可以使用介面，事先針對範圍中的每個 SQL Server 實例準備評估命令以進行遷移。 

如需跨大型資產的摘要報告，Data Migration Assistant (DMA) 評量現在可以 [合併到 Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb)中。

#### <a name="refactor-databases-with-dma"></a>使用 DMA 重構資料庫

根據 DMA 評估結果，您可能會有一系列的建議，以確保您的使用者資料庫 () 在遷移後正常執行和運作。 DMA 提供受影響物件的詳細資料，以及如何解決每個問題的資源。 建議您在生產環境遷移之前，先解決所有的重大變更和行為變更。

針對已淘汰的功能，如果您想要避免進行這些變更並加速遷移，您可以選擇在原始 [相容性](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) 模式下執行使用者資料庫 (s) 。 不過，在已被取代的專案解決之前，這會導致無法 [升級資料庫相容性](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) 。

強烈建議在 [遷移後](#post-migration)，將所有 DMA 修正程式編寫腳本並套用至目標 SQL Server 資料庫。

> [!CAUTION]
> 並非所有 SQL Server 版本都支援所有的相容性模式。 檢查您的 [目標 SQL Server 版本](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) 是否支援您所選擇的資料庫相容性。 例如，SQL Server 2019 不支援層級90相容性 (（SQL Server 2005) ）的資料庫。 這些資料庫至少需要升級至相容性層級100。
>

## <a name="migrate"></a>移轉

完成預先遷移步驟之後，您就可以開始遷移使用者資料庫和元件。 使用您慣用的 [遷移方法](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)來遷移您的資料庫。  

以下提供使用備份和還原執行遷移的步驟，或使用備份和還原以及記錄傳送的最短停機時間。 

### <a name="backup-and-restore"></a>備份與還原

若要使用備份和還原來執行標準遷移，請遵循下列步驟： 

1. 根據您的需求，設定 Azure VM 上目標 SQL Server 的連線能力。 請參閱 [連線到 Azure 上的 SQL Server 虛擬機器 (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md)。
1. 暫停/停止任何使用資料庫的應用程式以供遷移。 
1. 使用 [單一使用者模式](/sql/relational-databases/databases/set-a-database-to-single-user-mode)，確保使用者資料庫 (的) 處於非使用中狀態。 
1. 執行完整資料庫備份至內部部署位置。
1. 使用遠端桌面、 [Azure 資料總管](/data-explorer/data-explorer-overview)或 [AZCopy 命令列公用程式](../../../storage/common/storage-use-azcopy-v10.md) ，將您的內部部署備份檔案 (s) 複製到您的 VM， ( # A0 2 TB 備份建議) 。
1. 將完整資料庫備份 (s) 還原至 Azure VM 上的 SQL Server。

### <a name="log-shipping--minimize-downtime"></a>記錄傳送 (將停機時間降到最低) 

若要使用備份、還原和記錄傳送來執行降至低的停機時間，請遵循下列步驟： 

1. 根據您的需求，設定對 Azure VM 上的目標 SQL Server 的連線能力。 請參閱 [連線到 Azure 上的 SQL Server 虛擬機器 (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md)。
1. 請確定要遷移的內部部署使用者資料庫 () 是完整或大量記錄復原模式。
1. 執行完整資料庫備份至內部部署位置，並修改任何現有的完整資料庫備份作業，以使用 [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) 關鍵字來保留記錄鏈。
1. 使用遠端桌面、 [Azure 資料總管](/data-explorer/data-explorer-overview)或 [AZCopy 命令列公用程式](../../../storage/common/storage-use-azcopy-v10.md) ，將您的內部部署備份檔案 (s) 複製到您的 VM， ( # B0 1 TB 備份建議) 。
1. 在 Azure VM 上的 SQL Server 上還原完整資料庫備份 (s) 。
1. 在內部部署資料庫與 Azure VM 上的目標 SQL Server 之間設定 [記錄傳送](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) 。 請務必不要重新初始化資料庫 (s) ，因為先前的步驟已完成這項作業。
1. **切換** 至目標伺服器。 
   1. 使用要遷移的資料庫暫停/停止應用程式。 
   1. 使用 [單一使用者模式](/sql/relational-databases/databases/set-a-database-to-single-user-mode)，確保使用者資料庫 (的) 處於非使用中狀態。 
   1. 準備好時，請在內部部署資料庫 (s 中執行記錄傳送 [控制的容錯移轉](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) ，) 以 Azure VM 上的 SQL Server 為目標。



### <a name="migrating-objects-outside-user-databases"></a>在使用者資料庫外部遷移物件 (s) 

在遷移後，您的使用者資料庫可能需要額外的 SQL Server 物件，才能順利運作。 

下表提供的清單元件和建議的遷移方法，可以在您的使用者資料庫移轉之前或之後完成： 


| **功能** | **元件** | **(s 的遷移方法)** |
| --- | --- | --- |
| **資料庫** | 模型  | 使用 SQL Server Management Studio 的腳本 |
|| Tempdb | 規劃將 TempDB 移到 [AZURE VM 暫存磁片 (SSD](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)) 以獲得最佳效能。 請務必挑選具有足夠本機 SSD 的 VM 大小，以容納您的 TempDB。 |
|| 具有 Filestream 的使用者資料庫 |  使用 [備份和還原](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) 方法來進行遷移。 DMA 不支援具有 Filestream 的資料庫。 |
| **安全性** | SQL Server 和 Windows 登入 | 使用 DMA 來 [遷移使用者](/sql/dma/dma-migrateserverlogins)登入。 |
|| SQL Server 角色 | 使用 SQL Server Management Studio 的腳本 |
|| 密碼編譯提供者 | 建議 [轉換為使用 Azure Key Vault 服務](../../virtual-machines/windows/azure-key-vault-integration-configure.md)。 此程式會使用 [SQL VM 資源提供者](../../virtual-machines/windows/sql-vm-resource-provider-register.md)。 |
| **伺服器物件** | 備份裝置 | 使用 [Azure 備份 Service](../../../backup/backup-sql-server-database-azure-vms.md) 或寫入備份來取代為資料庫備份，以 [Azure 儲存體](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 CU2 +) 。 此程式會使用 [SQL VM 資源提供者](../../virtual-machines/windows/sql-vm-resource-provider-register.md)。|
|| 連結的伺服器 | 使用 SQL Server Management Studio 的腳本。 |
|| 伺服器觸發程式 | 使用 SQL Server Management Studio 的腳本。 |
| **複寫** | 本機發行集 | 使用 SQL Server Management Studio 的腳本。 |
|| 本機訂閱者 | 使用 SQL Server Management Studio 的腳本。 |
| **Polybase** | PolyBase | 使用 SQL Server Management Studio 的腳本。 |
| **管理** | Database Mail | 使用 SQL Server Management Studio 的腳本。 |
| **SQL Server Agent** | 工作 | 使用 SQL Server Management Studio 的腳本。 |
|| 警示 | 使用 SQL Server Management Studio 的腳本。 |
|| 運算子 | 使用 SQL Server Management Studio 的腳本。 |
|| Proxy | 使用 SQL Server Management Studio 的腳本。 |
| **作業系統** | 檔案，檔案共用 | 記下您的 SQL Server 所使用的任何其他檔案或檔案共用，並在 Azure VM 目標上進行複寫。 |


## <a name="post-migration"></a>移轉後

當您順利完成遷移階段之後，請完成一系列的遷移後工作，以確保一切都能順利且有效率地運作。

### <a name="remediate-applications"></a>修復應用程式

將資料移轉至目標環境之後，先前取用來源的所有應用程式都必須開始取用目標。 在某些情況下，完成這項工作可能需要變更應用程式。

將任何資料庫 Migration Assistant 建議的修正程式套用至使用者資料庫 (s) 。 建議您編寫這些腳本以確保一致性，並允許自動化。

### <a name="perform-tests"></a>執行測試

資料庫移轉的測試方法包含執行下列活動：

1. **開發驗證測試。**  使用 SQL 查詢來測試資料庫移轉。 建立驗證查詢以針對來源和目標資料庫執行。 驗證查詢應涵蓋已定義的範圍。
2. **設定測試環境。**  測試環境應該包含源資料庫與目標資料庫的複本。 請務必隔離測試環境。
3. **執行驗證測試。**  針對來源和目標執行驗證測試，然後分析結果。
4. **執行效能測試。**  針對來源和目標執行效能測試，然後分析並比較結果。

> [!TIP]
> 使用 [資料庫測試助理 (DEA) ](/sql/dea/database-experimentation-assistant-overview) 協助評估目標 SQL Server 效能。
>

### <a name="optimize"></a>最佳化

「遷移後階段」對於協調任何具有資料精確度和完整性的問題，以及解決工作負載的潛在效能問題而言很重要。

如需這些問題的詳細資訊和解決這些問題的特定步驟，請參閱下列資源：

- [遷移後驗證和優化指南。](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [調整 AZURE SQL 虛擬機器中的效能](../../virtual-machines/windows/performance-guidelines-best-practices.md)。
- [Azure 成本優化中心](https://azure.microsoft.com/overview/cost-optimization/)。

## <a name="next-steps"></a>後續步驟

- 若要檢查適用于 SQL Server 的服務可用性，請參閱 [Azure 全球基礎結構中心](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

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
