---
title: DB2 到 SQL Database-遷移指南
description: 遵循本指南來將您的 DB2 資料庫移轉至 Azure SQL Database。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: bc7db167ed1d1d8823e90bf422f17428a7ed4e48
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496646"
---
# <a name="migration-guide-db2-to-sql-database"></a>遷移指南： DB2 至 SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

本指南將指導您如何使用 SQL Server 移轉小幫手 for DB2 將 DB2 資料庫移轉至 Azure SQL Database。 

如需其他案例，請參閱 [資料庫移轉指南](https://datamigration.microsoft.com/)。

## <a name="prerequisites"></a>先決條件 

若要將您的 DB2 資料庫移轉至 SQL Database，您需要：

- 驗證是否支援來源環境。
- 下載[適用於 DB2 的 SQL Server 移轉小幫手 (SSMA)](https://www.microsoft.com/download/details.aspx?id=54254) (英文)。
- 目標 [Azure SQL Database](../../database/single-database-create-quickstart.md)。


## <a name="pre-migration"></a>移轉前

符合先決條件後，您即可開始探索環境的拓撲，並評定移轉的可行性。 

### <a name="assess-and-convert"></a>評定及轉換

使用 SQL Server 移轉小幫手 (SSMA) 建立評定。 

若要建立評定，遵循以下步驟：

1. 開啟適用於 DB2 的 SQL Server 移轉小幫手 (SSMA)。 
1. 選取 [檔案]，然後選擇 [新增專案]。 
1. 提供專案名稱、儲存專案的位置，然後從下拉式清單中選取 [Azure SQL Database] 作為遷移目標。 選取 [確定]。  

   :::image type="content" source="media/db2-to-sql-database-guide/new-project.png" alt-text="提供專案詳細資料，然後選取 [確定] 以儲存。":::


1. 在 [連線至 DB2] 對話方塊上，輸入 DB2 連線詳細資料的值。 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-db2.png" alt-text="連線至 DB2 執行個體":::


1. 以滑鼠右鍵按一下所要移轉的 DB2 結構描述，然後選擇 [建立報表]。 這會產生 HTML 報表。 或者，您也可以在選取結構描述之後，從導覽列選擇 [建立報表]。 

   :::image type="content" source="media/db2-to-sql-database-guide/create-report.png" alt-text="以滑鼠右鍵按一下結構描述，然後選擇 [建立報表]":::

1. 請檢閱 HTML 報表，以了解轉換統計資料以及任何錯誤或警告。 您也可以在 Excel 中開啟報表，以取得 DB2 物件的詳細目錄，以及執行結構描述轉換所需的工作。 報表其預設位置是在 SSMAProjects 內的報表資料夾中。

   例如： `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>` 。 

   :::image type="content" source="media/db2-to-sql-database-guide/report.png" alt-text="檢閱報告以識別任何錯誤或警告":::


### <a name="validate-data-types"></a>驗證資料類型

驗證預設資料類型對應，並視需要來根據需求加以變更。 若要這樣做，請依照下列步驟執行： 

1. 從功能表中選取 [工具]。 
1. 選取 [專案設定]。 
1. 選取 [類型對應] 索引標籤。 

   :::image type="content" source="media/db2-to-sql-database-guide/type-mapping.png" alt-text="選取 [結構描述]，然後鍵入對應":::

1. 您可藉由選取 [DB2 Metadata Explorer] 中資料表來變更每個資料表的類型對應。 

### <a name="schema-conversion"></a>結構描述轉換 

若要轉換結構描述，請遵循以下步驟：

1. (選擇性) 將動態或臨機操作查詢新增至陳述式。 以滑鼠右鍵按一下節點，然後選擇 [新增陳述式]。 
1. 選取 **[連接到 Azure SQL Database]** 。 
    1. 輸入連線詳細資料，以便在 Azure SQL Database 中連接您的資料庫。 
    1. 從下拉式清單中選擇您的目標 SQL Database。 
    1. 選取 [連接]  。 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-sql-database.png" alt-text="填入詳細資料，以連接到 Azure 中的邏輯伺服器":::


1. 使用滑鼠右鍵按一下結構描述，然後選取 [轉換結構描述]。 或者，您也可以在選取結構描述之後，從上方導覽列選擇 [轉換結構描述]。 

   :::image type="content" source="media/db2-to-sql-database-guide/convert-schema.png" alt-text="使用滑鼠右鍵按一下結構描述，然後選取 [轉換結構描述]":::

1. 轉換完成之後，請比較並檢閱結構描述的結構，以找出潛在的問題，並根據建議加以解決。 

   :::image type="content" source="media/db2-to-sql-database-guide/compare-review-schema-structure.png" alt-text="請比較並檢閱結構描述的結構，以找出潛在的問題，並根據建議加以解決。":::

1. 將專案儲存在本機，以進行離線結構描述補救練習。 從 [檔案] 功能表中選取 [儲存專案]。 


## <a name="migrate"></a>遷移

在完成資料庫的評定並解決任何不一致的情況之後，下一步就是執行移轉程序。

若要發佈結構描述並移轉資料，請遵循以下步驟：

1. 發行架構：以滑鼠右鍵按一下 **Azure SQL Database 中繼資料瀏覽器** 之 [ **資料庫** ] 節點中的資料庫，然後選擇 [ **與資料庫同步** ]。

   :::image type="content" source="media/db2-to-sql-database-guide/synchronize-with-database.png" alt-text="以滑鼠右鍵按一下資料庫，然後選擇 [與資料庫同步處理]":::

1. 移轉資料：以滑鼠右鍵按一下 [DB2 Metadata Explorer] 中的結構描述，然後選擇 [移轉資料]。 

   :::image type="content" source="media/db2-to-sql-database-guide/migrate-data.png" alt-text="以滑鼠右鍵按一下結構描述，然後選擇 [移轉資料]":::

1. 提供 DB2 和 Azure SQL Database 的連接詳細資料。 
1. 查看 **資料移轉報表** 。 

   :::image type="content" source="media/db2-to-sql-database-guide/data-migration-report.png" alt-text="檢閱資料移轉報表":::

1. 使用 SQL Server Management Studio 連接到您的 Azure SQL Database，並藉由檢查資料和架構來驗證遷移。 

   :::image type="content" source="media/db2-to-sql-database-guide/compare-schema-in-ssms.png" alt-text="比較 SSMS 中的結構描述":::

## <a name="post-migration"></a>移轉後 

在成功完成移轉階段之後，您需要進行一系列的後續移轉工作，以確保所有項目都能順暢且有效率地運作。

### <a name="remediate-applications"></a>修復應用程式 

將資料移轉至目標環境之後，先前取用來源的所有應用程式都必須開始取用目標。 在某些情況下，完成這項工作需要對應用程式進行變更。


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

## <a name="migration-assets"></a>移轉資產 

如需其他協助，請參閱下列資源，這是為了支援實際的移轉專案行動而開發的：

|資產  |說明  |
|---------|---------|
|[資料工作負載評定模型及工具](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| 此工具會針對指定的工作負載，提供建議的「最適合」目標平台、雲端整備，以及應用程式/資料庫補救等級。 此工具提供簡單的按一下即計算與報告產生功能，其可透過提供和自動化的統一目標平台決策程序來協助加速大型資產評估。|
|[DB2 zOS 資料資產探索與評定套件](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|在資料庫上執行 SQL 指令碼之後，您可將結果匯出至檔案系統上的檔案。 支援數種檔案格式，包括 *.csv，這可供在外部工具 (如試算表) 中擷取結果。 如果想要輕鬆地與未安裝 Workbench 的小組共用結果，這個方法會很有用。|
|[IBM DB2 LUW 詳細目錄指令碼及成品](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts) (英文)|此資產包含一個 SQL 查詢，其會叫用 IBM DB2 LUW 11.1 版系統資料表，並依結構描述及物件類型提供物件的計數、每個結構描述中「原始資料」的粗略估計值，以及每個結構描述中的資料表大小，並以 CSV 格式儲存結果。|
|[Azure 上 DB2 LUW 單純擴充 - 設定指南](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|本指南可作為 DB2 實作計畫的起點。 雖然商業需求將會不同，但相同的模式皆適用。 此架構模式也可用於 Azure 上的 OLAP 應用程式。|

這些資源是在資料 SQL Ninja 計畫中開發，由 Azure 資料群組工程小組贊助。 資料 SQL Ninja 計畫其核心宗旨是要為複雜的現代化步驟除去障礙並加速其過程，並將資料平台移轉機會與 Microsoft 的 Azure 資料平台相比較。 如果您認為組織想參與資料 SQL Ninja 計畫，請連絡帳戶小組並要求其提交提名。



## <a name="next-steps"></a>後續步驟

- 如需 Microsoft 和協力廠商服務的矩陣，以及可用來協助您進行各種資料庫和資料移轉案例以及專長工作的工具，請參閱 [資料移轉的服務和工具](../../../dms/dms-tools-matrix.md)。

- 若要深入了解 Azure SQL Database，請參閱：
   - [SQL Database 總覽](../../database/sql-database-paas-overview.md)
   - [Azure 擁有權總成本計算機](https://azure.microsoft.com/pricing/tco/calculator/) 


- 若要深入瞭解雲端遷移的架構和採用週期，請參閱
   -  [適用於 Azure 的雲端採用架構](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [將工作負載的成本與大小調整遷移至 Azure 的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 若要評估應用程式存取層，請參閱 [Data Access Migration Toolkit (預覽) ](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- 如需有關如何執行資料存取層 A/B 測試的詳細資訊，請參閱 [資料庫測試助理](/sql/dea/database-experimentation-assistant-overview)。
