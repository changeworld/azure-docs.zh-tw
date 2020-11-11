---
title: SQL Server 至 SQL Database-遷移指南
description: 遵循本指南，將您 SQL Server 的資料庫移轉至 Azure SQL Database。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 55ce3747aaf105c7e2cbb830b1175769a658fd72
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496618"
---
# <a name="migration-guide-sql-server-to-sql-database"></a>遷移指南： SQL Server 至 SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

本指南可協助您將 SQL Server 實例遷移至 Azure SQL Database。 

您可以遷移在內部部署或上執行的 SQL Server： 

- 虛擬機器上的 SQL Server  
- Amazon Web Services (AWS) EC2 
- Amazon 關係資料庫服務 (AWS RDS)  
- 計算引擎 (Google Cloud Platform GCP)   
- 適用于 SQL Server (Google Cloud Platform 的雲端 SQL-GCP)  

如需更多的遷移資訊，請參閱 [遷移總覽](sql-server-to-sql-database-overview.md)。 如需其他案例，請參閱 [資料庫移轉指南](https://datamigration.microsoft.com/)。

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="遷移程式流程":::

## <a name="prerequisites"></a>先決條件 

若要將您的 SQL Server 遷移至 Azure SQL Database，請確定您具有下列必要條件： 

- 選擇的 [遷移方法](sql-server-to-sql-database-overview.md#compare-migration-options) 和對應的工具 
- [Data Migration Assistant (DMA) ](https://www.microsoft.com/download/details.aspx?id=53595) 安裝在可連線至來源的電腦上 SQL Server
- 目標 [Azure SQL Database](../../database/single-database-create-quickstart.md)


## <a name="pre-migration"></a>移轉前

在確認您的來源環境受到支援之後，請從遷移前階段開始。 探索所有現有的資料來源、評定遷移可行性，並找出可能會妨礙您的遷移的任何封鎖問題。 

### <a name="discover"></a>探索

在探索階段中，掃描網路以找出您的組織所使用的所有 SQL Server 實例和功能。 

使用 [Azure Migrate](../../../migrate/migrate-services-overview.md) 來評估內部部署伺服器的遷移適用性、執行以效能為基礎的大小調整，並提供在 Azure 中執行它們的成本估計。 

或者，您也可以使用 [Microsoft 評估和規劃工具組 (「地圖工具 ](https://www.microsoft.com/download/details.aspx?id=7826) 組」 ) 來評估您目前的 IT 基礎結構。 此工具組提供功能強大的清查、評估和報告工具，可簡化遷移計畫的過程。 

如需可用於探索階段之工具的詳細資訊，請參閱 [資料移轉案例可用的服務和工具](../../../dms/dms-tools-matrix.md)。 

### <a name="assess"></a>評定 

探索到資料來源之後，請評估任何可遷移至 Azure SQL Database 的內部部署 SQL Server 資料庫 (s) ，以找出遷移封鎖程式或相容性問題。 

您可以使用 Data Migration Assistant (4.1 版和更新版本的) 來評定要取得的資料庫： 

- [Azure 目標建議](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Azure SKU 建議](/sql/dma/dma-sku-recommend-sql-db)

若要使用資料庫移轉評估來評估您的環境，請遵循下列步驟： 

1. 開啟 [Data Migration Assistant (DMA) ](https://www.microsoft.com/download/details.aspx?id=53595)。 
1. 選取 [檔案 **]，然後選擇 [** **新增評** 量]。 
1. 指定專案名稱、選取 [SQL Server] 作為 [來源伺服器類型]，然後選取 [Azure SQL Database] 作為 [目標伺服器類型]。 
1. 選取您要產生的評量報告)  (s 類型。 例如，資料庫相容性和功能同位。 根據評量的類型而定，來源 SQL Server 所需的許可權可能會不同。  DMA 將會醒目提示所選 advisor 在執行評量前所需的許可權。
    - **功能** 同位類別提供一組完整的建議、Azure 中提供的替代方案，以及可協助您規劃遷移專案的緩和步驟。 需要 (系統管理員（sysadmin）許可權) 
    - **相容性問題** 類別識別部分支援或不支援的功能相容性問題，這些問題可能會封鎖遷移以及解決這些問題的建議 (`CONNECT SQL` 、 `VIEW SERVER STATE` 和 `VIEW ANY DEFINITION`) 所需的許可權。
1. 指定 SQL Server 的來源連接詳細資料，並連接到源資料庫。
1. 選取 [ **開始評估** ]。 
1. 程式完成之後，請選取並查看評估報告以瞭解遷移封鎖和功能同位問題。 您也可以將評量報告匯出到可以與組織中其他小組或人員共用的檔案。 
1. 判斷最小化遷移後工作的資料庫相容性層級。  
1. 識別您內部部署工作負載的最佳 Azure SQL Database SKU。 

若要深入瞭解，請參閱 [使用 Data Migration Assistant 來執行 SQL Server 的遷移評](/sql/dma/dma-assesssqlonprem)量。

如果評量遇到多個封鎖程式，以確認您的資料庫尚未準備好進行 Azure SQL Database 的遷移，則另請考慮：

- 如果有多個實例範圍的相依性，則為[AZURE SQL 受控執行個體](../managed-instance/sql-server-to-managed-instance-overview.md)
- 如果 SQL Database 和 SQL 受控執行個體都不是適合的目標， [Azure 虛擬機器上的 SQL Server](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) 。 



#### <a name="scaled-assessments-and-analysis"></a>調整規模的評定與分析
Data Migration Assistant 支援執行調整規模評定和匯總評估報告以進行分析。 

如果您有多個需要大規模評估和分析的伺服器和資料庫，以提供更廣泛的資料資產查看，請參閱下列連結以深入瞭解：

- [使用 PowerShell 執行調整規模評定](/sql/dma/dma-consolidatereports)
- [使用 Power BI 分析評量報告](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> 針對多個資料庫（尤其是大型資料庫）大規模執行評量，也可以使用 [DMA 命令列公用程式](/sql/dma/dma-commandline) 來自動化，並上傳至 [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) 以便進一步分析和目標就緒。

## <a name="migrate"></a>移轉

完成預先遷移階段相關聯的工作之後，您就可以開始執行架構和資料移轉。 

使用您選擇的 [遷移方法](sql-server-to-sql-database-overview.md#compare-migration-options)來遷移您的資料。 

本指南說明兩個最受歡迎的選項-Data Migration Assistant 和 Azure 資料庫移轉服務。 

### <a name="data-migration-assistant-dma"></a>Data Migration Assistant (DMA)

若要使用 DMA 將資料庫從 SQL Server 遷移至 Azure SQL Database，請遵循下列步驟： 

1. 下載並安裝 [Migration Assistant 的資料庫](https://www.microsoft.com/download/details.aspx?id=53595)。
1. 建立新的專案，並選取 [ **遷移** ] 作為專案類型。
1. 將 [來源伺服器類型] 設定為 [ **SQL Server** ]，並將 [目標伺服器類型] 設定為 [ **Azure SQL Database** ]，然後選取 [ **架構和資料** ] 作為 [ **建立** ]。
1. 在 [遷移] 專案中，指定來源伺服器的詳細資料，例如伺服器名稱、用來連接到伺服器的認證，以及要遷移的源資料庫。
1. 在 [目標伺服器詳細資料] 中，指定 Azure SQL Database 伺服器名稱、要連接到伺服器的認證，以及要遷移至的目標資料庫。
1. 選取架構物件，並將它們部署至目標 Azure SQL Database。
1. 最後，選取 [ **開始資料移轉** ] 並監視遷移進度。

如需詳細的教學課程，請參閱 [使用 Data Migration Assistant 將內部部署 SQL Server 或 Azure vm 上的 SQL Server 遷移至 Azure SQL Database](/sql/dma/dma-migrateonpremsqltosqldb)。 


> [!NOTE]
> - 藉由提供更多資源，在匯入程式期間將您的資料庫調整為較高的服務層級和計算大小，以最大化匯入速度。 然後，您可以在匯入成功後縮小。</br>
> - 匯入資料庫的相容性層級是以源資料庫的相容性層級為基礎。 


### <a name="azure-database-migration-service-dms"></a>Azure 資料庫移轉服務 (DMS)

若要使用 DMS 將資料庫從 SQL Server 遷移至 Azure SQL Database，請遵循下列步驟：

1. 如果您還沒有這麼做，請在您的訂用帳戶中註冊 **microsoft.datamigration** 資源提供者。 
1. 在您選擇的所需位置中建立 Azure 資料庫移轉服務實例 (最好是在與目標 Azure SQL Database) 相同的區域中。 選取現有的虛擬網路，或建立一個新的虛擬網路來裝載您的 DMS 實例。
1. 建立 DMS 實例之後，請建立新的遷移專案並將來源伺服器類型指定為 **SQL Server** ，並將目標伺服器類型指定為 **Azure SQL Database** 。 在 [遷移專案建立] 分頁中，選擇 [ **離線資料移轉** ] 作為活動類型。
1. 在 [遷移 **來源** 詳細資料] 頁面上指定來源 SQL Server 詳細資料，並在 [ **遷移目標** 詳細資料] 頁面上指定目標 Azure SQL Database 詳細資料。
1. 對應來源和目標資料庫以進行遷移，然後選取您想要遷移的資料表。
1. 查看 [遷移摘要]，然後選取 [ **執行遷移** ]。 然後，您可以監視「遷移」活動，並檢查您的資料庫移轉進度。

如需詳細的教學課程，請參閱 [使用 DMS 將 SQL Server 遷移至 Azure SQL Database](../../../dms/tutorial-sql-server-to-azure-sql.md)。 

## <a name="data-sync-and-cutover"></a>資料同步和轉換

當您使用會持續將資料變更從來源複寫/同步處理至目標的遷移選項時，來源資料和架構可能會變更，並偏離目標。 在資料同步處理期間，請確定來源上的所有變更都已在遷移過程中捕獲並套用至目標。 

在您確認來源和目標上的資料相同時，您可以從來源切換至目標環境。 請務必規劃與商務/應用程式小組之間的轉換程式，以確保在轉換期間盡可能減少中斷，並不會影響商務持續性。 

> [!IMPORTANT]
> 如需使用 DMS 進行遷移時，與執行切換的相關特定步驟的詳細資訊，請參閱 [執行遷移](../../../dms/tutorial-sql-server-azure-sql-online.md#perform-migration-cutover)轉換。


## <a name="post-migration"></a>移轉後

當您順利完成遷移階段之後，請完成一系列的遷移後工作，以確保一切都能順利且有效率地運作。 

遷移後階段對於協調任何資料精確度問題和確認完整性，以及解決工作負載的效能問題而言很重要。 

### <a name="remediate-applications"></a>修復應用程式 

將資料移轉至目標環境之後，先前取用來源的所有應用程式都必須開始取用目標。 完成這項工作需要變更應用程式，在某些情況下會需要變更。

### <a name="perform-tests"></a>執行測試

資料庫移轉的測試方法包含下列活動：

1. **開發驗證測試** ：若要測試資料庫移轉，則需要使用 SQL 查詢。 您必須建立驗證查詢，以針對來源及目標資料庫執行。 驗證查詢應涵蓋已定義的範圍。
1. **設定測試環境** ：測試環境應該包含來源資料庫及目標資料庫的複本。 請務必隔離測試環境。
1. **執行驗證測試** ：對來源及目標執行驗證測試，然後分析結果。
1. **執行效能測試** ：對來源及目標執行效能測試，然後分析並比較結果。

   > [!NOTE]
   > 如需開發及執行後續移轉驗證測試的協助，請考慮合作夥伴 [QuerySurge](https://www.querysurge.com/company/partners/microsoft) 所提供的資料品質解決方案。 


## <a name="leverage-advanced-features"></a>利用先進的功能 

請務必利用 SQL Database 所提供的先進雲端式功能，例如 [內建的高可用性](../../database/high-availability-sla.md)、 [威脅偵測](../../database/advanced-data-security.md)，以及 [監視和調整您的工作負載](../../database/monitor-tune-overview.md)。 

只有當 [資料庫相容性層級](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) 變更為最新的相容性層級時，才可使用部分 SQL Server 的功能 (150) 。 

若要深入瞭解，請參閱 [在遷移後管理 Azure SQL Database](../../database/manage-data-after-migrating-to-database.md)


## <a name="next-steps"></a>後續步驟

- 如需 Microsoft 和協力廠商服務的矩陣，以及可用來協助您進行各種資料庫和資料移轉案例以及專長工作的工具，請參閱 [資料移轉的服務和工具](../../../dms/dms-tools-matrix.md)。

- 若要深入瞭解 SQL Database，請參閱：
    - [Azure SQL Database 總覽](../../database/sql-database-paas-overview.md)
   - [Azure 擁有權總成本計算機](https://azure.microsoft.com/pricing/tco/calculator/) 


- 若要深入瞭解雲端遷移的架構和採用週期，請參閱
   -  [適用於 Azure 的雲端採用架構](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [將工作負載的成本與大小調整遷移至 Azure 的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 若要評估應用程式存取層，請參閱 [Data Access Migration Toolkit (預覽) ](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 如需有關如何執行資料存取層 A/B 測試的詳細資訊，請參閱 [資料庫測試助理](/sql/dea/database-experimentation-assistant-overview)。
