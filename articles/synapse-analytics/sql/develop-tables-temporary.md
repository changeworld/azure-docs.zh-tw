---
title: 在 Synapse SQL 中使用臨時表
description: 在 Synapse SQL 中使用臨時表的基本指引。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 9e871b7959c11d7fbae650abf8394811b9088ed1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020555"
---
# <a name="temporary-tables-in-synapse-sql"></a>Synapse SQL 中的臨時表

本文包含使用臨時表的基本指引，並強調 Synapse SQL 中工作階段層級臨時表的原則。 

SQL 集區和 SQL 隨選（預覽）資源都可以利用臨時表。 SQL 隨選的限制會在本文結尾處討論。 

## <a name="what-are-temporary-tables"></a>什麼是暫存資料表？

暫存資料表在處理資料時很有用，尤其是具有暫時性中繼結果的轉換期間。 在 Synapse SQL 中，臨時表存在於工作階段層級。  只有出現在其建立所在的工作階段。 如此一來，當該工作階段登出時，就會自動將其卸除。 

## <a name="temporary-tables-in-sql-pool"></a>SQL 集區中的暫存資料表

在 SQL 集區資源中，暫存資料表的結果會寫入至本機，而不是遠端儲存體，這是其效能優點。

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

### <a name="dropping-temporary-tables"></a>捨棄暫存資料表
建立新的工作階段時，不應該存在任何暫存資料表。  不過，如果您呼叫的相同預存程式會建立具有相同名稱的臨時表，若要確保 `CREATE TABLE` 語句成功，請搭配使用簡單的預先存在檢查 `DROP` ： 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

為了維持編寫程式碼的一致性，資料表和暫存資料表最好都採用此模式。  `DROP TABLE`當您完成使用臨時表時，也最好使用來移除它們。  

在預存程序開發期間，在程序結尾一併搭配 drop 命令以確保會清除這些物件，也是常見的做法。

```sql
DROP TABLE #stats_ddl
```

### <a name="modularizing-code"></a>模組化程式碼
在使用者會話中的任何位置都可以使用臨時表。 如此一來，就可以利用這項功能來協助您模組化應用程式的程式碼。  為了示範，下列預存程式會產生 DDL，以依據統計資料名稱來更新資料庫中的所有統計資料：

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

在這個階段，唯一發生的動作是建立會產生 #stats_ddl 臨時表的預存程式。  預存程式會卸載 #stats_ddl （如果已經存在）。 如果在會話中執行一次以上，此卸載可確保它不會失敗。  

因為 `DROP TABLE` 預存程式結尾沒有，當預存程式完成時，建立的資料表會保留，而且可以在預存程式之外讀取。  

相較于其他 SQL Server 資料庫，Synapse SQL 可讓您在建立它的程式以外使用臨時表。  透過 SQL 集區建立的臨時表可以在會話內的**任何位置**使用。 因此，您將會有更多模組化和可管理的程式碼，如下列範例所示：

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

### <a name="temporary-table-limitations"></a>暫存資料表限制

SQL 集區對臨時表有幾個執行限制：

- 僅支援會話範圍的臨時表。  不支援全域暫存資料表。
- 無法在臨時表上建立 Views。
- 只能使用雜湊或循環配置資源散發來建立暫存資料表。  不支援複寫的暫存資料表散發。 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>隨選 SQL 中的臨時表（預覽）

支援 SQL 隨選中的臨時表，但其使用方式有限。 它們不能用在目標檔案的查詢中。 

例如，您無法聯結具有儲存體中檔案資料的臨時表。 臨時表的數目限制為100，而其大小總計限制為 100 MB。

## <a name="next-steps"></a>後續步驟

若要深入了解如何開發資料表，請參閱[使用 Synapse SQL 資源設計資料表](develop-tables-overview.md)一文。

