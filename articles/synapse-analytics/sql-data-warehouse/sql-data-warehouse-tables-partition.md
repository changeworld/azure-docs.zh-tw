---
title: 分區表
description: 在 Synapse SQL 池中使用表分區的建議與範例
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4e19c20036d74752b75a668d6a37c46ef1b008e6
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583195"
---
# <a name="partitioning-tables-in-synapse-sql-pool"></a>在 Synapse SQL 池中分割區表

在 Synapse SQL 池中使用表分區的建議和範例。

## <a name="what-are-table-partitions"></a>什麼是資料表分割？

資料表分割可讓您將資料分割成較小的資料群組。 在大部分情況下，資料表分割都是根據日期資料行建立。 所有 Synapse SQL 池表類型都支援分區;包括群集列存儲、群集索引和堆。 所有散發類型也也支援資料分割，包括散發的雜湊或循環配置資源。  

資料分割可以提升資料維護和查詢效能。 其具備上述兩個優點，還是只有一個優點，取決於資料的載入方式，以及相同的資料行是否可用於這兩個目的，因為資料分割只能在一個資料行上進行。

### <a name="benefits-to-loads"></a>載入的優點

Synapse SQL 池中分割區的主要好處是透過使用分區刪除、切換和合併來提高載入資料的效率和性能。 在大部分情況下，會依照與資料載入到資料庫的順序密切相關的日期資料行來分割資料。 使用資料分割來維護資料的最大優點之一是避免記錄交易。 雖然插入、更新或刪除資料可能是最直接的方法，但只要付出一些關心和努力，在載入處理期間使用資料分割可以大幅改善效能。

切換分割區可用於快速移除或取得資料表的某個區段。  例如，銷售事實資料表可能僅包含過去 36 個月的資料。 在每個月月底，便會從資料表刪除最舊月份的銷售資料。  使用 delete 陳述式來刪除最舊月份的資料，即可刪除此資料。 不過，使用 delete 陳述式逐列刪除大量資料可能需要太多時間，而且會產生大型交易的風險，如果發生錯誤，則需要很長的時間來復原。 比較理想的方法是卸除最舊的資料磁碟分割。 刪除個別的資料列需要數小時的時間，而刪除整個磁碟分割可能只要數秒。

### <a name="benefits-to-queries"></a>查詢的優點

資料分割也可用來改善查詢效能。 針對資料分割資料套用篩選的查詢可以限制只掃描合格的資料分割。 這種篩選方法可以避免掃描完整的資料表，而只掃描較小的資料子集。 引進叢集資料行存放區索引後，述詞消除效能優勢比較沒有幫助，但在某些情況下，對查詢有所益處。 例如,如果使用銷售日期欄位將銷售事實表分區為 36 個月,則篩選銷售日期的查詢可以跳過與篩選器不匹配的分區中的搜索。

## <a name="sizing-partitions"></a>調整分割區大小

雖然資料分割可用來改善某些案例的效能，但是在某些情況下，建立具有 **太多** 資料分割的資料表可能會降低效能。  叢集資料行存放區資料表尤其堪慮。 若要讓資料分割有所助益，務必要了解使用資料分割的時機，以及要建立的分割區數目。 多少分割區才算太多並無硬性規定，這取決於您的資料以及您同時載入多少分割區。 成功的資料分割配置通常會有數十至數百個資料分割，而不會高達數千個。

在**叢集資料行存放區**資料表上建立分割區時，請務必考慮每個分割區各有多少個資料列。 為了讓叢集資料行存放區資料表達到最佳壓縮和效能，每個散發與分割區都需要至少 100 萬個資料列。 在創建分區之前,Synapse SQL 池已經將每個表劃分為 60 個分散式資料庫。 除了散發以外，任何加入至資料表的資料分割都是在幕後建立。 使用此示例,如果銷售事實表包含 36 個每月分區,並且給定 Synapse SQL 池有 60 個分佈,則銷售事實表應包含每月 6000 萬行,或者填充所有月份時包含 21 億行。 如果資料表包含的資料列少於每個分割區建議的最小資料列數，請考慮使用較少的分割區，以增加每個分割區的資料列數目。 如需詳細資訊，請參閱[索引](sql-data-warehouse-tables-index.md)一文，其中包含可評估叢集資料行存放區索引品質的查詢。

## <a name="syntax-differences-from-sql-server"></a>與 SQL Server 之間的語法差異

