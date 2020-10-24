---
title: 設計資料表
description: 在 Synapse SQL 集區中設計資料表的簡介。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7973c85c7ca8051cae2ab7155dda94bec43ebd59
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486934"
---
# <a name="design-tables-in-synapse-sql-pool"></a>在 Synapse SQL 集區中設計資料表

本文提供在 SQL 集區中設計資料表的重要入門概念。

## <a name="determine-table-category"></a>決定資料表類別

[星狀結構描述](https://en.wikipedia.org/wiki/Star_schema)會將資料分類為事實和維度資料表。 某些資料表用於資料移至事實或維度資料表之前的整合或暫存。 設計資料表時，請決定資料表的資料將屬於事實、維度還是整合資料表。 此決定將使資料表具有適當的結構和散發機制。

- **事實資料表** 包含通常在交易式系統中產生，然後載入至 SQL 集區的量化資料。 例如，零售業務每天都會產生銷售交易，然後將資料載入至 SQL 集區事實資料表以進行分析。

- **維度資料表**包含可能會變更、但變更頻率通常不高的屬性資料。 例如，客戶的名稱和地址會儲存在維度資料表中，但只有在客戶的設定檔有所變更時，才會更新。 為了將大型事實資料表的大小降至最低，客戶的名稱和位址不需要在事實資料表的每個資料列中。 此時，事實資料表與維度資料表可以共用客戶識別碼。 查詢可以聯結兩個資料表，使客戶的設定檔與交易產生關聯。

- **整合資料表**可用來整合或暫存資料。 您可以建立整合資料表作為一般資料表、外部資料表或暫存資料表。 例如，您可以將資料載入至暫存資料表、對暫存的資料執行轉換，然後將該資料插入生產資料表中。

## <a name="schema-and-table-names"></a>結構描述和資料表名稱

架構很適合用來將資料表群組在一起，並以類似的方式使用。  如果您要將多個資料庫從內部內部部署方案遷移至 SQL 集區，最好將所有事實、維度和整合資料表遷移至 SQL 集區中的一個架構。

例如，您可以將 [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 範例 SQL 集區中的所有資料表儲存在一個名為 wwi 的架構中。 下列程式碼會建立名為 wwi 的 [使用者定義架構](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 。

```sql
CREATE SCHEMA wwi;
```

若要在 SQL 集區中顯示資料表的組織，您可以使用事實、維度和 int 作為資料表名稱的前置詞。 下表顯示 WideWorldImportersDW 的一些結構描述和資料表名稱。  

| WideWorldImportersDW 資料表  | 資料表類型 | SQL 集區 |
|:-----|:-----|:------|:-----|
| City | 維度 | wwi.DimCity |
| 單 | 事實 | wwi.FactOrder |

## <a name="table-persistence"></a>資料表持續性

資料表會將資料永久儲存在 Azure 儲存體、暫時在 Azure 儲存體中，或儲存在 SQL 集區外部的資料存放區中。

### <a name="regular-table"></a>一般資料表

一般資料表會將 Azure 儲存體中的資料儲存為 SQL 集區的一部分。 無論工作階段是否開啟，資料表和資料都會持續保存。  下列範例會建立具有兩個數據行的一般資料表。

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>暫存資料表

暫存資料表只存在於工作階段執行期間。 您可以使用臨時表來防止其他使用者看到暫存結果，也可以減少清除的需求。  

臨時表利用本機儲存體來提供快速的效能。  如需詳細資訊，請參閱[暫存資料表](sql-data-warehouse-tables-temporary.md)。

### <a name="external-table"></a>外部資料表

外部資料表會指向位於 Azure 儲存體 blob 或 Azure Data Lake Store 中的資料。 搭配 CREATE TABLE AS SELECT 語句使用時，從外部資料表選取時，會將資料匯入 SQL 集區。

因此，外部資料表很適合用來載入資料。 如需載入教學課程，請參閱 [使用 PolyBase 從 Azure blob 儲存體載入資料](load-data-from-azure-blob-storage-using-polybase.md)。

## <a name="data-types"></a>資料類型

SQL 集區支援最常使用的資料類型。 如需支援的資料類型清單，請參閱 CREATE TABLE 參考中 CREATE TABLE 陳述式中的[資料類型](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes)。 如需資料類型的使用指引，請參閱[資料類型](sql-data-warehouse-tables-data-types.md)。

## <a name="distributed-tables"></a>分散式資料表

Synapse SQL 的基本功能，是它 [可以跨散發](massively-parallel-processing-mpp-architecture.md#distributions)套件儲存及運算元據表的方式。 Synapse SQL 支援三種散發資料的方法：迴圈配置資源 (預設) 、雜湊和已複寫。

### <a name="hash-distributed-tables"></a>雜湊分散式資料表

雜湊分散式資料表會根據散發資料行中的值來散發資料列。 雜湊分散式資料表的設計目的是為了達到大型資料表查詢的高效能。 選擇散發資料行時，需要考慮幾個因素。

如需詳細資訊，請參閱[分散式資料表的設計指引](sql-data-warehouse-tables-distribute.md)。

### <a name="replicated-tables"></a>複寫資料表

複寫資料表有一個可在每個計算節點上使用的完整資料表複本。 查詢會在複寫的資料表上快速執行，因為複寫資料表上的聯結不需要移動資料。 但複寫需要額外的儲存空間，而且對於大型資料表而言並不實用。

如需詳細資訊，請參閱[複寫資料表的設計指引](design-guidance-for-replicated-tables.md)。

### <a name="round-robin-tables"></a>循環配置資源資料表

循環配置資源資料表會將資料表的資料列平均散發於所有散發。 資料列會隨機分散。 資料可以快速載入循環配置資源資料表。  請記住，查詢可能需要比其他散發方法更多的資料移動。

如需詳細資訊，請參閱[分散式資料表的設計指引](sql-data-warehouse-tables-distribute.md)。

### <a name="common-distribution-methods-for-tables"></a>資料表常用的散發方法

資料表類別通常會決定應選擇哪個選項來散發資料表。

| 資料表類別 | 建議的散發選項 |
|:---------------|:--------------------|
| 事實           | 使用具有叢集資料行存放區索引的雜湊散發。 在相同的散發資料行上聯結兩個雜湊資料表時，可以改善效能。 |
| 維度      | 對較小的資料表使用複寫。 如果資料表太大而無法儲存在每個計算節點上，請使用雜湊散發。 |
| 預備        | 對暫存資料表使用循環配置資源。 使用 CTAS 的載入速度較快。 一旦資料位於臨時表中，請使用 INSERT .。。選取即可將資料移至生產資料表。 |

## <a name="table-partitions"></a>資料表的資料分割

分割的資料表會並根據資料範圍儲存在資料表資料列上，並執行作業。 例如，資料表可能會依日、月或年進行分割。 您可以透過「資料分割消除」將查詢掃描限定於某個資料分割內的資料，進而提升查詢效能。 您也可以透過資料分割切換來維護資料。 因為 Azure Synapse Analytics 中的資料已經散發，所乙太多資料分割可能會降低查詢效能。 如需詳細資訊，請參閱[資料分割指引](sql-data-warehouse-tables-partition.md)。  當資料分割切換至非空白的資料表分割區時，如果要截斷現有的資料，請考慮在 [ALTER table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 語句中使用 TRUNCATE_TARGET 選項。 下列程式碼會將轉換後的每日資料切換至 [Salesfact，以覆寫任何現有的資料。

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>資料行存放區索引

根據預設，SQL 集區會將資料表儲存為叢集資料行存放區索引。 這種形式的資料儲存對於大型資料表可達到高度的資料壓縮和查詢效能。  

叢集資料行存放區索引通常是最佳選擇，但在某些情況下，叢集索引或堆積會是更適當的儲存結構。  

> [!TIP]
> 堆積資料表在載入暫時性資料時特別有用，例如轉換成最終資料表的臨時表。

如需資料行存放區功能的清單，請參閱[資料行存放區索引的新功能](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。 若要改善資料行存放區索引效能，請參閱[盡可能提高資料行存放區索引的資料列群組品質](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。

## <a name="statistics"></a>統計資料

查詢最佳化工具在建立執行查詢的計劃時，會使用資料行層級的統計資料。

若要改善查詢效能，請務必具有個別資料行的統計資料，尤其是查詢聯結中使用的資料行。 [建立統計資料](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) 會自動發生。  

更新統計資料不會自動發生。 在新增或變更大量的資料列之後，請更新統計資料。 例如，請在載入之後更新統計資料。 如需詳細資訊，請參閱[統計資料指引](sql-data-warehouse-tables-statistics.md)。

## <a name="primary-key-and-unique-key"></a>主鍵和唯一索引鍵

只有在同時使用非叢集且未強制執行時，才支援 PRIMARY KEY。  使用 UNIQUE 條件約束時，只會使用未強制執行的。  檢查 [SQL 集區資料表條件約束](sql-data-warehouse-table-constraints.md)。

## <a name="commands-for-creating-tables"></a>建立資料表的命令

您可以將資料表建立為新的空資料表。 您也可以在建立資料表後填入 Select 陳述式的結果。 以下是用來建立資料表的 T-SQL 命令。

| T-SQL 陳述式 | 說明 |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | 藉由定義所有的資料表資料行和選項，建立空的資料表。 |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | 建立外部資料表。 資料表的定義會儲存在 SQL 集區中。 資料表的資料會儲存在 Azure 儲存體或 Azure Data Lake Store 中。 |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | 在新的資料表中填入 Select 陳述式的結果。 資料表資料行和資料類型皆以 Select 陳述式的結果為基礎。 若要匯入資料，此陳述式可從外部資料表進行選取。 |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | 藉由將 Select 陳述式的結果匯出至外部位置，建立新的外部資料表。  其位置為 Azure Blob 儲存體或 Azure Data Lake Store。 |

## <a name="aligning-source-data-with-the-sql-pool"></a>將來源資料與 SQL 集區對齊

SQL 集區資料表是透過從另一個資料來源載入資料來填入。 若要順利執行載入，來源資料中的資料行數目和資料類型必須與 SQL 集區中的資料表定義一致。 取得相符的資料，可能是設計資料表時最困難的環節。

如果資料來自多個資料存放區，您可以將資料載入至 SQL 集區，並將它儲存在整合資料表中。 一旦資料位於整合資料表中，您就可以使用 SQL 集區的強大功能來執行轉換作業。 資料備妥後，您可以將它插入生產資料表。

## <a name="unsupported-table-features"></a>不支援的資料表功能

SQL 集區支援其他資料庫所提供的許多資料表功能，但並非全部。  下列清單顯示 SQL 集區中不支援的一些資料表功能：

- Foreign key，Check [Table 條件約束](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [計算資料行](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [索引檢視表](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [序列](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [疏鬆資料行](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- Surrogate 索引鍵。 使用[身分識別](sql-data-warehouse-tables-identity.md)進行實作。
- [同義字](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [觸發程序](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [唯一索引](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [使用者定義類型](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>資料表大小查詢

識別資料表在每個散發 (共 60 個) 中所耗用的空間和資料列數的其中一種簡單方式，就是使用 [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

不過，使用 DBCC 命令相當受限。  動態管理檢視 (DMV) 會比 DBCC 命令顯示更多詳細資料。 從建立這個視圖開始：

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

此查詢會傳回資料表的資料列和空格。  它可讓您查看哪些資料表是最大的資料表，以及它們是迴圈配置資源、複寫或雜湊散發。  若為雜湊分散式資料表，則查詢也會顯示散發資料行。  

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

為您的 SQL 集區建立資料表之後，下一個步驟是將資料載入資料表中。  如需載入教學課程，請參閱 [將資料載入至 SQL 集](load-data-wideworldimportersdw.md)區。
