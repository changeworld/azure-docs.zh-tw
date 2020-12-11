---
title: 專用 SQL 集區的資料載入最佳作法
description: 使用 Azure Synapse Analytics 中的專用 SQL 集區載入資料的建議和效能優化。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: a96d49a029eb83e24c1fb86954406693aa9c33a3
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093956"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用專用的 SQL 集區載入資料的最佳作法

在本文中，您將瞭解使用專用的 SQL 集區載入資料的建議和效能優化。

## <a name="preparing-data-in-azure-storage"></a>在 Azure 儲存體中準備資料

若要將延遲降至最低，請共置您的儲存層和您專用的 SQL 集區。

將資料匯出成 ORC 檔案格式時，如有大量文字資料行，則可能發生 Java 記憶體不足錯誤。 若要解決這項限制，只能匯出部分資料行。

每種檔案格式具有不同的效能特性。 若要最快載入，使用壓縮的分隔文字檔案。 UTF-8 和 UTF-16 效能之間的差異最小。

將大型的壓縮檔案分成較小的壓縮檔案。

## <a name="running-loads-with-enough-compute"></a>使用足夠的計算資源執行載入

如需最快的載入速度，一次只執行一項載入作業。 如果這不可行，請同時執行極少的載入數。 如果您預期會有大量載入作業，請考慮在負載前相應增加您專用的 SQL 集區。

若要以適當的計算資源執行載入，請建立為了執行載入而指定的載入使用者。 將每個載入使用者分類為特定的工作負載群組。 若要執行負載，請以其中一個載入使用者的形式登入，然後執行負載。 負載會以使用者的工作負載群組執行。  

### <a name="example-of-creating-a-loading-user"></a>建立載入使用者的範例

這個範例會建立分類至特定工作負載群組的載入使用者。 第一個步驟是 **連線到 主要資料庫** 並建立登入。

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

