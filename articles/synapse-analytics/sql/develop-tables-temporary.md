---
title: 將暫存表與 Synapse SQL 一起使用
description: 在 Synapse SQL 中使用暫時表的基本指南。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 090f453771dba6f537ad60605c6e9b96f3ca9957
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428754"
---
# <a name="temporary-tables-in-synapse-sql"></a>Synapse SQL 中的暫存表

本文包含使用臨時表的基本指南,並重點介紹了 Synapse SQL 中的會話級臨時表的原則。 

SQL 池和 SQL 按需(預覽)資源都可以利用臨時表。 SQL 按需具有本文末尾討論的限制。 

## <a name="what-are-temporary-tables"></a>什麼是暫存資料表？

臨時表在處理數據時很有用,尤其是在轉換期間,中間結果是暫時的。 使用 Synapse SQL,臨時表存在於工作階段級別。  它們僅在創建它們的會話中可見。 因此,當會話註銷時,它們會自動丟棄。 

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

### <a name="dropping-temporary-tables"></a>捨棄暫存資料表
建立新的工作階段時，不應該存在任何暫存資料表。  但是,如果您調用的相同儲存過程建立具有相同名稱的個臨時過程,以確保語句`CREATE TABLE`成功,請使用以下簡單存在前檢查`DROP`: 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

對於編碼一致性,最好將此模式同時用於表和臨時表。  完成臨時表後,最好使用`DROP TABLE`刪除臨時表。  

在存儲過程開發中,通常可以看到放置命令在過程結束時捆綁在一起,以確保清理這些物件。

```sql
DROP TABLE #stats_ddl
```

### <a name="modularizing-code"></a>模組化程式碼
臨時表可以在用戶會話的任意位置使用。 然後可以利用此功能來説明您模組化應用程式代碼。  為了演示,以下儲存過程將生成 DDL 以按統計資訊名稱更新資料庫中的所有統計資訊:

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

在此階段,發生的唯一操作是創建生成#stats_ddl臨時表的存儲過程。  如果存儲過程已存在,則#stats_ddl。 如果會話中運行多次,此丟棄可確保它不會失敗。  

由於存儲過程末尾沒有`DROP TABLE`a,因此當存儲過程完成時,創建的表將保留,並且可以在存儲過程之外讀取。  

與其他 SQL Server 資料庫相比,Synapse SQL 允許您在創建它的過程之外使用臨時表。  通過 SQL 池創建的臨時表可以在會話內的**任意位置**使用。 因此,您將擁有更模組化和可管理的代碼,如下示例所示:

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

SQL 池對於臨時表確實有一些實現限制:

- 僅支援會話作用域的臨時表。  不支援全域臨時表。
- 無法在臨時表上創建檢視。
- 只能使用哈希或迴圈分佈創建臨時表。  不支援複製的臨時表分發。 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>SQL 依需表(預覽)

支援 SQL 按需中的臨時表,但其使用範圍有限。 它們不能用於目標文件的查詢。 

例如,不能將來自存儲檔中的數據聯接暫時表。 臨時表的數量限制為 100,其總大小限制為 100MB。

## <a name="next-steps"></a>後續步驟

要瞭解有關開發表的更多內容,請參閱[使用 Synapse SQL 資源設計表](develop-tables-overview.md)一文。

