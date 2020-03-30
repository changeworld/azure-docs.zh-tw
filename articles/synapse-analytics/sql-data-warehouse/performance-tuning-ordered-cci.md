---
title: 透過已排序的叢集資料行存放區索引進行效能微調
description: 使用有序的群集列存儲索引以提高查詢性能時，應瞭解的建議和注意事項。
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
ms.openlocfilehash: a5bb048a2368f60a83e70dcd6d1ce663ce70a885
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350926"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>透過已排序的叢集資料行存放區索引進行效能微調  

當使用者查詢 SQL Analytics 中的列存儲表時，優化程式將檢查存儲在每個段中的最小值和最大值。  查詢謂詞邊界之外的段不會從磁片讀取到記憶體。  如果要讀取的段數及其總大小較小，則查詢的性能可以更快。   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>有序與非有序群集列存儲索引 
預設情況下，對於創建的每個 SQL Analytics 表，如果沒有索引選項，內部元件（索引產生器）都會在表上創建一個非有序的群集列存儲索引 （CCI）。  每列中的資料被壓縮到單獨的 CCI 行組段中。  每個段的值範圍都有中繼資料，因此在查詢執行期間不會從磁片讀取超出查詢謂詞邊界的段。  CCI 提供最高級別的資料壓縮，並減小要讀取的段大小，以便查詢可以更快地運行。 但是，由於索引產生器在將資料壓縮到段之前不對資料進行排序，因此可能會出現具有重疊值範圍的段，從而導致查詢從磁片讀取更多段並需要更長的時間才能完成。  

創建有序 CCI 時，SQL Analytics 引擎在索引產生器將現有資料壓縮到索引段之前，通過訂單鍵對記憶體中的現有資料進行排序。  使用已排序的資料，段重疊會減少，從而允許查詢進行更高效的段消除，從而更快地消除性能，因為從磁片讀取的段數較小。  如果所有資料可以一次在記憶體中排序，則可以避免段重疊。  鑒於 SQL Analytics 表中的資料量很大，這種情況並不經常發生。  

要檢查列的段範圍，請使用表名和列名稱運行此命令：

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
> 在有序的 CCI 表中，由同一批次 DML 或資料載入操作生成的新資料在該批次處理中排序，並且表中的所有資料之間沒有全域排序。  使用者可以重新生成已排序的 CCI 以對表中的所有資料進行排序。  在 SQL 分析中，列存儲索引 REBUILD 是離線操作。  對於分區表，REBUILD 一次執行一個分區。  正在重建的分區中的資料是"離線"的，並且不可用，直到該分區的 REBUILD 完成。 

## <a name="query-performance"></a>查詢效能

查詢從有序 CCI 獲得的性能收益取決於查詢模式、資料大小、資料排序情況、段的物理結構以及為查詢執行選擇的 DWU 和資源類。  在設計有序的 CCI 表之前，使用者應先查看所有這些因素，然後再選擇排序列。

具有所有這些模式的查詢通常使用有序的 CCI 運行得更快。  
1. 查詢具有相等性、不等式或範圍謂詞
1. 謂詞列和有序的 CCI 列相同。  
1. 謂詞列的使用順序與有序 CCI 列的列序號相同。  
 
在此示例中，表 T1 具有按Col_C、Col_B和Col_A順序排序的群集列存儲索引。

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

與其他三個查詢相比，查詢 1 的性能可以從有序 CCI 中獲益更多。 

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

## <a name="data-loading-performance"></a>資料載入性能

將資料載入到有序的 CCI 表中的性能類似于分區表。  由於資料排序操作，將資料載入到有序的 CCI 表中可能需要比未排序 CCI 表更長的時間，但查詢隨後使用有序 CCI 可以運行得更快。  

下面是將資料載入到具有不同架構的表中的性能比較的示例。

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


下面是 CCI 和有序 CCI 之間的查詢性能比較示例。

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>減少段重疊

重疊段的數量取決於要排序的資料大小、可用記憶體以及有序的 CCI 創建期間的最大並行性 （MAXDOP） 設置。 以下是在創建有序 CCI 時減少段重疊的選項。

- 在較高的 DWU 上使用 x大索引資源類，以便在索引產生器將資料壓縮到段之前，允許更多的記憶體進行資料排序。  一旦進入索引段，資料的物理位置將無法更改。  線段內或跨段沒有資料排序。  

- 使用 MAXDOP = 1 創建有序 CCI。  用於有序 CCI 創建的每個執行緒都適用于資料子集，並在本地排序。  沒有跨不同執行緒排序的資料進行全域排序。  使用平行線程可以減少創建有序 CCI 的時間，但與使用單一執行緒相比，會產生更多的重疊段。  目前，MAXDOP 選項僅在使用 CREATE TABLE 作為 SELECT 命令創建有序 CCI 表時受支援。  通過 CREATE INDEX 或創建表命令創建有序的 CCI 不支援 MAXDOP 選項。 例如，

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```
- 在將資料載入到 SQL Analytics 表中之前，先按排序鍵對資料進行排序。


下面是上述建議後具有零段重疊的有序 CCI 表分佈的示例。 訂購的 CCI 表通過使用 MAXDOP 1 和 x大板的 20 GB 堆表中的 CTAS 在 DWU1000c 資料庫中創建。  CCI 在 BIGINT 列上排序，沒有重複項。  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>在大型表上創建有序的 CCI
創建有序 CCI 是離線操作。  對於沒有分區的表，在有序的 CCI 創建過程完成之前，使用者無法訪問資料。   對於分區表，由於引擎按分區創建有序的 CCI 分區，因此使用者仍可以訪問未進行有序 CCI 創建的分區中的資料。   您可以使用此選項在大型表上有序的 CCI 創建期間將停機時間降至最低： 

1.    在目標大表上創建分區（稱為Table_A）。
2.    創建與表 A 相同的表和分區架構的空有序 CCI 表（稱為Table_B）。
3.    將一個分區從表 A 切換到表 B。
4.    運行 ALTER INDEX <Ordered_CCI_Index>Ordered_CCI_Index<Table_B>重建分區 = 表 B 上的<Partition_ID>以重建切換分區。  
5.    Table_A中的每個分區重複步驟 3 和步驟 4。
6.    一旦所有分區從Table_A切換到Table_B並重建，Table_A，並將Table_B重命名為Table_A。 

## <a name="examples"></a>範例

**A. 要檢查有序的列和訂單序號：**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. 要更改列序號，請從訂單清單中添加或刪除列，或從 CCI 更改為有序 CCI：**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
