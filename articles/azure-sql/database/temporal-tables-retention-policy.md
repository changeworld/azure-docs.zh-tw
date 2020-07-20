---
title: 管理時態表中的歷程記錄資料
description: 了解如何使用時態保留原則來控制歷史資料。
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: 8c5ea1f7ef094944c3e5a20dd19bce6d8cce294d
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985438"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>使用保留原則管理時態表中的歷程記錄資料
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

時態表可能會增加比一般資料表更多的資料庫大小，特別是當您保留歷程記錄資料一段較長的時間時。 因此，在規劃及管理每個時態表的生命週期時，歷史資料的保留原則很重要。 Azure SQL Database 和 Azure SQL 受控執行個體中的時態表附有便於使用的保留機制，可協助您完成這項工作。

時態歷程記錄保留可在不同的資料表層級上設定，讓使用者建立彈性的過時原則。 套用暫時保留很簡單：只需要在資料表建立或結構描述變更期間設定一個參數。

定義保留原則之後，Azure SQL Database 和 Azure SQL 受控執行個體會在有適合自動清除資料的歷程記錄資料列時，定期開始檢查。 識別相符資料列及從歷程記錄資料表中移除它們的作業，會以明確的方式在系統排程和執行的背景工作中進行。 會根據代表 SYSTEM_TIME 期間結束的資料行，檢查歷程記錄資料表資料列的存留期條件。 如果保留期限 (例如，設定為六個月) 資料表資料列符合清除資格，請滿足下列條件：

```sql
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

在上述範例中，我們假設**ValidTo**資料行對應至 SYSTEM_TIME 期限的結尾。

## <a name="how-to-configure-retention-policy"></a>如何設定保留原則？

在您設定時態表的保留原則之前，請先檢查是否已*在資料庫層級*啟用時態性歷程記錄保留。

```sql
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

根據預設，資料庫旗標 **is_temporal_history_retention_enabled** 設為 ON，但使用者可利用 ALTER DATABASE 陳述式來變更它。 它也會在[還原時間點](recovery-using-backups.md)作業之後自動設為 OFF。 若要為您的資料庫啟用時態歷程記錄保留清除功能，請執行下列陳述式：

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> 即使 **is_temporal_history_retention_enabled** 為 OFF，您還是可以設定時態表的保留，但在此情況下，不會觸發自動清除過時的資料列。

藉由指定 HISTORY_RETENTION_PERIOD 參數值，在資料表建立期間設定保留原則：

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Azure SQL Database 和 Azure SQL 受控執行個體可讓您使用不同的時間單位來指定保留期限：天、周、月和年。 如果省略了 HISTORY_RETENTION_PERIOD，則會假設 INFINITE 保留。 您也可以明確地使用 INFINITE 關鍵字。

在某些狀況中，您可能想要在資料表建立後設定保留，或變更先前設定的值。 在此情況下，請使用 ALTER TABLE 陳述式：

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> 將 SYSTEM_VERSIONING 設定為 OFF「不會保留」** 保留期限值。 如果將 SYSTEM_VERSIONING 設為 ON，但未明確指定 HISTORY_RETENTION_PERIOD，則會形成 INFINITE 保留期限。

