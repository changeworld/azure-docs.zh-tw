---
title: 透過已排序的叢集資料行存放區索引進行效能微調
description: 當您使用已排序的叢集資料行存放區索引來改善查詢效能時，應該知道的建議和考慮。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 088a0d10b96a30ef830b4e8a8dc12c19127141db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417048"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>透過已排序的叢集資料行存放區索引進行效能微調  

當使用者在 Synapse SQL 集區中查詢資料行存放區資料表時，優化工具會檢查每個區段中所儲存的最小和最大值。  在查詢述詞界限外的區段不會從磁片讀取至記憶體。  如果要讀取的區段數目和大小總計很小，則查詢可以獲得更快的效能。   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>已排序與未排序的叢集資料行存放區索引

根據預設，對於建立的每個資料表沒有索引選項，內部元件（索引產生器）會在其上建立未排序的叢集資料行存放區索引（CCI）。  每個資料行中的資料會壓縮成個別的 CCI 資料列群組區段。  每個區段的值範圍都有中繼資料，因此在查詢執行期間，不會從磁片讀取查詢述詞範圍外的區段。  CCI 提供最高層級的資料壓縮，並減少要讀取的區段大小，因此查詢的執行速度會更快。 不過，因為索引產生器不會先排序資料，再將其壓縮成區段，所以可能會發生具有重迭數值範圍的區段，因而導致查詢從磁片讀取更多區段，並花費更長的時間來完成。  

建立已排序的 CCI 時，Synapse SQL 引擎會依據順序索引鍵，在記憶體中排序現有的資料，然後再將其壓縮成索引區段。  使用已排序的資料，可以減少區段重迭，讓查詢具有更有效率的區段刪除，因而提高效能，因為從磁片讀取的區段數目較小。  如果所有資料都可以一次在記憶體中進行排序，則可以避免區段重迭。  由於資料倉儲中的大型資料表，因此不會經常發生這種情況。  

若要檢查資料行的區段範圍，請使用您的資料表名稱和資料行名稱來執行下列命令：

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
> 在已排序的 CCI 資料表中，從相同的 DML 或資料載入作業批次產生的新資料會在該批次中排序，而資料表中的所有資料都沒有全域排序。  使用者可以重建已排序的 CCI，以排序資料表中的所有資料。  在 Synapse SQL 中，資料行存放區索引重建是一項離線作業。  針對資料分割資料表，一次重建就會執行一個資料分割。  分割區中正在重建的資料會處於「離線」狀態，且在該分割區的重建完成之前無法使用。 

## <a name="query-performance"></a>查詢效能

排序的 CCI 中的查詢效能提升取決於查詢模式、資料大小、資料的排序程度、區段的實體結構，以及為查詢執行所選擇的 DWU 和資源類別。  在設計已排序的 CCI 資料表時，使用者應該先檢查所有這些因素，再選擇排序資料行。

具有上述所有模式的查詢通常會以排序的 CCI 更快速執行。  
1. 查詢具有相等、不相等或範圍述詞
1. 述詞資料行和已排序的 CCI 資料行相同。  
1. 述詞資料行的使用順序與已排序之 CCI 資料行的資料行序數相同。  
 
在此範例中，資料表 T1 的叢集資料行存放區索引是以 Col_C、Col_B 和 Col_A 的順序排序。

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

查詢1的效能比其他三個查詢更能受益于已排序的 CCI。 

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

將資料載入到已排序之 CCI 資料表的效能類似于分割資料表。  由於資料排序作業的不同，將資料載入到已排序的 CCI 資料表所需的時間可能會比未排序的 CCI 資料表長，但在排序的 CCI 之後，查詢可以更快速執行。  

以下是將資料載入具有不同架構之資料表的範例效能比較。

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


以下是 CCI 和已排序的 CCI 之間的範例查詢效能比較。

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>減少區段重迭

重迭區段的數目取決於要排序的資料大小、可用的記憶體，以及排序的 CCI 建立期間的平行處理原則的最大程度（MAXDOP）設定。 以下是在建立已排序的 CCI 時，減少區段重迭的選項。

- 在較高的 DWU 上使用 xlargerc 資源類別，以允許更多記憶體進行資料排序，索引產生器才會將資料壓縮成區段。  一旦在索引區段中，資料的實體位置就無法變更。  區段內或跨區段不會進行任何資料排序。  

- 建立具有 MAXDOP = 1 的已排序 CCI。  用於排序的 CCI 建立的每個執行緒都會在資料子集上運作，並在本機進行排序。  不同執行緒排序的資料上沒有全域排序。  使用平行線程可以縮短建立已排序之 CCI 的時間，但會產生比使用單一線程更多的重迭區段。  目前，只有在使用 CREATE TABLE AS SELECT 命令建立已排序的 CCI 資料表時，才支援 MAXDOP 選項。  透過 CREATE INDEX 或 CREATE TABLE 命令建立已排序的 CCI 不支援 MAXDOP 選項。 例如，

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- 先依照排序關鍵字預先排序資料，再將它們載入資料表。

以下是已排序的 CCI 資料表散發範例，其中有零個區段重迭下列建議。 已排序的 CCI 資料表是在 DWU1000c 資料庫中，透過使用 MAXDOP 1 和 xlargerc 的 20 GB 堆積資料表中的 CTAS 來建立。  CCI 會在沒有重複專案的 BIGINT 資料行上排序。  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>在大型資料表上建立已排序的 CCI

建立已排序的 CCI 是一種離線作業。  對於沒有分割區的資料表，使用者必須等到排序的 CCI 建立程式完成之後，才能存取資料。   針對資料分割資料表，由於引擎會依資料分割建立已排序的 CCI 分割區，因此，使用者仍可存取已排序的 CCI 建立不在處理中的資料。   您可以使用此選項，將在大型資料表上排序的 CCI 建立期間的停機時間降至最低： 

1.    在目標大型資料表（稱為 Table_A）上建立分割區。
2.    使用與資料表 A 相同的資料表和資料分割架構，建立空的已排序 CCI 資料表（稱為 Table_B）。
3.    將一個資料分割從資料表 A 切換到資料表 B。
4.    在 <上執行 ALTER INDEX <Ordered_CCI_Index> Table_B> 重建資料分割 = <Partition_ID> 在資料表 B 上重建切換的資料分割。  
5.    針對 Table_A 中的每個資料分割，重複步驟3和4。
6.    當所有分割區從 Table_A 切換到 Table_B 並已重建之後，請卸載 Table_A，然後將 Table_B 重新命名為 Table_A。 

## <a name="examples"></a>範例

**答：若要檢查已排序的資料行和訂單序數。。**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. 若要變更資料行序數、新增或移除訂單清單中的資料行，或從 CCI 變更為已排序的 CCI：**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
