---
title: 透過已排序的叢集資料行存放區索引進行效能微調
description: 當您使用已排序的叢集資料行存放區索引來改善查詢效能時，應該知道的建議和考慮。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 48db8541ebad19e3b22b737f7e92dcc980708ef6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841589"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>透過已排序的叢集資料行存放區索引進行效能微調  

當使用者在 Synapse SQL 集區中查詢資料行存放區資料表時，優化工具會檢查每個區段中所儲存的最小值和最大值。  在查詢述詞範圍外的區段不會從磁片讀取到記憶體。  如果要讀取的區段數目和其總大小很小，查詢可能會獲得更快的效能。   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>已排序與未排序的叢集資料行存放區索引

依預設，針對每個建立的資料表（不含索引選項）， (索引產生器的內部元件) 會建立非排序的叢集資料行存放區索引 (CCI) 。  每個資料行中的資料會壓縮成個別的 CCI 資料列群組區段。  每個區段的值範圍都有中繼資料，因此在查詢執行期間，不會從磁片讀取查詢述詞範圍外的區段。  CCI 提供最高層級的資料壓縮，並減少要讀取的區段大小，讓查詢的執行速度更快。 不過，由於索引產生器不會在將資料壓縮成區段之前排序資料，因此可能會發生具有重迭值範圍的區段，而導致查詢從磁片讀取更多區段，並需要較長的時間才能完成。  

在建立已排序的 CCI 時，Synapse SQL 引擎會依順序索引鍵 (s) 將現有的資料排序，直到索引產生器將它們壓縮成索引區段為止。  使用已排序的資料時，會降低區段重迭的情況，讓查詢具有更有效率的區段刪除，從而提高效能，因為從磁片讀取的區段數目較小。  如果所有資料一次都可以在記憶體中排序，則可以避免區段重迭。  由於資料倉儲中的大型資料表，這種情況通常不會發生。  

若要檢查資料行的區段範圍，請以您的資料表名稱和資料行名稱執行下列命令：

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> 在已排序的 CCI 資料表中，從相同批次或資料載入作業產生的新資料會在該批次中排序，在資料表中的所有資料之間都沒有全域排序。  使用者可以重建已排序的 CCI 來排序資料表中的所有資料。  在 Synapse SQL 中，資料行存放區索引重建是一種離線作業。  如果是資料分割資料表，重建會一次執行一個資料分割。  正在重建的資料分割中的資料是「離線」且無法使用，直到該分割區的重建完成為止。 

## <a name="query-performance"></a>查詢效能

從排序的 CCI 取得的查詢效能取決於查詢模式、資料大小、資料的排序程度、區段的實體結構，以及為查詢執行選擇的 DWU 和資源類別。  在設計已排序的 CCI 資料表時，使用者應該先檢查所有這些因素，再選擇排序資料行。

所有這些模式的查詢通常會以排序的 CCI 更快速執行。  
1. 查詢有相等、不相等或範圍述詞
1. 述詞資料行和已排序的 CCI 資料行相同。  
1. 使用述詞資料行的順序，與已排序之 CCI 資料行的資料行序數相同。  
 
在此範例中，資料表 T1 有叢集資料行存放區索引，以 Col_C、Col_B 和 Col_A 的順序排序。

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

查詢1的效能比其他三個查詢更能從已排序的 CCI 獲益。 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>資料載入效能

載入已排序之 CCI 資料表的資料效能與資料分割資料表類似。  由於資料排序作業的原因，將資料載入已排序的 CCI 資料表可能會花費更長的時間，但在排序的 CCI 之後，查詢的執行速度會更快。  

以下是將資料載入具有不同架構之資料表的範例效能比較。

![顯示將資料載入具有不同架構之資料表的效能比較的橫條圖。](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


以下是 CCI 和已排序 CCI 之間的查詢效能比較範例。

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>減少區段重迭

重迭的區段數目取決於要排序的資料大小、可用的記憶體以及平行處理原則的最大程度 (在排序的 CCI 建立期間 MAXDOP) 設定。 以下是建立已排序的 CCI 時，減少區段重迭的選項。

- 在較高的 DWU 上使用 xlargerc 資源類別，可在索引產生器將資料壓縮成區段之前，允許更多的記憶體進行資料排序。  一旦在索引區段中，就無法變更資料的實體位置。  區段內或跨區段之間沒有資料排序。  

- 使用 MAXDOP = 1 建立已排序的 CCI。  用於排序 CCI 建立的每個執行緒都可在資料子集上運作，並在本機進行排序。  不同執行緒排序的資料之間不會有全域排序。  使用平行線程可以減少建立已排序之 CCI 的時間，但會產生比使用單一執行緒更重迭的區段。  目前，只有在使用 CREATE TABLE AS SELECT 命令來建立已排序的 CCI 資料表時，才支援 MAXDOP 選項。  透過 CREATE INDEX 或 CREATE TABLE 命令建立已排序的 CCI 不支援 MAXDOP 選項。 例如，

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- 在將資料載入資料表之前，依排序索引鍵 (s) 預先排序資料。

以下是已排序的 CCI 資料表分佈範例，其在上述建議下的零區段重迭。 已排序的 CCI 資料表是在 DWU1000c 資料庫中，透過使用 MAXDOP 1 和 xlargerc 的 20 GB 堆積資料表中的 CTAS 建立。  CCI 是在沒有重複專案的 BIGINT 資料行上排序。  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>在大型資料表上建立已排序的 CCI

建立已排序的 CCI 是一種離線作業。  針對沒有分割區的資料表，使用者必須等到排序的 CCI 建立程式完成之後，才能存取資料。   針對資料分割資料表，因為引擎會依分割區建立已排序的 CCI 分割區，所以使用者仍然可以存取排序的 CCI 建立未在進程中的資料分割。   您可以使用此選項，將在大型資料表上排序的 CCI 建立期間的停機時間降到最低： 

1.    在目標大型資料表上建立資料分割 (稱為 Table_A) 。
2.    使用與資料表 A 相同的資料表和資料分割架構，建立空的已排序 CCI 資料表 (呼叫 Table_B) 。
3.    將一個資料分割從資料表 A 切換至資料表 B。
4.    執行 ALTER INDEX <Ordered_CCI_Index> ON <Table_B> REBUILD PARTITION = <Partition_ID 在資料表 B 上重建，以重建已切換的資料分割。  
5.    針對 Table_A 中的每個資料分割重複步驟3和4。
6.    一旦將所有資料分割從 Table_A 切換到 Table_B 並重建之後，請卸載 Table_A，然後將 Table_B 重新命名為 Table_A。 

## <a name="examples"></a>範例

**的.若要檢查已排序的資料行和訂單序數：**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B。若要變更資料行序數，請新增或移除 order 清單中的資料行，或從 CCI 變更為已排序的 CCI：**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
