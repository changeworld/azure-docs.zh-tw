---
title: 開始使用時態表
description: 瞭解如何開始使用 Azure SQL Database 和 Azure SQL 受控執行個體中的時態表。
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 06/26/2019
ms.openlocfilehash: ea037d12417c8fad9d80b77df69285ed2c8df31b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618653"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database-and-azure-sql-managed-instance"></a>開始使用 Azure SQL Database 和 Azure SQL 受控執行個體中的時態表
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

時態表是 Azure SQL Database 和 Azure SQL 受控執行個體的可程式性功能，可讓您追蹤和分析資料變更的完整歷程記錄，而不需要自訂程式碼。 時態表會讓資料與時間內容緊密相關，以便將儲存的事實解讀成隻在特定期間內有效。 時態表的這個屬性可讓您有效率地進行以時間為基礎的分析，並從資料演進中取得見解。

## <a name="temporal-scenario"></a>時態性案例

本文說明在應用程式案例中使用時態表的步驟。 假設您想要從頭開始追蹤正在開發的新網站上的使用者活動，或您想要使用使用者活動分析擴充的現有網站上的使用者活動。 在這個簡化的範例中，我們假設在一段時間內流覽過的網頁數目是一個指標，必須在裝載于 Azure SQL Database 或 Azure SQL 受控執行個體的網站資料庫中加以捕獲和監視。 使用者活動歷史分析的目標是要獲得重新設計網站的意見，並為訪客提供更好的經驗。

此案例的資料庫模型非常簡單：使用者活動度量是以單一整數欄位 **PageVisited** 表示，而且會與使用者設定檔上的基本資訊一起被擷取。 此外，對於以時間為基礎的分析，您要為每個使用者保留一連串的資料列，其中每個資料列都代表一段特定時間內特定使用者瀏覽過的頁數。

![結構描述](./media/temporal-tables/AzureTemporal1.png)

幸運的是，您不需要在您的 app 上花太多精力，就可以維護此活動資訊。 使用時態表時，此程式會自動化，讓您在網站設計期間提供完整的彈性，並更多時間專注于資料分析本身。 您只需要確保將 **WebSiteInfo** 資料表設定為 [時態系統設定版本](/sql/relational-databases/tables/temporal-tables#what-is-a-system-versioned-temporal-table)。 在此案例中使用時態表的確切步驟如下所述。

## <a name="step-1-configure-tables-as-temporal"></a>步驟 1︰將資料表設定為時態表

根據您要開始新的開發工作，還是升級現有的應用程式，您將會建立時態表，或透過新增時態屬性來修改現有的資料表。 在一般情況下，您的案例可能會混用這兩個選項。 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) 、 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) (SSDT) 、 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)或任何其他 transact-sql 開發工具來執行這些動作。

> [!IMPORTANT]
> 建議您一律使用最新版本的 Management Studio，以與 Azure SQL Database 和 Azure SQL 受控執行個體的更新保持同步。 [更新 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)。

### <a name="create-new-table"></a>建立新資料表

在 SSMS 的 [物件總管] 中使用內容功能表項目 [新系統設定版本的資料表] 開啟查詢編輯器與時態表範本指令碼，然後使用 [指定範本參數的值] \(Ctrl + Shift + M) 填入範本：

![SSMSNewTable](./media/temporal-tables/AzureTemporal2.png)

在 SSDT 中，將新項目新增至資料庫專案時，請選擇 [時態表 (系統建立版本)] 範本。 這將會開啟資料表設計工具，並讓您輕鬆地指定資料表配置︰

![SSDTNewTable](./media/temporal-tables/AzureTemporal3.png)

您也可以透過直接指定 Transact-SQL 陳述式來建立時態表，如下列範例所示。 請注意，每個時態表的必要元素為 PERIOD 定義以及可參照將儲存歷史資料列版本的另一個使用者資料表的 SYSTEM_VERSIONING 子句︰

