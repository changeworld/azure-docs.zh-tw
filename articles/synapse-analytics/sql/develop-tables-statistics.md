---
title: 建立、更新統計資訊
description: 在 SynapsE SQL 建立和更新查詢最佳化統計資訊的建議和範例。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 5196c85ca1d68028893caee55035c6c455b37d64
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676944"
---
# <a name="statistics-in-synapse-sql"></a>SynapsE SQL 中的統計資訊

本文中提供了使用 Synapse SQL 資源建立和更新查詢優化統計資訊的建議和範例:SQL 池和 SQL 按需(預覽)。

## <a name="statistics-in-sql-pool"></a>SQL 池中的統計資訊

### <a name="why-use-statistics"></a>為什麼使用統計資訊

SQL 池資源對數據瞭解得越多,執行查詢的速度就越快。 將數據載入 SQL 池後,收集數據的統計資訊是可用於查詢優化的最重要操作之一。  

SQL 池查詢優化器是基於成本的優化器。 它比較各種查詢計劃的成本,然後選擇成本最低的計劃。 在大多數情況下,它選擇執行速度最快的計劃。

例如,如果優化器估計查詢篩選的日期將返回一行,它將選擇一個計劃。 如果估計所選日期將返回 100 萬行,它將返回不同的計劃。

### <a name="automatic-creation-of-statistics"></a>自動建立統計資料

當資料庫AUTO_CREATE_STATISTICS選項設置為 時,SQL 池將分析傳入用戶查詢中`ON`缺少的統計資訊。  如果缺少統計資訊,查詢優化器將創建查詢謂詞或聯接條件中各個列的統計資訊。 此功能用於改進查詢計劃的基數估計。

> [!IMPORTANT]
> 自動建立統計資料目前依預設開啟。

您可以透過執行以下指令檢查資料套件庫是否設定了AUTO_CREATE_STATISTICS:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

如果資料倉儲未啟用AUTO_CREATE_STATISTICS,我們建議您透過執行以下命令開啟此屬性:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

這些文句將觸發統計資訊的自動建立:

- SELECT
- INSERT-SELECT
- CTAS
- UPDATE
- 刪除
- 偵測到聯接或存在謂詞時出現

> [!NOTE]
> 不自動創建統計資訊,不會在臨時表或外部表上生成統計資訊。

自動創建統計資訊是同步完成的。 因此,如果列缺少統計資訊,則查詢性能可能會略有下降。 為單個列創建統計資訊的時間取決於表的大小。

為了避免可衡量的性能下降,您應該首先通過在分析系統之前執行基準工作負載來創建統計資訊。

