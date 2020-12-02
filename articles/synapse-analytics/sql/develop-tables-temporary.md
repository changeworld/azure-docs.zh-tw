---
title: 在 Synapse SQL 中使用臨時表
description: 在 Synapse SQL 中使用臨時表的基本指引。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06faa1da71331c299245a93af96166880e7732de
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451781"
---
# <a name="temporary-tables-in-synapse-sql"></a>Synapse SQL 中的臨時表

本文包含使用臨時表的基本指引，並強調 Synapse SQL 內工作階段層級臨時表的原則。 

專用的 SQL 集區和無伺服器的 SQL 集區資源都可以使用臨時表。 無伺服器 SQL 集區具有本文最後討論的限制。 

## <a name="temporary-tables"></a>暫存資料表

暫存資料表在處理資料時很有用，尤其是具有暫時性中繼結果的轉換期間。 在 Synapse SQL 中，臨時表存在於工作階段層級。  只有出現在其建立所在的工作階段。 如此一來，當該工作階段登出時，就會自動將其卸除。 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>專用 SQL 集區中的臨時表

在專用的 SQL 集區資源中，臨時表會提供效能優勢，因為它們的結果會寫入本機，而不是遠端儲存體。

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

### <a name="drop-temporary-tables"></a>捨棄臨時表

建立新的工作階段時，不應該存在任何暫存資料表。  但是，如果您呼叫的相同預存程式會使用相同的名稱來建立暫時的，若要確保您的 `CREATE TABLE` 語句成功，請使用簡單的預先存在性檢查並搭配  `DROP` ： 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

為了維持編寫程式碼的一致性，資料表和暫存資料表最好都採用此模式。  `DROP TABLE`當您完成臨時表時，也最好使用移除它們。  

在預存程序開發期間，在程序結尾一併搭配 drop 命令以確保會清除這些物件，也是常見的做法。

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>模組化程式碼

臨時表可在使用者會話中的任何位置使用。 如此一來，就可以利用這項功能來協助您模組化應用程式程式碼。  為了示範，下列預存程式會產生 DDL，以依統計資料名稱更新資料庫中的所有統計資料：

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

在這個階段，唯一發生的動作就是建立會產生 #stats_ddl 臨時表的預存程式。  預存程式會卸載 #stats_ddl （如果已經存在）。 如果在會話中執行一次以上，此卸載可確保不會失敗。  

因為 `DROP TABLE` 預存程式結尾沒有，所以當預存程式完成時，所建立的資料表仍會保留，而且可以在預存程式之外讀取。  

相較于其他 SQL Server 資料庫，Synapse SQL 可讓您在建立此資料表的程式之外使用該臨時表。  透過專用 SQL 集區建立的臨時表可以在會話內的 **任何位置** 使用。 因此，您將會有更多模組化且可管理的程式碼，如下列範例所示：

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

專用的 SQL 集區有一些臨時表的執行限制：

- 僅支援會話範圍的臨時表。  不支援全域暫存資料表。
- 無法在臨時表上建立 Views。
- 只能使用雜湊或循環配置資源散發來建立暫存資料表。  不支援複寫的暫存資料表散發。 

## <a name="temporary-tables-in-serverless-sql-pool"></a>無伺服器 SQL 集區中的臨時表

支援無伺服器 SQL 集區中的臨時表，但其使用方式有限。 它們不能用在以檔案為目標的查詢中。 

例如，您無法從儲存體中的檔案聯結臨時表與資料。 臨時表的數目限制為100，而其總大小限制為100MB。

## <a name="next-steps"></a>後續步驟

若要深入了解如何開發資料表，請參閱[使用 Synapse SQL 資源設計資料表](develop-tables-overview.md)一文。