連接到專用的 SQL 集區，並建立使用者。 下列程式碼假設您已連接到名為 mySampleDataWarehouse 的資料庫。 它會示範如何建立名為 loader 的使用者，並為使用者提供使用 [COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)建立資料表和載入的許可權。 然後，它會將使用者分類為具有最大資源的 DataLoads 工作負載群組。 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
      MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```
<br><br>
>[!IMPORTANT] 
>這是將 SQL 集區的100% 資源配置到單一負載的極端範例。 這會為您提供最大並行值1。 請注意，這應該僅用於初始負載，您必須使用自己的設定來建立額外的工作負載群組，以在您的工作負載之間 balanace 資源。 

若要使用載入工作負載群組的資源來執行負載，請以載入器登入並執行負載。

## <a name="allowing-multiple-users-to-load-polybase"></a>允許多個使用者載入 (PolyBase) 

通常需要讓多個使用者將資料載入專用的 SQL 集區。 以 [SELECT (transact-sql) ](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (PolyBase) 中的 CREATE TABLE 載入，需要資料庫的 CONTROL 許可權。  CONTROL 權限可控制所有結構描述的存取。

您可能不希望所有的載入使用者都能控制所有結構描述的存取。 若要限制權限，請使用 DENY CONTROL 陳述式。

例如，請考慮將資料庫結構描述 schema_A 用於 dept A 以及 schema_B 用於 dept B，讓資料庫使用者 user_A 和 user_B 個別成為 dept A 及 B 中載入的 PolyBase 之使用者。 這兩個使用者皆已獲得 CONTROL 資料庫權限。 結構描述 A 和 B 的建立者現在是使用 DENY 鎖定其結構描述：

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A 和 user_B 現在已從其他部門的架構鎖定。

## <a name="loading-to-a-staging-table"></a>載入至暫存表格

若要達到將資料移至專用 SQL 集區資料表的最快載入速度，請將資料載入至臨時表。  將暫存資料表定義為堆積，並使用循環配置資源作為散發選項。

請考慮載入通常是兩個步驟的程式，您可以在其中先載入至臨時表，然後將資料插入生產專用的 SQL 集區資料表。 如果生產資料表使用雜湊散發，您若定義採用雜湊散發的暫存資料表，則載入和插入的總時間可能比較快。

載入至暫存表格所需的時間比較長，但是將資料列插入生產資料表的第二個步驟不會導致資料四處移動。

## <a name="loading-to-a-columnstore-index"></a>載入至資料行存放區索引

資料行存放區索引需要大量的記憶體，才能將資料壓縮成高品質的資料列群組。 為了最佳的壓縮和索引效率，資料行存放區索引需要將多達 1,048,576 個資料列壓縮到每個資料列群組中。

當記憶體不足時，資料行存放區索引可能無法達到最大的壓縮率。 接著，此案例會影響查詢效能。 如需深入探討，請參閱[資料行存放區記憶體最佳化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。

- 若要確保載入使用者有足夠的記憶體可達到最大的壓縮率，請使用是中型或大型資源類別成員的載入使用者。
- 載入足夠的資料列，完全填滿新的資料列群組。 在大量載入期間，每 1,048,576 個資料列會以完整資料列群組形式直接壓縮到資料行存放區中。 若載入的資料列少於 102,400 個，則會將資料列傳送至差異存放區，其中的資料列會保存在 b 型樹狀結構索引中。

> [!NOTE]
> 如果您載入的資料列數目太少，它們可能會路由傳送至差異存放區，而不會立即壓縮成資料行存放區格式。

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>使用 SqLBulkCopy API 或 bcp 時，增加批次大小

使用 COPY 語句載入將會提供具有專用 SQL 集區的最高輸送量。 如果您無法使用此複製來載入，而且必須使用 [SQLBULKCOPY API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 或 [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)，您應該考慮增加批次大小，以獲得更好的輸送量。

> [!TIP]
> 介於 100 K 到1百萬個數據列之間的批次大小是判斷最佳批次大小容量的建議基準。

## <a name="handling-loading-failures"></a>處理載入失敗

使用外部資料表的載入可能會失敗，並顯示「查詢已中止 -- 從外部來源讀取時已達最大拒絕閾值」錯誤訊息。 此訊息表示您的外部資料包含「錯誤」記錄。

如果資料記錄符合下列其中一個條件，則會被視為中途變更：

- 資料類型和資料行數目不符合外部資料表的資料行定義。
- 資料不符合指定的外部檔案格式。

若要修正「錯誤」記錄，請確定您的外部資料表及外部檔案格式定義皆正確，且這些定義與您的外部資料相符。

如果外部資料記錄的子集有變更，您可以使用 [CREATE EXTERNAL TABLE (transact-sql) ](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)中的拒絕選項，選擇拒絕查詢的這些記錄。

## <a name="inserting-data-into-a-production-table"></a>將資料插入生產資料表中

使用 [INSERT 陳述式](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)單次載入小型資料表，或甚至定期重新載入查閱，可能會與使用 `INSERT INTO MyLookup VALUES (1, 'Type 1')` 之類的陳述式有一樣好的效果。  不過，單一插入的效率不如執行大量載入。

如果您整天有數千個或更多單一插入，請將插入分批，以便進行大量載入。  開發將單一插入附加至檔案的程序，然後建立另一個可定期載入檔案的程序。

## <a name="creating-statistics-after-the-load"></a>建立載入後的統計資料

為了改善查詢效能，在首次載入資料或資料發生重大變更之後，建立所有資料表的所有資料行統計資料非常重要。 建立統計資料可以手動完成，也可以啟用 [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic)。

如需統計資料的詳細說明，請參閱[統計資料](sql-data-warehouse-tables-statistics.md)。 下列範例顯示如何在 Customer_Speed 資料表的五個數據行上手動建立統計資料。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>將儲存體金鑰輪替 (PolyBase) 

基於安全性考量，請定期變更您 blob 儲存體的存取金鑰。 您的 blob 儲存體帳戶有兩個儲存體金鑰，這可讓您轉換金鑰。

若要輪替 Azure 儲存體帳戶金鑰：

對於金鑰已變更的每個儲存體帳戶，發出 [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

範例：

建立原始金鑰

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

將金鑰從 key 1 輪替為 key 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

不需要對基礎外部資料來源進行其他變更。

## <a name="next-steps"></a>後續步驟

- 若要在設計 (ELT) 進程的解壓縮、載入和轉換時，深入瞭解 COPY 語句或 PolyBase，請參閱 [為 Azure Synapse Analytics 設計 ELT](design-elt-data-loading.md)。
- 如需載入教學課程，請 [使用 COPY 語句將資料從 Azure blob 儲存體載入至 SYNAPSE SQL](load-data-from-azure-blob-storage-using-polybase.md)。
- 若要監視資料載入，請參閱[使用 DMV 監視工作負載](sql-data-warehouse-manage-monitor.md)。