> [!NOTE]
> 統計資訊的創建記錄在不同的使用者上下文中的[sys.dm_pdw_exec_requests。](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

創建自動統計資訊時,它們將採用以下形式 _:WA_Sys_十六進位元>_的 8 位列 id<<十六进制> 中的 8 位錶 ID。 您可以透過執行[DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)指令來檢視已建立的統計資訊:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

table_name是包含要顯示的統計資訊的表的名稱,該統計資訊不能是外部表。 目標是要顯示統計資訊的目標索引、統計資訊或列的名稱。

### <a name="update-statistics"></a>更新統計資料

其中一個最佳做法，是隨著新增新的日期，每天在日期資料行上更新統計資料。 每次有新資料列載入資料倉儲時，就會加入新的載入日期或交易日期。 這些添加將更改數據分佈並使統計資訊過時。

客戶表中國家或地區列的統計資訊可能永遠不需要更新,因為值分佈通常不會更改。 假設客戶間的散發固定不變，將新資料列加入至資料表變化並不會改變資料散發情況。

但是,當數據倉庫僅包含一個國家或地區,並且您帶來了來自新國家或地區的資料時,您需要更新國家或地區列的統計資訊。

以下為更新統計資料的相關建議：

|||
|-|-|
| **統計資料更新的頻率**  | 保守：每日 </br> 載入或轉換資料之後 |
| **取樣** |  少於 10 億行,使用預設採樣(20%)。 </br> 擁有超過10億行,使用採樣的2%。 |

### <a name="determine-last-statistics-update"></a>確定上次統計資訊更新

為查詢疑難排解時，首先要詢問的問題之一就是「統計資料是最新的嗎？」****

這個問題不是按數據年齡可以回答的問題。 如果基礎資料並沒有任何實質變更，最新的統計資料物件可能會是舊的。 當行數發生實質性更改,或者列的值分佈發生重大更改*時,是時候*更新統計資訊了。

沒有動態管理檢視可用於確定自上次更新統計信息以來表中的數據是否已更改。 了解統計數據的年齡可以為您提供部分圖片。 您可以使用下列查詢來判斷每份資料表上次更新了哪些統計資料。

> [!NOTE]
> 如果列的值分佈存在重大更改,則應更新統計資訊,而不管它們上次更新時如何。

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

例如,數據倉庫中**的日期列**通常需要頻繁的統計資訊更新。 每次有新資料列載入資料倉儲時，就會加入新的載入日期或交易日期。 這些添加將更改數據分佈並使統計資訊過時。

客戶表中的性別列統計資訊可能永遠不需要更新。 假設客戶間的散發固定不變，將新資料列加入至資料表變化並不會改變資料散發情況。

但是,如果數據倉庫僅包含一個性別,並且新的需求導致多個性別,則需要更新性別列的統計資訊。 有關詳細資訊,請查看[「統計資訊」](/sql/relational-databases/statistics/statistics)一文。

### <a name="implementing-statistics-management"></a>實作統計資料管理

通常最好擴展數據載入過程,以確保在載入結束時更新統計資訊。 數據載入是表最常更改其大小、值分佈或兩者時。 因此,載入過程是實現某些管理過程的邏輯位置。

以下提供指導原則，以便在載入過程中更新您的統計資料：

- 確保每個載入的資料表至少都更新一個統計資料物件。 此過程將更新表大小(行計數和頁數)資訊作為統計資訊更新的一部分。
- 將焦點放在參與 JOIN、GROUP BY、ORDER BY 和 DISTINCT 子句的資料行。
- 請考慮更頻繁地更新"提升鍵"列(如事務日期),因為這些值不會包含在統計直方圖中。
- 考慮較不常更新靜態散發資料行。
- 請記得，每個統計資料物件會依序更新。 僅只實作 `UPDATE STATISTICS <TABLE_NAME>` 不一定理想，尤其是對具有許多統計資料物件的寬型資料表而言。

如需詳細資訊，請參閱[基數估計](/sql/relational-databases/performance/cardinality-estimation-sql-server)。

### <a name="examples-create-statistics"></a>範例：建立統計資料

下列範例顯示如何使用各種選項來建立統計資料。 您用於每個資料行的選項取決於您的資料特定以及在查詢中使用資料行的方式。

#### <a name="create-single-column-statistics-with-default-options"></a>使用預設選項建立單一資料行統計資料

要對列創建統計資訊,請為統計資訊物件提供名稱和列的名稱。
此語法會使用所有預設選項。 默認情況下,SQL 池在創建統計資訊時對表的**20% 進行**採樣。

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

例如：

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>檢查每個資料列以建立單一資料行統計資料

20% 的預設取樣率足以應付大部分的情況。 不過，您可以調整取樣率。 若要取樣整個資料表，請使用此語法：

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

例如：

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>指定取樣大小以建立單一資料行統計資料

另一個選項是指定樣本大小為百分比:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>只對某些資料列建立單一資料行統計資料

您還可以在表中的行的一部分創建統計資訊,這稱為篩選統計資訊。

例如，當您計劃查詢大型分割資料表的特定分割時，可以使用篩選的統計資料。 通過僅創建分區值的統計資訊,統計信息的準確性將提高。 您還將體驗到查詢性能的提高。

這個範例會建立某個值範圍的統計資料。 您可以輕鬆地定義這些值以符合分割中的值範圍。

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> 若要讓查詢最佳化工具在選擇分散式查詢計劃時考慮使用篩選的統計資料，查詢必須符合統計資料物件的定義。 使用上述範例，查詢的 WHERE 子句需要指定介於 2000101 和 20001231 之間的 col1 值。

#### <a name="create-single-column-statistics-with-all-the-options"></a>使用所有選項建立單一資料行統計資料

您也可以將選項結合在一起。 以下範例會使用自訂樣本大小建立篩選的統計資料物件：

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

如需完整參考，請參閱 [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

#### <a name="create-multi-column-statistics"></a>建立多重資料行統計資料

要創建多列統計資訊物件,請使用前面的示例,但指定更多列。

> [!NOTE]
> 用來估計查詢結果中資料列數目的長條圖，只適用於統計資料物件定義中所列的第一個資料行。

在此範例中，長條圖位於 *product\_category*。 跨欄統計資訊根據*\_產品類別*和*產品\_sub_category*計算:

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

由於*產品\_類別*和*\_產品子\_類別*之間存在關聯,因此,如果同時訪問這些列,則多列統計資訊物件可能很有用。

#### <a name="create-statistics-on-all-columns-in-a-table"></a>對資料表中的所有資料行建立統計資料

建立統計資料的其中一個方法是在建立資料表後發出 CREATE STATISTICS 命令：

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>使用預存程序對資料庫中的所有資料行建立統計資料

SQL 池沒有等效於 SQL Server 中sp_create_stats的系統存儲過程。 此預存程序會對資料庫中還沒有統計資料的每個資料行建立單一資料行統計資料物件。
以下範例會協助您開始進行資料庫設計。 請放心地依照您的需求進行調整：

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

要使用預設值對表中的所有列創建統計資訊,請執行存儲過程。

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

若要使用 fullscan 為資料表中的所有資料行建立統計資料，請呼叫以下程序：

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

若要為資料表中的所有資料行建立取樣的統計資料，請輸入 3 和取樣百分比。 以下步驟使用 20% 的採樣率。

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>範例：更新統計資料

若要更新統計資料，您可以：

- 更新一個統計資料物件。 指定您要更新的統計資料物件名稱。
- 更新資料表上的所有統計資料物件。 指定資料表名稱，而不是一個特定統計資料物件。

#### <a name="update-one-specific-statistics-object"></a>更新一個特定統計資料物件

使用下列語法來更新特定統計資料物件：

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

例如：

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

藉由更新特定統計資料物件，即可減少管理統計資料所需的時間和資源。 此操作需要一些考慮才能選擇要更新的最佳統計信息物件。

#### <a name="update-all-statistics-on-a-table"></a>更新資料表的所有統計資料

更新表上所有統計資訊物件的一種簡單方法是:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

例如：

```sql
UPDATE STATISTICS dbo.table1;
```

更新統計資訊語句易於使用。 請記住,它更新了表上*的所有*統計資訊,從而引發了比所需的更多工作。 如果性能不是問題,則此方法是保證統計資訊是最新的最簡單、最完整的方法。

> [!NOTE]
> 更新表上的所有統計資訊時,SQL 池執行掃描,以採樣每個統計資訊物件的表。 如果資料表很大，而且有許多資料行以及許多統計資料，則根據需求來更新個別統計資料可能比較有效率。

有關`UPDATE STATISTICS`過程的實現,請參閱[臨時表](develop-tables-temporary.md)。 實作方法與上述的 `CREATE STATISTICS` 程序有點不同，但結果相同。
有關完整語法,請參閱[更新統計資訊](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="statistics-metadata"></a>統計資料中繼資料

您可利用數個系統檢視和函式來尋找統計資料相關資訊。 例如,可以使用STATS_DATE() 函數查看統計資訊物件是否可能已過期。 STATS_DATE() 允許您查看上次創建或更新統計信息的時間。

#### <a name="catalog-views-for-statistics"></a>統計資料的目錄檢視

這些系統檢視提供統計資料的相關資訊：

| 目錄檢視 | 描述 |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |每個資料行有一個資料列。 |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |資料庫中每個物件有一個資料列。 |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |資料庫中每個結構描述有一個資料列。 |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |每個統計資料物件有一個資料列。 |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |統計資料物件中每個資料行有一個資料列。 連結回到 sys.columns。 |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |每個資料表 (包括外部資料表) 有一個資料列。 |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |每個資料類型有一個資料列。 |

#### <a name="system-functions-for-statistics"></a>統計資料的系統函式

這些系統函式很適合用於處理統計資料：

| 系統函式 | 描述 |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |上次更新統計資料物件的日期。 |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |有關統計資料物件所理解之值散發的摘要層級和詳細資訊。 |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>將統計資料資料行和函式結合成一個檢視

此檢視會一起顯示與統計資料相關的資料行，以及 STATS_DATE() 函式的結果。

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS() 範例

DBCC SHOW_STATISTICS() 顯示統計資料物件中保存的資料。 此資料來自三個部分：

- 頁首
- 密度向量
- 長條圖

標頭是關於統計資訊的元數據。 此長條圖會顯示統計資料物件的第一個索引鍵資料行中的值散發。 密度向量可測量跨資料行關聯性。 SQL 池使用統計資訊物件中的任何數據計算基數估計值。

#### <a name="show-header-density-and-histogram"></a>顯示標頭、密度和長條圖

這個簡單範例顯示統計資料物件的所有三個部分：

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

例如：

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>顯示 DBCC SHOW_STATISTICS() 的一或多個部分

如果您只想要檢視特定部分，請使用 `WITH` 子句並指定您要查看哪些部分：

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

例如：

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS() 差異

`DBCC SHOW_STATISTICS()`與 SQL Server 相比,在 SQL 池中更嚴格地實現:

- 不支援未記錄的功能。
- 不能使用Stats_stream。
- 無法聯接統計資訊特定子集的結果。 例如,STAT_HEADER加入DENSITY_VECTOR。
- 無法為消息抑制設置NO_INFOMSGS。
- 不能使用統計資訊名稱周圍的方括弧。
- 不能使用列名稱來標識統計信息物件。
- 不支援自定義錯誤 2767。

### <a name="next-steps"></a>後續步驟

如需進一步改善查詢效能，請參閱[監視工作負載](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="statistics-in-sql-on-demand-preview"></a>SQL 依需統計(預覽)

根據特定數據集(儲存路徑)的特定列創建統計資訊。

### <a name="why-use-statistics"></a>為什麼使用統計資訊

SQL 按需(預覽)對數據瞭解得越多,對數據的查詢執行速度就越快。 收集有關數據的統計資訊是優化查詢的最重要操作之一。 SQL 按需查詢優化器是基於成本的優化器。 它比較各種查詢計劃的成本,然後選擇成本最低的計劃。 在大多數情況下,它選擇執行速度最快的計劃。 例如,如果優化器估計查詢篩選的日期將返回一行,它將選擇一個計劃。 如果估計所選日期將返回 100 萬行,它將返回不同的計劃。

### <a name="automatic-creation-of-statistics"></a>自動建立統計資料

SQL 按需分析傳入用戶查詢,以求缺少統計資訊。 如果缺少統計資訊,查詢優化器將創建查詢謂詞或聯接條件中各個列的統計資訊,以改善查詢計劃的基數估計。

SELECT 語句將觸發自動創建統計資訊。

> [!NOTE]
> 自動建立 Parquet 檔案的統計資訊。 對於 CSV 檔,您需要手動創建統計資訊,直到支援自動創建 CSV 檔統計資訊。

自動創建統計資訊是同步完成的,因此,如果列缺少統計資訊,則查詢性能可能會略有下降。 為單個列創建統計資訊的時間取決於目標檔的大小。

### <a name="manual-creation-of-statistics"></a>手動建立統計資料

SQL 按需允許您手動創建統計資訊。 對於 CSV 檔,您必須手動創建統計資訊,因為 CSV 檔的自動建立統計資訊不會打開。 有關如何手動創建統計資訊的說明,請參閱以下示例。

### <a name="updating-statistics"></a>更新統計資料

對文件中的數據的更改、刪除和添加檔會導致資料分發更改,並使統計資訊過時。 在這種情況下,需要更新統計資訊。

如果數據發生顯著變化,SQL 按需自動重新創建統計資訊。 每次自動創建統計資訊時,也會保存數據集的當前狀態:檔案路徑、大小、上次修改日期。

當統計資訊過時時,將創建新統計資訊。 演演算法遍遍數據,並將其與數據集的當前狀態進行比較。 如果更改的大小大於特定閾值,則刪除舊統計資訊,並將在新數據集上重新創建。

手動統計信息永遠不會聲明過時。

> [!NOTE]
> 為 Parquet 檔開啟統計資訊的自動重新使用。 對於 CSV 檔,您需要手動刪除和創建統計資訊,直到支援自動創建 CSV 檔案統計資訊。 請查看以下有關如何刪除和創建統計資訊的範例。

為查詢疑難排解時，首先要詢問的問題之一就是「統計資料是最新的嗎？」****

當行數發生顯著變化,或者列的值分佈發生重大變化*時,是時候*更新統計資訊了。

> [!NOTE]
> 如果列的值分佈存在重大更改,則應更新統計資訊,而不管它們上次更新時如何。

### <a name="implementing-statistics-management"></a>實作統計資料管理

您可能希望擴展資料管道,以確保在透過添加、刪除或更改檔顯著更改資料時更新統計資訊。

更新統計資訊提供以下指導原則:

- 確保數據集至少更新了一個統計信息物件。 這將更新大小(行計數和頁數)資訊作為統計資訊更新的一部分。
- 將焦點放在參與 JOIN、GROUP BY、ORDER BY 和 DISTINCT 子句的資料行。
- 更頻繁地更新"提升鍵"列(如事務日期),因為這些值不會包含在統計直方圖中。
- 更新靜態分佈列的頻率較低。

如需詳細資訊，請參閱[基數估計](/sql/relational-databases/performance/cardinality-estimation-sql-server)。

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>範例:為 OPENROWSET 路徑中的欄建立統計資訊

以下範例示範如何使用各種選項創建統計資訊。 您用於每個資料行的選項取決於您的資料特定以及在查詢中使用資料行的方式。

> [!NOTE]
> 此時只能創建單列統計資訊。

儲存儲存程序用於建立統計資訊:

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

參數: @stmt [ ] n'statement_text' - 指定一個 Transact-SQL 語句,該語句將返回用於統計的列值。 您可以使用 TABLESAMPLE 指定要使用的資料樣本。 如果未指定 TABLESAMPLE,將使用 FULLSCAN。

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> CSV 採樣目前不起作用,CSV 僅支援 FULLSCAN。

#### <a name="create-single-column-statistics-by-examining-every-row"></a>檢查每個資料列以建立單一資料行統計資料

要對列創建統計資訊,請提供返回需要統計資訊的列的查詢。

預設情況下,如果不另行指定,SQL 按需在創建統計資訊時會使用數據集中 100% 的數據。

例如,要根據總體.csv 檔案為資料集的一年列建立具有預設選項 (FULLSCAN) 的統計資訊:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>指定取樣大小以建立單一資料行統計資料

您可以將樣本大小指定為百分比:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>範例：更新統計資料

要更新統計資訊,您需要刪除並創建統計資訊。 以下儲存程序用於刪除統計資訊:

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

參數: @stmt [ ] N'statement_text' - 指定建立統計資訊時使用的相同 Transact-SQL 語句。

要更新基於 sands.csv 檔案的資料集中的年份列的統計資訊,您需要刪除並建立統計資訊:

```sql
EXEC sys.sp_drop_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>範例:為外部表列建立統計資訊

以下範例示範如何使用各種選項創建統計資訊。 您用於每個資料行的選項取決於您的資料特定以及在查詢中使用資料行的方式。

> [!NOTE]
> 此時只能創建單列統計資訊。

要對列創建統計資訊,請為統計資訊物件提供名稱和列的名稱。

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

參數:external_table指定應創建統計資訊的外部表。

通過掃描所有行來計算統計資訊。 FULLSCAN 和 SAMPLE 100 PERCENT 的結果相同。 FULLSCAN 不能搭配 SAMPLE 選項一起使用。

SAMPLE 編號%指定查詢優化器創建統計資訊時使用的表或索引檢視中的大致百分比或行數。 數位可以從 0 到 100。

SAMPLE 不能和 FULLSCAN 選項一起使用。

> [!NOTE]
> CSV 採樣目前不起作用,CSV 僅支援 FULLSCAN。

#### <a name="create-single-column-statistics-by-examining-every-row"></a>檢查每個資料列以建立單一資料行統計資料

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>指定取樣大小以建立單一資料行統計資料

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>範例：更新統計資料

要更新統計資訊,您需要刪除並創建統計資訊。 首先刪除統計資訊:

```sql
DROP STATISTICS census_external_table.sState
```

並建立統計資訊:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>後續步驟

有關進一步的查詢性能改進,請參閱[SQL 池的最佳做法](best-practices-sql-pool.md#maintain-statistics)。
