---
title: 使用 Azure Synapse SQL 資源建立和更新統計資料
description: 建立和更新 Synapse SQL 中查詢最佳化統計資料的建議和範例。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 52e3ea3e07a81495f64f70f72686154a02a654af
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451789"
---
# <a name="statistics-in-synapse-sql"></a>Synapse SQL 中的統計資料

本文提供使用 Synapse SQL 資源建立和更新查詢優化統計資料的建議和範例：專用的 SQL 集區和無伺服器 SQL 集區。

## <a name="statistics-in-dedicated-sql-pool"></a>專用 SQL 集區中的統計資料

### <a name="why-use-statistics"></a>為何使用統計資料

更專用的 SQL 集區知道您的資料，執行查詢的速度就愈快。 將資料載入專用的 SQL 集區之後，收集資料的統計資料是查詢優化最重要的一件事。  

專用的 SQL 集區查詢最佳化工具是以成本為基礎的優化工具。 它會比較各種查詢方案的成本，然後選擇成本最低的方案。 在大部分的情況下，它會選擇執行最快的方案。

例如，如果最佳化工具評估您的查詢篩選的日期會傳回一個資料列，則會選擇一個方案。 如果最佳化工具評估所選取的日期將傳回 100 萬個資料列，則會傳回不同的方案。

### <a name="automatic-creation-of-statistics"></a>自動建立統計資料

當資料庫 AUTO_CREATE_STATISTICS 選項設定為時，專用的 SQL 集區引擎會分析傳入的使用者查詢是否有遺漏的統計資料 `ON` 。  如果缺少統計資料，查詢最佳化工具會在查詢述詞或聯結條件中的個別資料行上建立統計資料。 

此函數用於改善查詢計劃的基數估計值。

> [!IMPORTANT]
> 自動建立統計資料目前依預設開啟。

您可以執行下列命令，檢查您的資料倉儲是否已設定 AUTO_CREATE_STATISTICS：

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

如果您的資料倉儲未啟用 AUTO_CREATE_STATISTICS，建議您執行以下命令來啟用此屬性：

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

下列陳述式將觸發統計資料的自動建立動作：

- SELECT
- INSERT-SELECT
- CTAS
- UPDATE
- 刪除
- EXPLAIN，包含聯結或偵測到存在述詞時

> [!NOTE]
> 在暫存或外部資料表上不會產生統計資料的自動建立。

統計資料的自動建立是同步完成的。 因此，如果您的資料行缺少統計資料，查詢效能可能會略為降低。 為單一資料行建立統計資料的時間，取決於資料表的大小。

若要避免測量效能降低，請在分析系統前，先執行基準測試的工作負載，以確定會先建立統計資料。

> [!NOTE]
> 統計資料的建立會記錄在不同使用者內容下的 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