Synapse SQL 池引入了一種定義比 SQL Server 更簡單的分區的方法。 分區函數和方案在 Synapse SQL 池中不使用,因為它們在 SQL Server 中。 相反地，您只需要識別已分割的資料行和邊界點。 雖然資料分割的語法與 SQL Server 稍有不同，但基本概念是一樣的。 SQL Server 和 Synapse SQL 池支援每個表一個分區列,可以劃分分區範圍。 若要深入了解資料分割，請參閱[分割資料表和索引](/sql/relational-databases/partitions/partitioned-tables-and-indexes)。

使用 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) 陳述式的下列範例，依據 OrderDateKey 資料行分割 FactInternetSales 資料表︰

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>從 SQL Server 移轉資料分割

要將 SQL Server 分割區定義移至 Synapse SQL 池,只需:

- 刪除 SQL Server [資料分割配置](/sql/t-sql/statements/create-partition-scheme-transact-sql)。
- 將[資料分割函式](/sql/t-sql/statements/create-partition-function-transact-sql)定義新增至您的 CREATE TABLE。

如果您從 SQL Server 執行個體移轉分割資料表，下列 SQL 可協助您找出每個分割區中的資料列數目。 請記住,如果在 Synapse SQL 池上使用相同的分區粒度,則每個分區的行數將減少 60 倍。  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>分割切換

突觸 SQL 池支援分區拆分、合併和切換。 這些功能是使用 [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) 陳述式執行。

若要切換兩個資料表間的分割區，您必須確定分割區對齊其各自的界限，而且資料表定義相符。 因為檢查條件約束不適用於強制資料表中的值範圍，來源資料表必須包含與目標資料表相同的分割區界限。 如果分割區界限不同，則分割區切換將會失敗，因為分割區中繼資料不會同步處理。

### <a name="how-to-split-a-partition-that-contains-data"></a>如何分割包含資料的分割

使用 `CTAS` 陳述式是分割已含資料之分割的最有效方法。 如果資料分割資料表是叢集式資料行存放區，則資料表分割區必須是空的，才可加以分割。

下列範例會建立分割的資料行存放區資料表。 它會在每個分割區中插入一個資料列：

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

下列查詢會使用 `sys.partitions` 目錄檢視來尋找資料列計數：

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

下列分割命令收到錯誤訊息：

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

訊息 35346、層級 15、狀態 1、行 44：ALTER PARTITION 陳述式的 SPLIT 子句失敗，因為分割不是空的。 當資料表上存在資料行存放區索引時，僅能分割空的分割區。 請考慮在發出 ALTER PARTITION 陳述式前停用資料行存放區索引，然後在 ALTER PARTITION 完成後重建資料行存放區索引。

不過，您可以使用 `CTAS` 建立新資料表以保存資料。

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

分割區界限已對齊，所以允許切換。 這會讓來源資料表有空白分割區可供您接著分割。

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

接下來只需使用 `CTAS` 將資料對齊新的分割區界限，然後將我們的資料切換回到主資料表。

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

一旦您完成資料移動後，最好先在目標資料表上重新整理統計資料。 更新統計資料可確保統計資料正確反映其各自分割區中的新資料散發。

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>將新資料載入到包含資料的分割區中,一步一步

通過分區切換將數據載入到分區中是一種方便的方式,用於在表中暫行新數據,而新數據中的交換機對使用者不可見。  在繁忙的系統上,處理與分區切換相關的鎖定爭用可能具有挑戰性。  要清除分區中的現有數據,需要一個`ALTER TABLE`數據來切換數據。  然後,`ALTER TABLE`需要另一個切換到新數據。  在 Synapse SQL`TRUNCATE_TARGET``ALTER TABLE`池中, 命令中支援該選項。  使用`TRUNCATE_TARGET``ALTER TABLE`指令使用新資料覆蓋分區中的現有數據。  下面是一個範例,用於`CTAS`使用現有資料創建新表,插入新資料,然後將所有資料切換回目標表,覆蓋現有資料。

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>資料表分割原始檔控制

若要避免您的資料表定義在您的原始檔控制系統中**失效**，您可以考慮下列方法：

1. 將資料表建立為分割資料表，但沒有分割值

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT` 資料表：

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

使用這種方法,原始程式碼管理中的代碼保持靜態,並且允許分區邊界值是動態的;隨著時間的推移,隨著資料庫的發展。

## <a name="next-steps"></a>後續步驟

如需開發資料表的詳細資訊，請參閱[資料表概觀](sql-data-warehouse-tables-overview.md)一文。

