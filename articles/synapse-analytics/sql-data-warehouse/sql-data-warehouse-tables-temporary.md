---
title: 暫存資料表
description: 在 Synapse SQL 集區中使用臨時表的基本指引，其中反白顯示工作階段層級臨時表的原則。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408035"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Synapse SQL 集區中的臨時表
本文包含使用暫存資料表的基本指引，並強調說明工作階段層級暫存資料表的原則。 

使用本文中的資訊可協助您模組化程式碼，同時提升重複使用性和輕鬆維護的便利性。

## <a name="what-are-temporary-tables"></a>什麼是暫存資料表？
臨時表在處理資料時很有用，尤其是在轉換期間，中繼結果是暫時性的。 在 SQL 集區中，臨時表存在於工作階段層級。  

只有在建立臨時表時，才看得到它們，並會在該會話登出時自動卸載。  

暫存資料表的結果會寫入至本機，而不是遠端儲存體，這是它的效能優點。

臨時表在處理資料時很有用，尤其是在轉換期間，中繼結果是暫時性的。 使用 SQL 分析時，臨時表存在於工作階段層級。  只有在建立它們的會話才會看到它們。 如此一來，當該會話登出時，就會自動卸載它們。 

## <a name="temporary-tables-in-sql-pool"></a>SQL 集區中的臨時表

在 SQL 集區資源中，臨時表會提供效能優勢，因為它們的結果會寫入本機而不是遠端儲存體。

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

如果您要呼叫相同的預存程式，這會建立具有相同名稱的暫存，為了確保`CREATE TABLE`語句成功，使用的簡單預先存在檢查`DROP`可如下列範例所示：

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

針對程式碼一致性，將此模式用於資料表和臨時表是很好的作法。  當您在程式碼中完成使用`DROP TABLE`臨時表時，也最好使用來移除它們。  

在預存程式開發中，通常會在程式結束時看到連結在一起的 drop 命令，以確保清除這些物件。

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>模組化程式碼
因為臨時表可以在使用者會話中的任何位置看到，所以可以利用這項功能來協助您模組化應用程式程式碼。  

例如，下列預存程式會產生 DDL，以依據統計資料名稱來更新資料庫中的所有統計資料：

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

在這個階段，唯一發生的動作是建立使用 DDL 語句來產生臨時表（#stats_ddl）的預存程式。  

這個預存程式會卸載現有的 #stats_ddl，以確保在會話中執行一次以上時，它不會失敗。  

不過，因為預存程序結尾沒有任何 `DROP TABLE`，當預存程序完成時，它會保留建立的資料表，以便能夠從預存程序之外讀取。  

在 SQL 集區中，與其他 SQL Server 資料庫不同的是，可以在建立它的程式之外使用臨時表。  SQL 集區臨時表可以在會話內的**任何位置**使用。 這項功能可能會導致更多模組化且可管理的程式碼，如下列範例所示：

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
SQL 集區在執行臨時表時，會強制執行幾項限制。  目前，僅支援工作階段範圍內的暫存資料表。  不支援全域臨時表。  

此外，您也無法在臨時表上建立 views。  只能使用雜湊或迴圈配置資源散發來建立臨時表。  不支援複寫的臨時表散發。 

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何開發資料表，請參閱[使用 SQL 分析資源設計資料表](sql-data-warehouse-tables-overview.md)一文。