```sql
CREATE TABLE WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

當您建立系統設定版本的時態表時，會自動建立隨附預設組態的歷程記錄資料表。 預設的歷程記錄資料表包含期間資料行 (結束、開始) 上啟用頁面壓縮的叢集 B 型樹狀目錄索引。 此組態適合使用時態表的大部分案例，特別是用於 [資料稽核](/sql/relational-databases/tables/temporal-table-usage-scenarios#enabling-system-versioning-on-a-new-table-for-data-audit)。

在此特殊案例中，我們的目標是針對一段較長的資料歷程記錄以及較大的資料集，執行以時間為基礎的趨勢分析，因此歷程記錄表格的儲存體選擇為叢集資料行存放區索引。 叢集資料行存放區為分析查詢提供很好的壓縮和效能。 時態表可讓您彈性地在目前的和時態表上，完全獨立地設定索引。

> [!NOTE]
> 資料行存放區索引適用于商務關鍵性、一般用途和進階層，以及標準層（S3 和更新版本）。

下列指令碼示範如何將歷程記錄資料表的預設索引變更為叢集資料行存放區︰

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

時態表會在物件總管中以特定的圖示呈現，以方便識別，而其歷程記錄資料表會顯示為子節點。

![AlterTable](./media/temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>將現有的資料表變更為時態表

讓我們來看看替代案例，其中 WebsiteUserInfo 資料表已存在，但不是針對保留變更的歷程記錄而設計。 在此情況下，您只能擴充現有的資料表，使其成為時態表，如以下範例所示︰

```sql
ALTER TABLE WebsiteUserInfo
ADD
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo);

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

## <a name="step-2-run-your-workload-regularly"></a>步驟 2：定期執行您的工作負載

時態表的主要優點是您不需要以任何方式變更或調整您的網站來執行變更追蹤。 建立之後，時態表會在您每次對資料執行修改時，以透明的方式保存先前的資料列版本。

為了在此特定案例中使用自動變更追蹤，讓我們在每次使用者于網站上結束會話時，更新資料行 **PagesVisited** ：

```sql
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5
WHERE [UserID] = 1;
```

請務必注意，更新查詢不需要知道實際作業進行的時間，也不需要知道如何保留歷史資料以供未來分析之用。 這兩個層面都會由 Azure SQL Database 和 Azure SQL 受控執行個體自動處理。 下圖說明如何在每次更新時產生歷程記錄資料。

![TemporalArchitecture](./media/temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>步驟 3︰執行歷史資料分析

現在當時態系統設定版本功能啟用時，您只需要一個查詢，就可以進行歷史資料分析。 在本文中，我們將提供一些解決常見分析案例的範例。若要了解所有詳細資料，請瀏覽使用 [FOR SYSTEM_TIME](/sql/relational-databases/tables/temporal-tables#how-do-i-query-temporal-data) 子句所導入的各種選項。

若要查看依瀏覽網頁次數排序的前 10 名使用者，請執行以下查詢︰

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

您可以輕鬆地修改此查詢，以分析截至一天前、一個月前，或您希望的任何過去時間點的網站瀏覽記錄。

若要進行前一天的基本統計分析，請使用以下範例︰

```sql
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

若要搜尋特定使用者在某段時間的活動，請使用 CONTAINED IN 子句︰

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

圖形視覺效果對於時態查詢特別方便，因為您可以以直覺方式，非常輕鬆地顯示趨勢和使用模式︰

![TemporalGraph](./media/temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>不斷演進的資料表結構描述

一般而言，您必須在開發 app 的同時，變更時態表結構描述。 如此一來，只要執行一般的 ALTER TABLE 語句和 Azure SQL Database 或 Azure SQL 受控執行個體就會適當地將變更傳播到歷程記錄資料表。 下列指令碼示範如何新增要追蹤的其他屬性︰

```sql
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

同樣地，您可以在您的工作負載正在作用中時，變更資料行定義︰

```sql
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

最後，您可以移除您不再需要的資料行。

```sql
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo
    DROP COLUMN TemporaryColumn;
```

或者，當您連線到資料庫 (線上模式)，或您屬於資料庫專案的一部分 (離線模式) 時，使用最新的 [SSDT](/sql/ssdt/download-sql-server-data-tools-ssdt) 變更時態表結構描述。

## <a name="controlling-retention-of-historical-data"></a>控制歷史資料的保留期

透過系統設定版本的時態表，歷程記錄資料表可以將資料庫大小增加到超過一般資料表。 對於不斷成長的大型歷程記錄資料表來說，單是儲存成本和加諸於時態查詢之上的效能負擔就可能會造成問題。 因此，開發資料保留原則來管理歷程記錄資料表中的資料是規劃及管理每個時態表生命週期的重要環節。 使用 Azure SQL Database 和 Azure SQL 受控執行個體，您可以使用下列方法來管理時態表中的歷程記錄資料：

- [資料表資料分割](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-table-partitioning-approach)
- [自訂清除指令碼](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-custom-cleanup-script-approach)

## <a name="next-steps"></a>後續步驟

- 如需時態表的詳細資訊，請參閱簽出 [時態表](/sql/relational-databases/tables/temporal-tables)。
- 流覽 Channel 9，聽聽 [客戶的時態性實現成功案例](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) ，並觀看 [即時的時態示範](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)。
