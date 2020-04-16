---
title: 使用 Synapse SQL 設計表
description: 在 SynapsE SQL 中設計表的簡介。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f1eec76d92edc97f7e4058d3afe813f0bb2aae47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431874"
---
# <a name="design-tables-using-synapse-sql"></a>使用 Synapse SQL 設計表

本文檔包括設計具有 SQL 池和 SQL 按需(預覽)的表的關鍵概念。  

[SQL 按需(預覽)](on-demand-workspace-overview.md)是數據湖中數據的查詢服務。 它沒有用於數據引入的本地存儲。 [SQL 池](best-practices-sql-pool.md)表示在使用 Synapse SQL 時正在預配的分析資源的集合。 SQL 集區的大小取決於資料倉儲單位 (DWU)。

下表列出與 SQL 池與 SQL 支援需求的佈景主題:

| 主題                                                        | SQL 池 | 依需 SQL |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [決定資料表類別](#determine-table-category)        | 是                | 否                      |
| [結構描述名稱](#schema-names)                                | 是                | 是                     |
| [表格名稱](#table-names)                                  | 是                | 否                      |
| [資料表持續性](#table-persistence)                      | 是                | 否                      |
| [一般資料表](#regular-table)                              | 是                | 否                      |
| [暫存表](#temporary-table)                          | 是                | 是                     |
| [外部表](#external-table)                            | 是                | 是                     |
| [資料類型](#data-types)                                    | 是                | 是                     |
| [分散式資料表](#distributed-tables)                    | 是                | 否                      |
| [雜湊分散式資料表](#hash-distributed-tables)          | 是                | 否                      |
| [複寫資料表](#replicated-tables)                      | 是                | 否                      |
| [循環配置資源資料表](#round-robin-tables)                    | 是                | 否                      |
| [資料表常用的散發方法](#common-distribution-methods-for-tables) | 是                | 否                      |
| [資料分割](#partitions)                                    | 是                | 是                     |
| [資料行存放區索引](#columnstore-indexes)                  | 是                | 否                      |
| [統計資料](#statistics)                                    | 是                | 是                     |
| [主鍵與唯一鍵](#primary-key-and-unique-key)    | 是                | 否                      |
| [建立資料表的命令](#commands-for-creating-tables) | 是                | 否                      |
| [找出資料倉儲對應的資料來源](#aligning-source-data-with-the-data-warehouse) | 是                | 否                      |
| [不支援的表功能](#unsupported-table-features)    | 是                | 否                      |
| [資料表大小查詢](#table-size-queries)                    | 是                | 否                      |

## <a name="determine-table-category"></a>決定資料表類別

[星狀結構描述](https://en.wikipedia.org/wiki/Star_schema)會將資料分類為事實和維度資料表。 某些表用於在移動到事實或維度表之前集成或暫存數據。 設計資料表時，請決定資料表的資料將屬於事實、維度還是整合資料表。 此決定將使資料表具有適當的結構和散發機制。

- **事實數據表**包含通常在事務系統中生成的定量數據,然後載入到數據倉庫中。 例如，零售商每天都會產生銷售交易，然後將資料載入資料倉儲事實資料表中，以進行分析。

- **維度資料表**包含可能會變更、但變更頻率通常不高的屬性資料。 例如，客戶的名稱和地址會儲存在維度資料表中，但只有在客戶的設定檔有所變更時，才會更新。 為了最小化大型事實表的大小,客戶的姓名和位址不需要出現在事實數據表的每一行中。 此時，事實資料表與維度資料表可以共用客戶識別碼。 查詢可以聯結兩個資料表，使客戶的設定檔與交易產生關聯。

- **整合資料表**可用來整合或暫存資料。 您可以建立整合資料表作為一般資料表、外部資料表或暫存資料表。 例如，您可以將資料載入至暫存資料表、對暫存的資料執行轉換，然後將該資料插入生產資料表中。

## <a name="schema-names"></a>結構描述名稱

架構是組合以類似方式使用的物件的好方法。 以下代碼建立一個稱為 wwi[的使用者定義的架構](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>資料表名稱

如果要將多個資料庫從 prem 解決方案遷移到 SQL 池,最佳做法是將所有事實、維度和集成表遷移到一個 SQL 池架構。 例如,您可以將[WideWorld 導入者DW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)範例資料倉庫中的所有表儲存在一個稱為 wwi 的架構中。

要在 SQL 池中顯示表的組織,可以使用事實、暗和 int 作為表名稱的前置碼。 下表顯示了 WideWorld 導入者DW 的一些架構和表名稱。  

| WideWorldImportersDW 資料表  | 資料表類型 | SQL 池 |
|:-----|:-----|:------|:-----|
| City | 維度 | wwi.DimCity |
| 單 | 事實 | wwi.FactOrder |

## <a name="table-persistence"></a>資料表持續性

表將資料永久存儲在 Azure 儲存中、臨時在 Azure 存儲中或數據存儲在數據倉庫外部的數據存儲中。

### <a name="regular-table"></a>一般資料表

一般資料表會將資料儲存在 Azure 儲存體中，作為資料倉儲的一部分。 無論會話是否打開,表和數據都將保留。  下面的範例創建一個包含兩列的常規表。

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>暫存資料表

暫存資料表只存在於工作階段執行期間。 您可以使用臨時表來防止其他使用者看到臨時結果。 使用臨時表也會減少清理的需要。  臨時表利用本地存儲,在 SQL 池中可以提供更快的性能。  

SQL 按需支援臨時表。 但是,它的用法是有限的,因為您可以從臨時表中選擇,但不能將其與存儲中的檔聯接。

如需詳細資訊，請參閱[暫存資料表](develop-tables-temporary.md)。

### <a name="external-table"></a>外部資料表

[外部表](develop-tables-external-tables.md)指向位於 Azure 儲存 Blob 或 Azure 資料湖存儲中的數據。

使用[「創建表作為選擇](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)」語句將資料從外部表匯入 SQL 池。 如需載入的教學課程，請參閱[使用 PolyBase 從 Azure Blob 儲存體載入資料](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

對於 SQL 按需,可以使用[CETAS](develop-tables-cetas.md)將查詢結果保存到 Azure 儲存中的外部表。

## <a name="data-types"></a>資料類型

SQL 池支援最常用的數據類型。 如需支援的資料類型清單，請參閱 CREATE TABLE 參考中 CREATE TABLE 陳述式中的[資料類型](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes)。 關於使用資料類型的詳細資訊,請參考[資料型態](../sql/develop-tables-data-types.md)。

## <a name="distributed-tables"></a>分散式資料表

SQL 池的一個基本功能是它可以跨[分佈](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions)在表上存儲和操作的方式。  SQL 池支援三種資料分發方法:

- 循環配置資源 (預設)
- 雜湊
- 複寫

### <a name="hash-distributed-tables"></a>雜湊分散式資料表

哈希分散式表根據分佈列中的值分佈行。 哈希分散式表旨在實現對大型表上的查詢的高性能。 選擇分佈列時,需要考慮幾個因素。

如需詳細資訊，請參閱[分散式資料表的設計指引](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="replicated-tables"></a>複寫資料表

複寫資料表有一個可在每個計算節點上使用的完整資料表複本。 查詢在複製的表上運行很快,因為複製的表上的聯接不需要數據移動。 但是,複製需要額外的存儲,對於大型表來說並不實用。

如需詳細資訊，請參閱[複寫資料表的設計指引](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="round-robin-tables"></a>循環配置資源資料表

循環配置資源資料表會將資料表的資料列平均散發於所有散發。 資料列會隨機分散。 資料可以快速載入循環配置資源資料表。  但是,查詢可能需要比其他分發方法更多的數據移動。

如需詳細資訊，請參閱[分散式資料表的設計指引](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="common-distribution-methods-for-tables"></a>資料表常用的散發方法

表類別通常確定表分佈的最佳選項。

| 資料表類別 | 建議的散發選項 |
|:---------------|:--------------------|
| 事實           | 使用具有叢集資料行存放區索引的雜湊散發。 在相同的散發資料行上聯結兩個雜湊資料表時，可以改善效能。 |
| 維度      | 對較小的資料表使用複寫。 如果資料表太大而無法儲存在每個計算節點上，請使用雜湊散發。 |
| 預備        | 對暫存資料表使用循環配置資源。 使用 CTAS 的載入速度較快。 資料在暫存表中後,請使用 INSERT...選擇以將數據移動到生產表。 |

## <a name="partitions"></a>資料分割

在 SQL 池中,分區的表根據數據範圍儲存並執行表行的操作。 例如，資料表可能會依日、月或年進行分割。 您可以透過「資料分割消除」將查詢掃描限定於某個資料分割內的資料，進而提升查詢效能。

您也可以透過資料分割切換來維護資料。 由於 SQL 池中的數據已經分發,因此過多的分區可能會降低查詢性能。 如需詳細資訊，請參閱[資料分割指引](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。  

> [!TIP]
> 當分區切換到不為空的表分區時,請考慮在[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)語句中使用TRUNCATE_TARGET選項,如果現有數據要截斷。

以下代碼將轉換後的數據轉換為 SalesFact 分區並覆蓋任何現有數據。

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

在 SQL 按需中,您可以限制查詢將讀取的檔/ 資料夾(分區)。 使用[查詢儲存檔](develop-storage-files-overview.md)中描述的檔案路徑和檔資訊函數支援按路徑分區。 下面的範例讀取包含 2017 年資料的資料夾:

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>資料行存放區索引

默認情況下,SQL 池將表存儲為群集列存儲索引。 這種形式的資料儲存對於大型資料表可達到高度的資料壓縮和查詢效能。  叢集資料行存放區索引通常是最佳選擇，但在某些情況下，叢集索引或堆積會是更適當的儲存結構。  

> [!TIP]
> 堆表對於載入瞬態數據(如過渡表)特別有用,該暫存表將轉換為最終表。

如需資料行存放區功能的清單，請參閱[資料行存放區索引的新功能](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。 若要改善資料行存放區索引效能，請參閱[盡可能提高資料行存放區索引的資料列群組品質](../sql/data-load-columnstore-compression.md)。

## <a name="statistics"></a>統計資料


查詢最佳化工具在建立執行查詢的計劃時，會使用資料行層級的統計資料。 為了提高查詢性能,對單個列(尤其是查詢聯接中使用的列)進行統計資訊非常重要。 Synapse SQL 支援自動建立統計資訊。 

統計更新不會自動進行。 在新增或變更大量的資料列之後，請更新統計資料。 例如,在載入後更新統計資訊。 ["統計資訊"指導](develop-tables-statistics.md)文章中提供了其他資訊。

## <a name="primary-key-and-unique-key"></a>主鍵與唯一鍵

僅當同時使用非強制和未執行時,才支援主密鑰。  僅當使用"不強制"時,才支援 UNIQUE 約束。  有關詳細資訊,請參閱 SQL[池錶約束](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="commands-for-creating-tables"></a>建立資料表的命令

您可以將資料表建立為新的空資料表。 您也可以在建立資料表後填入 Select 陳述式的結果。 以下是用來建立資料表的 T-SQL 命令。

| T-SQL 陳述式 | 描述 |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 藉由定義所有的資料表資料行和選項，建立空的資料表。 |
| [建立外部表](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 建立外部資料表。 表的定義存儲在 SQL 池中。 表數據存儲在 Azure Blob 儲存或 Azure 資料湖儲存中。 |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 在新的資料表中填入 Select 陳述式的結果。 資料表資料行和資料類型皆以 Select 陳述式的結果為基礎。 若要匯入資料，此陳述式可從外部資料表進行選取。 |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 藉由將 Select 陳述式的結果匯出至外部位置，建立新的外部資料表。  位置是 Azure Blob 儲存或 Azure 資料湖儲存。 |

## <a name="aligning-source-data-with-the-data-warehouse"></a>找出資料倉儲對應的資料來源

資料倉儲資料表會在您從其他資料來源載入資料時填入。 要實現成功載入,源資料中的列的數量和數據類型必須與數據倉庫中的表定義一致。

> [!NOTE]
> 取得相符的資料，可能是設計資料表時最困難的環節。

如果數據來自多個數據存儲,則可以將數據移植到數據倉庫並將其存儲在集成表中。 數據在整合表中後,可以使用 SQL 池的強大功能來實現轉換操作。 資料備妥後，您可以將它插入生產資料表。

## <a name="unsupported-table-features"></a>不支援的資料表功能

SQL 池支援其他資料庫提供的許多(但不是全部)表功能。  下面的清單顯示了 SQL 池中不支援的一些表功能。

- 外鍵,檢查[表約束](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [計算資料行](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [索引檢視表](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [序列](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [疏鬆資料行](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- 代理金鑰,使用[識別實作](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [同義字](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [觸發程序](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [唯一索引](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [使用者定義類型](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>資料表大小查詢

識別 60 個分配中每個分配中表佔用的空間和行的一種簡單方法是使用[DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

請記住,使用 DBCC 命令可能相當有限。  動態管理檢視 (DMV) 會比 DBCC 命令顯示更多詳細資料。 首先創建下面的視圖。

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>資料表空間摘要

此查詢會傳回資料表的資料列和空格。  表空間摘要允許您查看哪些表是最大表。 您還將查看它們是迴圈、複製還是哈希分佈。  若為雜湊分散式資料表，則查詢也會顯示散發資料行。  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>依散發類型的資料表空間

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>依索引類型的資料表空間

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>散發空間摘要

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>後續步驟

為您的資料倉儲建立資料表之後，下一個步驟是將資料載入資料表中。  有關載入教程,請參閱[將數據載入到 SQL 池](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool)中。
