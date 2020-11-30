---
title: SQL Server 至 SQL 受控執行個體-遷移指南
description: 遵循本指南，將 SQL Server 資料庫移轉至 Azure SQL 受控執行個體。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 5d5404537ad107a54bd32110727e5a7d0f74ebea
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326891"
---
# <a name="migration-guide-sql-server-to-sql-managed-instance"></a>遷移指南： SQL Server 至 SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

本指南可協助您將 SQL Server 實例遷移至 Azure SQL 受控執行個體。 

您可以遷移在內部部署或上執行的 SQL Server： 

- 虛擬機器上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 關係資料庫服務 (AWS RDS)  
- 計算引擎 (Google Cloud Platform GCP)   
- 適用于 SQL Server (Google Cloud Platform 的雲端 SQL-GCP)  

如需更多的遷移資訊，請參閱 [遷移總覽](sql-server-to-managed-instance-overview.md)。 如需其他案例，請參閱 [資料庫移轉指南](https://datamigration.microsoft.com/)。

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="遷移程式流程":::

## <a name="prerequisites"></a>必要條件 

若要將您的 SQL Server 遷移至 Azure SQL 受控執行個體，請務必完成下列先決條件： 

- 選擇 [遷移方法](sql-server-to-managed-instance-overview.md#compare-migration-options) 以及所選方法所需的對應工具
- 在可連線至來源的電腦上安裝 [Data Migration Assistant (DMA) ](https://www.microsoft.com/download/details.aspx?id=53595) SQL Server


## <a name="pre-migration"></a>移轉前

在確認您的來源環境受到支援之後，請從遷移前階段開始。 探索所有現有的資料來源、評定遷移可行性，並找出可能會妨礙您的遷移的任何封鎖問題。  

### <a name="discover"></a>探索

在探索階段中，掃描網路以找出您的組織所使用的所有 SQL Server 實例和功能。 

使用 [Azure Migrate](../../../migrate/migrate-services-overview.md) 來評估內部部署伺服器的遷移適用性、執行以效能為基礎的大小調整，並提供在 Azure 中執行它們的成本估計。 

或者，您也可以使用 [Microsoft 評估和規劃工具組 (「地圖工具 ](https://www.microsoft.com/download/details.aspx?id=7826) 組」 ) 來評估您目前的 IT 基礎結構。 此工具組提供功能強大的清查、評估和報告工具，可簡化遷移計畫的過程。 

如需可用於探索階段之工具的詳細資訊，請參閱 [資料移轉案例可用的服務和工具](../../../dms/dms-tools-matrix.md)。 

### <a name="assess"></a>評定 

探索到資料來源之後，請評估可遷移至 Azure SQL 受控執行個體的任何內部部署 SQL Server 實例 (s) ，以找出遷移封鎖程式或相容性問題。 

您可以使用 Data Migration Assistant (4.1 版和更新版本的) 來評定要取得的資料庫： 

- [Azure 目標建議](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU 建議](/sql/dma/dma-sku-recommend-sql-db)

若要使用資料庫移轉評估來評估您的環境，請遵循下列步驟： 

1. 開啟 [Data Migration Assistant (DMA) ](https://www.microsoft.com/download/details.aspx?id=53595)。 
1. 選取 [檔案 **]，然後選擇 [** **新增評** 量]。 
1. 指定專案名稱、選取 [SQL Server] 作為 [來源伺服器類型]，然後選取 [Azure SQL 受控執行個體] 作為 [目標伺服器類型]。 
1. 選取您要產生的評量報告)  (s 類型。 例如，資料庫相容性和功能同位。 根據評量的類型而定，來源 SQL Server 所需的許可權可能會不同。  DMA 將會醒目提示所選 advisor 在執行評量前所需的許可權。
    - **功能** 同位類別提供一組完整的建議、Azure 中提供的替代方案，以及可協助您規劃遷移專案的緩和步驟。 需要 (系統管理員（sysadmin）許可權) 
    - **相容性問題** 類別識別部分支援或不支援的功能相容性問題，這些問題可能會封鎖遷移以及解決這些問題的建議 (`CONNECT SQL` 、 `VIEW SERVER STATE` 和 `VIEW ANY DEFINITION`) 所需的許可權。
1. 指定 SQL Server 的來源連接詳細資料，並連接到源資料庫。
1. 選取 [ **開始評估**]。 
1. 當程式完成時，請選取並查看評估報告以瞭解遷移封鎖和功能同位問題。 您也可以將評量報告匯出到可以與組織中其他小組或人員共用的檔案。 
1. 判斷最小化遷移後工作的資料庫相容性層級。  
1. 針對您的內部部署工作負載，找出最佳的 Azure SQL 受控執行個體 SKU。 

若要深入瞭解，請參閱 [使用 Data Migration Assistant 來執行 SQL Server 的遷移評](/sql/dma/dma-assesssqlonprem)量。

如果 SQL 受控執行個體不適合您的工作負載，Azure Vm 上的 SQL Server 可能是您的業務的可行替代目標。 

#### <a name="scaled-assessments-and-analysis"></a>調整規模的評定與分析

Data Migration Assistant 支援執行調整規模評定和匯總評估報告以進行分析。 如果您有多個需要大規模評估和分析的伺服器和資料庫，以提供更廣泛的資料資產查看，請按一下下列連結以深入瞭解。

- [使用 PowerShell 執行調整規模評定](/sql/dma/dma-consolidatereports)
- [使用 Power BI 分析評量報告](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>您也可以使用 [DMA 的命令列公用程式](/sql/dma/dma-commandline) 來自動化執行多個資料庫的評量，也可以讓結果上傳至 [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) ，以供進一步分析和目標就緒。

### <a name="create-a-performance-baseline"></a>建立效能基準

如果您需要將 SQL 受控執行個體上工作負載的效能與 SQL Server 上執行的原始工作負載進行比較，請建立要用於比較的效能基準。 若要深入瞭解，請參閱 [效能基準](sql-server-to-managed-instance-performance-baseline.md) 。 

### <a name="create-sql-managed-instance"></a>建立 SQL 受控執行個體 

根據探索和評估階段中的資訊，建立適當大小的目標 SQL 受控執行個體。 您可以使用 [Azure 入口網站](../../managed-instance/instance-create-quickstart.md)、 [PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)或 [Azure Resource Manager (ARM) 範本](../../managed-instance/create-template-quickstart.md)來這麼做。 


## <a name="migrate"></a>移轉

完成預先遷移階段相關聯的工作之後，您就可以開始執行架構和資料移轉。 

使用您選擇的 [遷移方法](sql-server-to-managed-instance-overview.md#compare-migration-options)來遷移您的資料。 

本指南說明兩個最熱門的選項-Azure 資料庫移轉服務 (DMS) 和原生備份和還原。 

### <a name="database-migration-service"></a>Database Migration Service

若要使用 DMS 執行遷移，請依照下列步驟執行：

1. 如果您是第一次執行此工作，請在您的訂用帳戶中註冊 **microsoft.datamigration** 資源提供者。
1. 在您選擇的所需位置中建立 Azure 資料庫移轉服務實例 (最好在與目標 Azure SQL 受控執行個體相同的區域中) 然後選取現有的虛擬網路，或建立新的虛擬網路來裝載 DMS 實例。
1. 建立 DMS 實例之後，請建立新的遷移專案並將來源伺服器類型指定為 **SQL Server** ，並將目標伺服器類型指定為 **Azure SQL Database 受控執行個體**。 在 [專案建立] 分頁（線上或離線資料移轉）中選擇活動類型。 
1.  在 [遷移 **來源** 詳細資料] 頁面上指定來源 SQL Server 詳細資料，並在 [ **遷移目標** 詳細資料] 頁面上指定目標 Azure SQL 受控執行個體詳細資料。 選取 [下一步] 。
1. 選擇您想要遷移的資料庫。 
1. 提供設定以指定包含資料庫備份檔案的 **SMB 網路共用** 。 使用可存取網路共用之 DMS 的 Windows 使用者認證。 提供您的 **Azure 儲存體帳戶詳細資料**。 
1. 檢查遷移摘要，然後選擇 [ **執行遷移**]。 然後，您可以監視「遷移」活動，並檢查您的資料庫移轉進度。
1. 還原資料庫之後，選擇 [ **開始** 轉換]。 當您將尾記錄備份提供給 SMB 網路共用，並在目標上還原時，遷移程式就會複製尾記錄備份。 
1. 停止您源資料庫的所有連入流量，並將連接字串更新為新的 Azure SQL 受控執行個體資料庫。 

如需此遷移選項的詳細逐步教學課程，請參閱 [使用 DMS 將 SQL Server 遷移至 AZURE SQL 受控執行個體線上](../../../dms/tutorial-sql-server-managed-instance-online.md)。 
   


### <a name="backup-and-restore"></a>備份與還原 

Azure SQL 受控執行個體的其中一項重要功能，就是能夠快速輕鬆地遷移資料庫，這是資料庫備份 (`.bak`) 儲存在 [Azure 儲存體](https://azure.microsoft.com/services/storage/)上的檔案的原生還原。 備份和還原是以資料庫大小為基礎的非同步作業。 

下圖會提供程序的高階概觀：

![圖表顯示 SQL Server 有一個標示為 [備份]/[上傳至 URL] Azure 儲存體的箭號，以及另一個標示為 [從 Azure 儲存體至 SQL 受控執行個體的 URL 還原] 的第二個箭號。](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> 進行備份、將其上傳至 Azure 儲存體，以及對 Azure SQL 受控執行個體執行原生還原作業的時間，取決於資料庫的大小。 請將足夠的停機時間納入考慮，以容納大型資料庫的操作。 


若要使用備份和還原進行遷移，請遵循下列步驟： 

1. 將資料庫備份至 Azure blob 儲存體。 例如，在[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)中使用 [[備份至 url](/sql/relational-databases/backup-restore/sql-server-backup-to-url) ]。 使用 [Microsoft Azure 工具](https://go.microsoft.com/fwlink/?LinkID=324399) 來支援早于 SQL SERVER 2012 SP1 CU2 的資料庫。 
1. 使用 SQL Server Management Studio 連接到您的 Azure SQL 受控執行個體。 
1. 使用共用存取簽章建立認證，以存取您的 Azure Blob 儲存體帳戶與您的資料庫備份。 例如：

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. 從 Azure 儲存體 blob 容器還原備份。 例如： 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. 還原完成後，請在 SQL Server Management Studio 內 **物件總管** 查看資料庫。 

若要深入瞭解此遷移選項，請參閱 [使用 SSMS 將資料庫還原至 AZURE SQL 受控執行個體](../../managed-instance/restore-sample-database-quickstart.md)。

> [!NOTE]
> 資料庫還原作業是非同步的，而且可以重試。 如果連線中斷或逾時已過期，您可能會在 SQL Server Management Studio 中收到錯誤。 Azure SQL Database 將會繼續嘗試在背景還原資料庫，而且您可以使用 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 和 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 檢視追蹤還原進度。



## <a name="data-sync-and-cutover"></a>資料同步和轉換

當您使用會持續將資料變更從來源複寫/同步處理至目標的遷移選項時，來源資料和架構可能會變更，並偏離目標。 在資料同步處理期間，請確定來源上的所有變更都已在遷移過程中捕獲並套用至目標。 

在您確認來源和目標上的資料都相同時，您可以從來源切換至目標環境。 請務必規劃與商務/應用程式小組之間的轉換程式，以確保在轉換期間盡可能減少中斷，並不會影響商務持續性。 

> [!IMPORTANT]
> 如需使用 DMS 進行遷移時，與執行切換的相關特定步驟的詳細資訊，請參閱 [執行遷移](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover)轉換。


## <a name="post-migration"></a>移轉後

當您順利完成遷移階段之後，請完成一系列的遷移後工作，以確保一切都能順利且有效率地運作。 

遷移後階段對於協調任何資料精確度問題和確認完整性，以及解決工作負載的效能問題而言很重要。 

### <a name="remediate-applications"></a>修復應用程式 

將資料移轉至目標環境之後，先前取用來源的所有應用程式都必須開始取用目標。 完成這項工作需要變更應用程式，在某些情況下會需要變更。

### <a name="perform-tests"></a>執行測試

資料庫移轉的測試方法包含下列活動：

1. **開發驗證測試**：若要測試資料庫移轉，則需要使用 SQL 查詢。 您必須建立驗證查詢，以針對來源及目標資料庫執行。 驗證查詢應涵蓋已定義的範圍。
1. **設定測試環境**：測試環境應該包含來源資料庫及目標資料庫的複本。 請務必隔離測試環境。
1. **執行驗證測試**：對來源及目標執行驗證測試，然後分析結果。
1. **執行效能測試**：對來源及目標執行效能測試，然後分析並比較結果。

   > [!NOTE]
   > 如需開發及執行後續移轉驗證測試的協助，請考慮合作夥伴 [QuerySurge](https://www.querysurge.com/company/partners/microsoft) 所提供的資料品質解決方案。 



## <a name="leverage-advanced-features"></a>利用先進的功能 

請務必利用 SQL 受控執行個體所提供的先進雲端式功能，例如 [內建的高可用性](../../database/high-availability-sla.md)、 [威脅偵測](../../database/azure-defender-for-sql.md)，以及 [監視和調整您的工作負載](../../database/monitor-tune-overview.md)。 

[Azure SQL 分析](../../../azure-monitor/insights/azure-sql.md) 可讓您以集中方式監視大型的受控實例集。

只有當 [資料庫相容性層級](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) 變更為最新的相容性層級時，才可使用部分 SQL Server 的功能 (150) 。 


## <a name="next-steps"></a>後續步驟

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