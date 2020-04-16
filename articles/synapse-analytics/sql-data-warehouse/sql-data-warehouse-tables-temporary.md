---
title: 暫存資料表
description: 在 Synapse SQL 池中使用暫時長表的基本指南,突出顯示工作階段級別暫時臨時表的原則。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 56d8ab81fcf9200fec2cfb4a741724b8f79db820
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408035"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Synapse SQL 池中的暫存表
本文包含使用暫存資料表的基本指引，並強調說明工作階段層級暫存資料表的原則。 

使用本文中的資訊可以説明您模組化代碼,提高可重用性和易於維護性。

## <a name="what-are-temporary-tables"></a>什麼是暫存資料表？
臨時表在處理數據時很有用,尤其是在轉換期間,中間結果是暫時的。 在 SQL 池中,臨時表存在於會話級別。  

臨時表僅對創建臨時表的會話可見,並且當該會話註銷時會自動刪除。  

暫存資料表的結果會寫入至本機，而不是遠端儲存體，這是它的效能優點。

臨時表在處理數據時很有用,尤其是在轉換期間,中間結果是暫時的。 使用 SQL 分析,臨時表存在於會話級別。  它們僅在創建它們的會話中可見。 因此,當會話註銷時,它們會自動丟棄。 

## <a name="temporary-tables-in-sql-pool"></a>SQL 池中的暫時

在 SQL 池資源中,臨時表提供性能優勢,因為它們的結果寫入本地存儲而不是遠端存儲。

### <a name="create-a-temporary-table"></a>建立暫存資料表

建立暫存資料表時會在資料表名稱前面加上 `#`。  例如：

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

`CTAS` 也可用來建立暫存資料表，方法完全相同：

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` 是一個強大的命令，可有效率地使用交易記錄空間是它額外的好處。 
> 
> 

## <a name="dropping-temporary-tables"></a>捨棄暫存資料表
建立新的工作階段時，不應該存在任何暫存資料表。  

如果要呼叫同一儲存過程(建立具有相同名稱的臨時過程,以確保`CREATE TABLE`語句成功,則使用`DROP`a 的簡單存在前檢查可以使用,如以下範例所示:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

對於編碼一致性,最好將此模式同時用於表和臨時表。  在代碼中完成臨時表後,最好`DROP TABLE`使用它刪除臨時表。  

在存儲過程開發中,通常可以看到放置命令在過程結束時捆綁在一起,以確保清理這些物件。

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>模組化程式碼
由於在使用者作業階段的任意位置都可以看到臨時表,因此可以利用此功能説明您模組化應用程式代碼。  

例如,以下儲存過程生成 DDL 以按統計名稱更新資料庫中的所有統計資訊:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

在此階段,發生的唯一操作是創建一個存儲過程,該過程生成具有 DDL 語句的臨時表#stats_ddl。  

此存儲過程將刪除現有#stats_ddl以確保在會話中運行多次時不會失敗。  

不過，因為預存程序結尾沒有任何 `DROP TABLE`，當預存程序完成時，它會保留建立的資料表，以便能夠從預存程序之外讀取。  

在 SQL 池中,與其他 SQL Server 資料庫不同,可以在創建它的過程之外使用臨時表。  SQL 池臨時表可以在會話內的**任意位置**使用。 此功能可以帶來更模組化和可管理的代碼,如以下範例所示:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>暫存資料表限制
在實現臨時表時,SQL 池確實會施加一些限制。  目前，僅支援工作階段範圍內的暫存資料表。  不支援全域臨時表。  

此外,無法在臨時表上創建檢視。  只能使用哈希或迴圈分佈創建臨時表。  不支援複製的臨時表分發。 

## <a name="next-steps"></a>後續步驟

要瞭解有關開發表的更多資訊,請參閱使用[SQL Analytics 資源設計表](sql-data-warehouse-tables-overview.md)一文。