當自動統計資料建立完成時，將採用以下格式：_WA_Sys_<十六進位的 8 位數資料行識別碼>_<十六進位的 8 位數資料表識別碼>。 您可以執行 [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 命令，來檢視已建立的統計資料：

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

table_name 是包含要顯示統計資料的資料表名稱，此資料表不可為外部資料表。 目標是用來顯示統計資料資訊的目標索引、統計資料或資料行名稱。

### <a name="update-statistics"></a>更新統計資料

其中一個最佳做法，是隨著新增新的日期，每天在日期資料行上更新統計資料。 每次有新資料列載入資料倉儲時，就會加入新的載入日期或交易日期。 這些增加項目會造成資料散發變更，並使統計資料過時。

客戶資料表中國家/地區資料行上的統計資料，可能永遠不需要更新，因為值散發通常不會變更。 假設客戶間的散發固定不變，將新資料列加入至資料表變化並不會改變資料散發情況。

不過，當您的資料倉儲原本只包含單一國家/地區，而您又帶入來自新國家/地區的資料時，您就需要更新國家/地區資料行上的統計資料。

以下為更新統計資料的相關建議：

|||
|-|-|
| **統計資料更新的頻率**  | 保守：每日 </br> 載入或轉換資料之後 |
| **取樣** |  小於 10 億個資料列，使用預設取樣 (20%)。 </br> 大於 10 億個資料列，使用 2% 的取樣。 |

### <a name="determine-last-statistics-update"></a>判斷上次統計資料更新

為查詢疑難排解時，首先要詢問的問題之一就是「統計資料是最新的嗎？」

這個問題不是可依資料存留期回答的問題。 如果基礎資料並沒有任何實質變更，最新的統計資料物件可能會是舊的。 當資料列數目已顯著變更，或資料行的值散發有實質變更時，就應該更新統計資料。

並沒有可用的動態管理檢視可判斷自上次更新統計資料後，資料表中的資料是否發生變化。 了解統計資料的存留期，可為您提供部分資訊。 

您可以使用下列查詢來判斷每份資料表上次更新了哪些統計資料。

> [!NOTE]
> 如果資料行的值散發有實質變更，無論統計資料上一次更新的時間為何，您都應該更新統計資料。

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

例如，資料倉儲中的 **日期資料行** 通常需要經常更新統計資料。 每次有新資料列載入資料倉儲時，就會加入新的載入日期或交易日期。 這些增加項目會造成資料散發變更，並使統計資料過時。

客戶資料表上性別資料行的統計資料可能永遠不需要更新。 假設客戶間的散發固定不變，將新資料列加入至資料表變化並不會改變資料散發情況。

但是，如果資料倉儲只包含一種性別，而新的需求導致多種性別，則需要更新性別資料行的統計資料。 

如需詳細資訊，請檢閱[統計資料](/sql/relational-databases/statistics/statistics)一文。

### <a name="implement-statistics-management"></a>實作統計資料管理

擴充您的資料載入程序通常是個不錯的主意，可確保在載入結束時更新統計資料。 當資料表變更其大小、值散發或兩者時，資料載入最為頻繁。 因此，載入程序是實作某些管理程序的合理位置。

以下提供指導原則，以便在載入過程中更新您的統計資料：

- 確保每個載入的資料表至少都更新一個統計資料物件。 此程序會在統計資料更新過程中更新資料表大小 (資料列計數和頁面計數) 資訊。
- 將焦點放在參與 JOIN、GROUP BY、ORDER BY 和 DISTINCT 子句的資料行。
- 考慮較頻繁更新「遞增索引鍵」資料行 (例如交易日期)，因為這些值不會包含在統計資料長條圖中。
- 考慮較不常更新靜態散發資料行。
- 請記得，每個統計資料物件會依序更新。 僅只實作 `UPDATE STATISTICS <TABLE_NAME>` 不一定理想，尤其是對具有許多統計資料物件的寬型資料表而言。

如需詳細資訊，請參閱[基數估計](/sql/relational-databases/performance/cardinality-estimation-sql-server)。

### <a name="examples-create-statistics"></a>範例：建立統計資料

下列範例顯示如何使用各種選項來建立統計資料。 您用於每個資料行的選項取決於您的資料特定以及在查詢中使用資料行的方式。

#### <a name="create-single-column-statistics-with-default-options"></a>使用預設選項建立單一資料行統計資料

若要建立資料行的統計資料，請提供統計資料物件的名稱和資料行的名稱。
此語法會使用所有預設選項。 根據預設，專用的 SQL 集區會在建立統計資料時，取樣 **20%** 的資料表。

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

另一個選項是以百分比指定樣本大小：

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>只對某些資料列建立單一資料行統計資料

您也可以對資料表 (其稱為篩選的統計資料) 中部分的資料列建立統計資料。

例如，當您計劃查詢大型分割資料表的特定分割時，可以使用篩選的統計資料。 只對分割值建立統計資料，將會改善統計資料的精確度。 您也會體驗到查詢效能的改善。

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

如需完整參考，請參閱 [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

#### <a name="create-multi-column-statistics"></a>建立多重資料行統計資料

若要建立多重資料行統計資料物件，請利用上述範例，但要指定更多資料行。

> [!NOTE]
> 用來估計查詢結果中資料列數目的長條圖，只適用於統計資料物件定義中所列的第一個資料行。

在此範例中，長條圖位於 *product\_category*。 跨資料行統計資料會依據 *product\_category* 和 *product\_sub_category* 計算：

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

因為 *product\_category* 和 *product\_sub\_category* 之間有關聯性，所以多重資料行統計資料物件在同時存取這些資料行時，相當實用。

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

SQL 集區沒有相當於 SQL Server 中 sp_create_stats 的系統預存程序。 此預存程序會對資料庫中還沒有統計資料的每個資料行建立單一資料行統計資料物件。

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

若要使用預設值為資料表中的所有資料行建立統計資料，請執行預存程序。

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

若要使用 fullscan 為資料表中的所有資料行建立統計資料，請呼叫以下程序：

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

若要為資料表中的所有資料行建立取樣的統計資料，請輸入 3 和取樣百分比。 以下程序使用的取樣率為 20%。

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

藉由更新特定統計資料物件，即可減少管理統計資料所需的時間和資源。 此動作需要經過思考，才能選取要更新的最佳統計資料物件。

#### <a name="update-all-statistics-on-a-table"></a>更新資料表的所有統計資料

更新資料表上所有統計資料物件的簡單方法為：

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

例如：

```sql
UPDATE STATISTICS dbo.table1;
```

UPDATE STATISTICS 陳述式易於使用。 只要記住，這會更新資料表上所有統計資料，執行超出所需的更多工作。 

如果效能不成問題，此方法是保證擁有最新統計資料的最簡單且最完整的方式。

> [!NOTE]
> 更新資料表上的所有統計資料時，專用的 SQL 集區會進行掃描，以針對每個統計資料物件來取樣資料表。 如果資料表很大，而且有許多資料行以及許多統計資料，則根據需求來更新個別統計資料可能比較有效率。

如需 `UPDATE STATISTICS` 程序的實作，請參閱[暫存資料表](develop-tables-temporary.md)。 實作方法與上述的 `CREATE STATISTICS` 程序有點不同，但結果相同。
如需完整語法，請參閱[更新統計資料](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

### <a name="statistics-metadata"></a>統計資料中繼資料

您可利用數個系統檢視和函式來尋找統計資料相關資訊。 例如，使用 STATS_DATE() 函式，即可查看統計資料物件是否可能過期。 STATS_DATE() 可讓您查看上次建立或更新統計資料的時間。

#### <a name="catalog-views-for-statistics"></a>統計資料的目錄檢視

這些系統檢視提供統計資料的相關資訊：

| 目錄檢視 | 描述 |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |每個資料行有一個資料列。 |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |資料庫中每個物件有一個資料列。 |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |資料庫中每個結構描述有一個資料列。 |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |每個統計資料物件有一個資料列。 |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |統計資料物件中每個資料行有一個資料列。 連結回到 sys.columns。 |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |每個資料表 (包括外部資料表) 有一個資料列。 |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |每個資料類型有一個資料列。 |

#### <a name="system-functions-for-statistics"></a>統計資料的系統函式

這些系統函式很適合用於處理統計資料：

| 系統函式 | 描述 |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |上次更新統計資料物件的日期。 |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |有關統計資料物件所理解之值散發的摘要層級和詳細資訊。 |

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

標頭是關於統計資料的中繼資料。 此長條圖會顯示統計資料物件的第一個索引鍵資料行中的值散發。 

密度向量可測量跨資料行關聯性。 專用的 SQL 集區會使用統計資料物件中的任何資料來計算基數估計值。

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

`DBCC SHOW_STATISTICS()` 相較于 SQL Server，更嚴格地在專用的 SQL 集區中執行：

- 不支援未記載的功能。
- 無法使用 Stats_stream。
- 無法聯結特定統計資料子集的結果。 例如，STAT_HEADER JOIN DENSITY_VECTOR。
- 無法針對訊息隱藏項目設定 NO_INFOMSGS。
- 無法使用統計資料名稱前後的方括弧。
- 無法使用資料行名稱來識別統計資料物件。
- 不支援自訂錯誤 2767。


## <a name="statistics-in-serverless-sql-pool"></a>無伺服器 SQL 集區中的統計資料

統計資料是依特定資料集的特殊資料行 (儲存體路徑) 建立的。

> [!NOTE]
> 無法為 LOB 資料行建立統計資料。

### <a name="why-use-statistics"></a>為何使用統計資料

更多無伺服器的 SQL 集區知道您的資料，它對其執行查詢的速度愈快。 從資料中收集統計資料是將查詢最佳化最重要的工作之一。 

無伺服器 SQL 集區查詢最佳化工具是以成本為基礎的優化工具。 它會比較各種查詢方案的成本，然後選擇成本最低的方案。 在大部分的情況下，它會選擇執行最快的方案。 

例如，如果最佳化工具評估您的查詢篩選的日期會傳回一個資料列，則會選擇一個方案。 如果最佳化工具評估所選取的日期將傳回 100 萬個資料列，則會傳回不同的方案。

### <a name="automatic-creation-of-statistics"></a>自動建立統計資料

無伺服器 SQL 集區會分析傳入的使用者查詢是否有遺漏的統計資料。 如果缺少統計資料，查詢最佳化工具會在查詢述詞或聯結條件中的個別資料行上建立統計資料，以改善查詢計劃的基數估計值。

SELECT 陳述式將觸發統計資料的自動建立。

> [!NOTE]
> 為 Parquet 檔案開啟統計資料的自動建立。 對於 CSV 檔案，您需要手動建立統計資料，直到支援自動建立 CSV 檔案統計資料為止。

自動建立統計資料是同步進行的，因此，如果您的資料行缺少統計資料，查詢效能可能會略為降低。 為單一資料行建立統計資料的時間，取決於目標檔案的大小。

### <a name="manual-creation-of-statistics"></a>手動建立統計資料

無伺服器 SQL 集區可讓您手動建立統計資料。 對於 CSV 檔案，您必須手動建立統計資料，因為，CSV 檔案未開啟統計資料的自動建立。 

如需如何手動建立統計資料的指示，請參閱以下範例。

### <a name="update-statistics"></a>更新統計資料

變更檔案中的資料、刪除和新增檔案，會造成資料散發變更，並讓統計資料過期。 在這種情況下，則需要更新統計資料。

如果資料有大幅變更，無伺服器 SQL 集區會自動重新建立統計資料。 每次自動建立統計資料時，也會儲存資料集的目前狀態：檔案路徑、大小、上次修改日期。

如果統計資料過時，將建立新的統計資料。 此演算法會透過資料進行，並將資料與資料集的目前狀態進行比較。 如果變更的大小大於特定閾值，則會刪除舊的統計資料，並在新的資料集上重新建立。

手動統計資料永遠不會宣告為過時。

> [!NOTE]
> 為 Parquet 檔案開啟統計資料的自動重新建立。 對於 CSV 檔案，您需要手動置放和建立統計資料，直到支援自動建立 CSV 檔案統計資料為止。 請查看下列如何置放和建立統計資料的範例。

為查詢疑難排解時，首先要詢問的問題之一就是「統計資料是最新的嗎？」

當資料列數目已顯著變更，或資料行的值散發有實質變更時，就應該更新統計資料。

> [!NOTE]
> 如果資料行的值散發有實質變更，無論統計資料上一次更新的時間為何，您都應該更新統計資料。

### <a name="implement-statistics-management"></a>實作統計資料管理

您可能希望擴充您的資料管線，以確保在透過新增、刪除，或變更檔案而發生大幅變更時，更新統計資料。

以下提供指導原則，以便更新您的統計資料：

- 確保資料集至少都更新一個統計資料物件。 這會在統計資料更新過程中更新大小 (資料列計數和頁面計數) 資訊。
- 專注于參與 WHERE、JOIN、GROUP BY、ORDER BY 和 DISTINCT 子句的資料行。
- 較為頻繁地更新「遞增索引鍵」資料行 (例如交易日期)，因為這些值不會包含在統計資料長條圖中。
- 較不常更新靜態散發資料行。

如需詳細資訊，請參閱[基數估計](/sql/relational-databases/performance/cardinality-estimation-sql-server)。

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>範例：為 OPENROWSET 路徑中的資料行建立統計資料

下列範例顯示如何使用各種選項來建立統計資料。 您用於每個資料行的選項取決於您的資料特定以及在查詢中使用資料行的方式。

> [!NOTE]
> 您目前只能建立單一資料行的統計資料。
>
> 若要執行 sp_create_openrowset_statistics 和 sp_drop_openrowset_statistics，必須具備下列許可權：管理大量作業或管理資料庫大量作業。

下列預存程序可用於建立統計資料：

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

引數：[ @stmt = ] N'statement_text' - Specifies a Transact-SQL 陳述式，將傳回要用於統計資料的資料行值。 您可以使用 TABLESAMPLE，以指定要使用的資料樣本。 如果未指定 TABLESAMPLE，將使用 FULLSCAN。

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> CSV 取樣目前無法運作，CSV 僅支援 FULLSCAN。

#### <a name="create-single-column-statistics-by-examining-every-row"></a>檢查每個資料列以建立單一資料行統計資料

若要建立資料行的統計資料，請提供查詢來傳回需要統計資料的資料行。

根據預設，如果您未指定，無伺服器 SQL 集區會在建立統計資料時，使用資料集所提供的100% 資料。

例如，根據 population.csv 檔案，為資料集的 [年] 資料行使用預設選項 (FULLSCAN)，建立統計資料：

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

EXEC sys.sp_create_openrowset_statistics N'SELECT year
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

或您可以用百分比指定取樣大小：

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

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>範例：更新統計資料

若要更新統計資料，您需要置放和建立統計資料。 下列預存程序可用於置放統計資料：

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> 若要執行 sp_create_openrowset_statistics 和 sp_drop_openrowset_statistics，必須具備下列許可權：管理大量作業或管理資料庫大量作業。

引數：[ @stmt = ] N'statement_text' - 指定建立統計資料時所使用的相同 Transact-SQL 陳述式。

若要針對資料集的 [年] 資料行更新統計資料 (該資料集以 population.csv 檔案為基礎)，您必須置放和建立統計資料：

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
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

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>範例：為外部資料表資料行建立統計資料

下列範例顯示如何使用各種選項來建立統計資料。 您用於每個資料行的選項取決於您的資料特定以及在查詢中使用資料行的方式。

> [!NOTE]
> 您目前只能建立單一資料行的統計資料。

若要建立資料行的統計資料，請提供統計資料物件的名稱和資料行的名稱。

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

引數：external_table 可指定應建立統計資料的外部資料表。

FULLSCAN 可透過掃描所有資料列來計算統計資料。 FULLSCAN 和 SAMPLE 100 PERCENT 的結果相同。 FULLSCAN 不能搭配 SAMPLE 選項一起使用。

SAMPLE 數目 PERCENT 指定當查詢最佳化工具建立統計資料時，要在資料表或索引檢視表中使用的近似百分比或資料列數目。 數字可以從 0 到 100。

SAMPLE 不能和 FULLSCAN 選項一起使用。

> [!NOTE]
> CSV 取樣目前無法運作，CSV 僅支援 FULLSCAN。

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

若要更新統計資料，您需要置放和建立統計資料。 先置放統計資料：

```sql
DROP STATISTICS census_external_table.sState
```

然後建立統計資料：

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

### <a name="statistics-metadata"></a>統計資料中繼資料

您可利用數個系統檢視和函式來尋找統計資料相關資訊。 例如，使用 STATS_DATE() 函式，即可查看統計資料物件是否可能過期。 STATS_DATE() 可讓您查看上次建立或更新統計資料的時間。

> [!NOTE]
> 統計資料中繼資料僅適用于外部資料表資料行。 未提供 OPENROWSET 資料行的統計資料。

#### <a name="catalog-views-for-statistics"></a>統計資料的目錄檢視

這些系統檢視提供統計資料的相關資訊：

| 目錄檢視                                                 | 描述                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | 每個資料行有一個資料列。                                     |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | 資料庫中每個物件有一個資料列。                     |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | 資料庫中每個結構描述有一個資料列。                     |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | 每個統計資料物件有一個資料列。                          |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | 統計資料物件中每個資料行有一個資料列。 連結回到 sys.columns。 |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | 每個資料表 (包括外部資料表) 有一個資料列。           |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | 每個資料類型有一個資料列。                                  |

#### <a name="system-functions-for-statistics"></a>統計資料的系統函式

這些系統函式很適合用於處理統計資料：

| 系統函式                                              | 描述                                  |
| :----------------------------------------------------------- | :------------------------------------------- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | 上次更新統計資料物件的日期。 |

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
WHERE   st.[user_created] = 1
;
```

## <a name="next-steps"></a>後續步驟

若要進一步改善專用 SQL 集區的查詢效能，請參閱監視[專用 sql 集區的](best-practices-sql-pool.md#maintain-statistics)[工作負載](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)和最佳作法。

若要進一步改善無伺服器 SQL 集區的查詢效能，請參閱 [無伺服器 sql 集區的最佳作法](best-practices-sql-on-demand.md)