若要檢閱目前的保留原則狀態，請使用下列查詢，以聯結資料庫層級的暫時保留啟用旗標與個別資料表的保留期間：

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```

## <a name="how-ages-rows-are-deleted"></a>資料列刪除的頻率

清除處理序取決於歷程記錄資料表的索引配置。 請務必注意*只有包含叢集索引 (B 型樹狀目錄或資料行存放區）的歷程記錄資料表可以設定有限的保留原則*。 建立的背景工作可利用有限的保留期間為所有時態表執行過時資料清除。
資料列存放區 (B 型樹狀目錄) 叢集索引的清除邏輯會以較小區塊刪除過時資料列 (最多 10K)，以儘量減輕資料庫記錄檔和 IO 子系統的壓力。 雖然清除邏輯會利用必要的 B 型樹狀目錄索引，但無法絶對保證早於保留期間之資料列的刪除順序。 因此，*請勿在應用程式中對清除順序採用任何相依性*。

叢集資料行存放區的清除工作會一次移除整個資料列[群組](/sql/relational-databases/indexes/columnstore-indexes-overview)（通常會包含每個資料列的1000000），這非常有效率，特別是當歷程記錄資料是以較高的速度產生時。

![叢集資料行存放區保留](./media/temporal-tables-retention-policy/cciretention.png)

卓越的資料壓縮和有效率的保留清除，可讓叢集資料行存放區索引成為您的工作負載快速產生大量歷程記錄資料時的完美選擇。 此模式通常用於需要[使用時態表的大量交易處理工作負載](/sql/relational-databases/tables/temporal-table-usage-scenarios)，以追蹤和稽核變更、分析趨勢，或擷取 IoT 資料。

## <a name="index-considerations"></a>索引考量

對於具有資料列存放區叢集索引的資料表，清除工作需有從對應 SYSTEM_TIME 時段結束的資料行開始的索引。 如果沒有這種索引，您就無法設定有限保留期限：

*訊息13765，層級16，狀態 1 <br> </br> 設定有限的保留期間在系統建立版本的時態表 ' Temporalstagetestdb.dbo.websiteuserinfohistory. WebsiteUserInfo ' 上失敗，因為記錄資料表 ' temporalstagetestdb.dbo.websiteuserinfohistory ' 不包含所需的叢集索引。請考慮建立叢集資料行存放區或 B 型樹狀目錄索引，其開頭為符合記錄資料表上 SYSTEM_TIME 期間結尾的資料行。*

請務必注意，Azure SQL Database 和 Azure SQL 受控執行個體所建立的預設歷程記錄資料表已經有叢集索引，其符合保留原則。 如果您嘗試在設定有限保留期限的資料表上移除該索引，作業會失敗並傳回下列錯誤︰

*訊息13766，層級16，狀態1無法卸載叢集 <br> </br> 索引 ' IX_WebsiteUserInfoHistory WebsiteUserInfoHistory '，因為它正用於自動清除過時資料。如果您需要卸載此索引，請在對應的系統版本設定時態表上，考慮將 HISTORY_RETENTION_PERIOD 設為無限。*

如果歷史資料列依遞增順序插入 (依時段結束資料行排序)，完全以 SYSTEM_VERSIONIOING 機制填入記錄資料表時就是如此，則會以最佳方式清除叢集資料行存放區索引。 如果記錄資料表中的資料列未依時段結束資料行排序 (可能是您已移轉現有的歷史資料)，您應該在已正確排序的 B 型樹狀目錄資料列存放區索引上，重新建立叢集資料行存放區索引，以發揮最佳效能。

在設定有限保留期限的記錄資料表上，請避免重建叢集資料行存放區索引，因為這可能會變更系統版本控制作業在資料列群組中自然強加的順序。 如果您需要在記錄資料表上重建叢集資料行存放區索引，請在相容的 B 型樹狀目錄索引之外重建，讓需要定期清除資料的資料列群組中可以保留順序。 如果您使用具有叢集資料行索引但不保證資料順序的現有記錄資料表來建立時態表，則應該採取相同的方法︰

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

當具有叢集資料行存放區索引的記錄資料表已設定有限保留期限時，您無法在該資料表上建立其他非叢集 B 型樹狀目錄索引︰

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

嘗試執行上述陳述式失敗並傳回下列錯誤︰

*訊息13772，層級16，狀態 1 <br> </br> 無法在時態性歷程記錄資料表 ' WebsiteUserInfoHistory ' 上建立非叢集索引，因為它已定義有限的保留週期和叢集資料行存放區索引。*

## <a name="querying-tables-with-retention-policy"></a>使用保留原則來查詢資料表

時態表上的所有查詢會自動篩選掉符合有限保留原則的歷史資料列，以避免非預期和不一致的結果，因為清除工作可以「隨時以任意順序」** 刪除過時資料列。

下圖顯示簡單查詢的查詢計劃︰

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

查詢計劃包含額外的篩選，在「叢集索引掃描」運算子中套用到記錄資料表 (反白顯示) 的時段結束資料行 (ValidTo)。 這個範例假設 WebsiteUserInfo 資料表上已設定一個 MONTH 保留期限。

![保留查詢篩選](./media/temporal-tables-retention-policy/queryexecplanwithretention.png)

不過，如果您直接查詢記錄資料表，您可能會看到超過指定保留期限的資料列，但完全不保證會得到重複的查詢結果。 下圖顯示在記錄資料表上查詢的查詢執行計劃，但未套用額外篩選︰

![在不使用保留篩選的情況下查詢記錄](./media/temporal-tables-retention-policy/queryexecplanhistorytable.png)

請勿依賴商務邏輯來讀取超出保留期限的記錄資料表，否則可能會得到不一致或非預期的結果。 建議以 FOR SYSTEM_TIME 子句來使用暫時查詢，以分析時態表中的資料。

## <a name="point-in-time-restore-considerations"></a>還原時間點考量

當您[將現有資料庫還原到特定時間點](recovery-using-backups.md)來建立新的資料庫時，資料庫層級上會停用時態保留  (**is_temporal_history_retention_enabled** 旗標設為 OFF)。 這項功能可讓您在還原時檢查所有歷史資料列，而不必擔心過時資料列在您開始查詢之前就被移除。 這可用來「檢查超出設定保留期限的歷史資料」**。

假設時態表已指定一個 MONTH 保留期限。 如果是在高階服務層中建立資料庫，您可以使用過去最多 35 天的資料庫狀態來建立資料庫副本。 實際上，這可讓您直接查詢記錄資料表，以分析過去最多 65 天的歷史資料列。

如果您想要啟動時態保留清除，請在還原時間點之後執行下列 TRANSACT-SQL 陳述式︰

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>後續步驟

若要瞭解如何在您的應用程式中使用時態表，請參閱[使用時態表消費者入門](../temporal-tables.md)。

流覽 Channel 9 以聆聽[客戶的時態性實現成功案例](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions)，並觀看[即時的時態性示範](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)。

如需有關時態表的詳細資訊，請參閱[時態表](/sql/relational-databases/tables/temporal-tables)。
