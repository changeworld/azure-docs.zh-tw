---
title: SQL 集區的最佳做法
description: 當您使用 SQL 集區時，您應該知道的建議和最佳作法。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81427792"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse 分析中的 SQL 集區最佳做法

本文提供最佳做法集合，可協助您在 Azure Synapse 分析中為 SQL 集區達到最佳效能。 您可以在下面找到基本的指引和重要領域，以在您建立解決方案時專注于。 每一節都會為您介紹一個概念，然後將您指向更深入探討此概念的詳細文章。

## <a name="sql-pools-loading"></a>SQL 集區載入

如需 SQL 集區載入指引，請參閱[載入資料的指引](data-loading-best-practices.md)。

## <a name="reduce-cost-with-pause-and-scale"></a>利用暫停和調整來降低成本

如需透過暫停和調整來降低成本的詳細資訊，請參閱[管理計算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

## <a name="maintain-statistics"></a>維護統計資料

雖然 SQL Server 會自動偵測並建立或更新資料行的統計資料，但是 SQL 集區需要手動維護統計資料。 您會想要維護您的統計資料，以確保 SQL 集區計畫已優化。  最佳化工具建立的計劃只能利用可用的統計資料。

> [!TIP]
> 建立每個資料行的範本統計資料是開始使用統計資料的簡單方式。  

 更新統計資料和對您的資料做重大變更一樣重要。  保守的作法是每天或每次載入之後更新統計資料。  建立和更新統計資料的效能與成本之間總有一些取捨。

若要縮短統計資料維護時間，請選擇有統計資料的資料行，或需要最頻繁的更新。 例如，您可能會想要更新日期資料行，其中每日可能會加入新的值。 將焦點放在包含聯結的資料行、WHERE 子句中使用的資料行，以及在 GROUP BY 中找到的資料行。

如需統計資料的詳細資訊，請參閱[管理資料表統計資料](develop-tables-statistics.md)、[建立統計](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)資料和[更新統計資料](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)等文章。

## <a name="group-insert-statements-into-batches"></a>將 INSERT 陳述式群組為批次

具有 INSERT 語句（例如） `INSERT INTO MyLookup VALUES (1, 'Type 1')`的小型資料表單次載入，可能是根據您的需求而定的最佳方法。 不過，如果您需要在一天內載入數千或數百萬個數據列，則單一插入可能不是最佳的。

解決此問題的方法之一，是開發一個會寫入檔案的程式，然後再由另一個進程定期載入此檔案。 如需詳細資訊，請參閱[插入](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 將資料快速載入及匯出

SQL 集區支援透過數個工具（包括 Azure Data Factory、PolyBase 和 BCP）載入和匯出資料。  若是小量的資料，效能不是那麼重要，任何工具都可以滿足您的需求。  

> [!NOTE]
> 當您載入或匯出大量資料時，Polybase 是最佳選擇，或者您需要更快的效能。

您可使用 CTAS 或 INSERT INTO 來執行 PolyBase 載入。 CTAS 會將交易記錄降到最低，而且是載入資料最快的方式。 Azure Data Factory 也支援 PolyBase 載入，並可達到類似 CTAS 的效能。 PolyBase 支援各種不同的檔案格式，包括 Gzip 檔案。

若要在使用 Gzip 文字檔時最大化輸送量，請將檔分成60或更多檔案，以最大化負載的平行處理。  如需更快的總輸送量，請考慮同時載入資料。 下列文章包含與本節相關的主題的其他資訊：

- [載入資料](data-loading-overview.md)
- [使用 PolyBase 指南](data-loading-best-practices.md)
- [Azure SQL 集區載入模式和策略](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [使用 Azure Data Factory 載入資料](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [使用 Azure Data Factory 移動資料](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Create table as select （CTAS）](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>載入並查詢外部資料表

Polybase 不是查詢的最佳作法。 適用于 SQL 集區的 Polybase 資料表目前僅支援 Azure blob 檔案和 Azure Data Lake 儲存體。 這些檔案沒有任何支援的計算資源。 因此，SQL 集區無法卸載此工作，而且必須藉由將它載入至 tempdb 來讀取整個檔案，讓它可以讀取資料。

如果您有數個查詢可查詢此資料，最好只載入此資料一次，並讓查詢使用本機資料表。 如需進一步的 Polybase 指導方針，請參閱[使用 polybase 的指南](data-loading-best-practices.md)一文。

## <a name="hash-distribute-large-tables"></a>雜湊分散大型資料表

根據預設，資料表是以「循環配置資源」方式分散。   此預設值可讓使用者輕鬆地開始建立資料表，而不需要決定其資料表的散發方式。 迴圈配置資源資料表可能會對某些工作負載執行得夠多。 但是在大部分情況下，散發資料行提供較佳的效能。  

當兩個大型事實資料表聯結時，其效果優於迴圈配置資源資料表的資料行所散發之資料表的最常見範例是。  

例如，如果您有 order_id 散發的 orders 資料表，而且交易資料表也是由 order_id 散發，則當您將 orders 資料表聯結至 order_id 上的交易資料表時，此查詢會變成傳遞查詢。 然後會刪除資料移動作業。 較少的步驟代表較快的查詢。 較少的資料移動也會讓查詢更快。

> [!NOTE]
> 載入分散式資料表時，您的傳入資料不應依照散發索引鍵排序。 這麼做會使您的負載變慢。

下面提供的文章連結將提供有關透過選取散發資料行來改善效能的其他詳細資料。 此外，您還可以在 CREATE TABLE 語句的 WITH 子句中，找到如何定義分散式資料表的相關資訊：

- [資料表總覽](develop-tables-overview.md)
- [資料表散發](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [選取資料表發佈](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>不要過度執行資料分割

雖然分割資料可以透過分割區切換或使用分割區刪除來優化掃描，而有效地維護您的資料，但有太多資料分割可能會使查詢變慢。  通常，高細微性的資料分割策略可能適用于 SQL Server 在 SQL 集區上可能無法順利運作。  

如果每個分割區的資料列少於1000000，有太多資料分割可能會降低叢集資料行存放區索引的效率。 SQL 集區會自動將您的資料分割成60資料庫。 因此，如果您建立具有100個數據分割的資料表，則結果會是6000個磁碟分割。 每個工作負載都不同，因此最好的建議是試驗資料分割，以瞭解最適合您工作負載的方式。  

其中一個要考慮的選項是使用的資料細微性，低於您使用 SQL Server 所實作為的程度。 例如，請考慮使用每週或每月分割區，而不是每日分割區。

如需資料分割的詳細資訊，請參閱[資料表分割](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="minimize-transaction-sizes"></a>將交易大小最小化

在交易中執行的 INSERT、UPDATE 和 DELETE 子句。 當它們失敗時，必須將它們復原。 若要降低長時間復原的可能性，請盡可能將交易大小降到最低。  將 INSERT、UPDATE 和 DELETE 子句分割成多個部分，可將交易大小降至最低。 例如，如果您有預期會花費1小時的插入，您可以將插入分成四個部分。 每次執行都會縮短為15分鐘。  

> [!TIP]
> 利用特殊的最低限度記錄案例，例如 CTAS、截斷、卸載資料表或插入空的資料表，以降低回復風險。  

另一個消除回復的作法是使用「僅中繼資料」作業 (像是資料分割切換) 進行資料管理。  例如，而不是執行 DELETE 子句來刪除資料表中的所有資料列，其中 order_date 是2001年10月，您可以每月分割資料。 接著，您可以使用另一個資料表中的空白分割區來切換出資料分割（請參閱 ALTER TABLE 範例）。  

針對未分割的資料表，請考慮使用 CTAS 來寫入您想要保留在資料表中的資料，而不是使用 DELETE。  如果 CTAS 採用相同的時間量，則執行會更安全，因為它具有最少的交易記錄，而且可以在需要時快速取消。

本章節的相關內容的進一步資訊包含在下列文章中：

- [Create table as select （CTAS）](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [了解交易](develop-transactions.md)
- [最佳化交易](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [資料表分割](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>減少查詢結果大小

減少查詢結果大小可協助您避免因大型查詢結果而造成的用戶端問題。  您可以編輯您的查詢，以減少傳回的資料列數目。 某些查詢產生工具可讓您在每個查詢中加入 "top N" 語法。  您也可以將查詢結果 CETAS 至臨時表，然後使用 PolyBase export 進行舊版處理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小的可能資料行大小

定義 DDL 時，請使用將支援資料的最小資料類型，因為這樣做會改善查詢效能。  對於 CHAR 和 VARCHAR 資料行而言，這項建議特別重要。  如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。  避免將所有字元資料行定義為較大的預設長度。  此外，將資料行定義為 VARCHAR，這是必要的，而不是使用 NVARCHAR。

如需與上述資訊相關之基本概念的詳細審查，請參閱[資料表總覽](develop-tables-overview.md)、[資料表資料類型](develop-tables-data-types.md)和[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章。

## <a name="use-temporary-heap-tables-for-transient-data"></a>針對暫時性資料使用暫存堆積資料表

當您暫時登陸 SQL 集區上的資料時，堆積資料表通常會使整體程式更快速。  如果您只是在執行更多轉換之前將資料載入，則將資料表載入堆積資料表的速度會比將資料載入叢集資料行存放區資料表更快。  

將資料載入臨時表也會比將資料表載入永久儲存體更快速。  臨時表是以 "#" 開頭，而且只能由建立它的會話存取。 因此，它們只能在有限的情況下使用。 堆積資料表是在 CREATE TABLE 的 WITH 子句中定義。  如果您使用暫存資料表，請記得也在該暫存資料表上建立統計資料。

如需其他指引，請參閱[臨時表](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[CREATE TABLE，做為選取](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)的文章。

## <a name="optimize-clustered-columnstore-tables"></a>將叢集資料行存放區資料表最佳化

叢集資料行存放區索引是您可以將資料儲存在 SQL 集區中最有效率的方式之一。  根據預設，SQL 集區中的資料表會建立為叢集資料行存放區。  為了讓資料行存放區資料表的查詢獲得最佳效能，良好的區段品質很重要。  當資料列在記憶體不足的狀態下寫入資料行存放區資料表時，資料行存放區區段品質可能會降低。  

區段品質可以用壓縮資料列群組中的資料列數目來測量。 如需偵測和改善叢集資料行存放區資料表之區段品質的逐步指示，請參閱[資料表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中的資料行存放區[索引品質不佳的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)。  

由於高品質的資料行存放區區段很重要，因此最好使用中型或大型資源類別中的使用者識別碼來載入資料。 使用較低的[資料倉儲單位](resource-consumption-models.md)，表示您想要將更大型的資源類別指派給正在載入的使用者。

資料行存放區資料表通常不會將資料推送到壓縮的資料行存放區區段中，直到每個資料表超過1000000個數據列。 每個 SQL 集區資料表會分割成60個數據表。 因此，資料行存放區資料表不會受益于查詢，除非資料表有超過60000000個數據列。  

> [!TIP]
> 對於包含少於60000000個數據列的資料表，具有資料行存放區索引可能不是最佳的解決方案。  

如果您分割資料，每個分割區都必須有1000000個數據列，才能受益于叢集資料行存放區索引。  針對具有100資料分割的資料表，它必須至少有6000000000個數據列，才能受益于叢集資料行存放區（60分佈*100*資料分割1000000資料列）。  

如果您的資料表沒有6000000000個數據列，您有兩個主要選項。 請減少資料分割數目，或考慮改為使用堆積資料表。  您也可以使用具有次要索引的堆積資料表，而不是資料行存放區資料表，以查看是否能獲得較佳的效能。

查詢資料行存放區資料表時，如果您只選取您需要的資料行，查詢執行會更快速。  如需資料表和資料行存放區索引的詳細資訊，請參閱[資料表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、資料行存放區[索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和重建資料行存放區[索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)文章。

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用較大的資源類別來改善查詢效能

SQL 集區會使用資源群組做為將記憶體配置給查詢的方式。 一開始，所有使用者都會指派給小型資源類別，每個散發會授與 100 MB 的記憶體。  一律有60發行版本。 每個散發的最小值為 100 MB。 全系統記憶體配置總計為 6000 MB，或只在 6 GB 之下。  

有些查詢，像是大型聯結或載入叢集資料行存放區資料表，將受益於較大的記憶體配置。  某些查詢（例如純粹掃描）將不會看到任何好處。 利用較大的資源類別會影響並行。 因此，在將所有使用者移至大型資源類別之前，您會想要記住這些事實。

如需資源類別的詳細資訊，請參閱[適用于工作負載管理的資源類別](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用較小的資源類別來增加並行

如果您在使用者查詢中發現長時間延遲，您的使用者可能會在較大的資源類別中執行。 此案例會提升平行存取插槽的耗用量，這可能會導致其他查詢排入佇列。  若要判斷使用者查詢是否已排入`SELECT * FROM sys.dm_pdw_waits`佇列，請執行以查看是否傳回任何資料列。

[工作負載管理的資源類別](../sql-data-warehouse/resource-classes-for-workload-management.md)和[sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章將提供您詳細資訊。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 對查詢進行監視和最佳化

SQL 集區有數個 Dmv 可用來監視查詢執行。  下列監視文章會引導您逐步瞭解如何查看執行中查詢的詳細資料。  若要在這些 DMV 中快速找到查詢，可在您的查詢中使用 LABEL 選項。 如需其他詳細資訊，請參閱下列清單中所包含的文章：

- [使用 DMV 監視工作負載](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [標誌](develop-label.md)
- [件](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>後續步驟

另請參閱[疑難排解](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中的常見問題和解決方案。

如果您需要本文中未提供的資訊，請使用此頁面左側的 [搜尋檔] 來搜尋所有 SQL 集區檔。  [Sql 集區論壇](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)是讓您對其他使用者和 SQL 集區產品群組提出問題的地方。  

我們會主動監看這個論壇，以確保您的問題有其他使用者或是我們回答。  如果您想要在 Stack Overflow 上詢問問題，我們也有[AZURE SQL 集區 Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)。

如需功能要求，請使用[AZURE SQL 集區意見](https://feedback.azure.com/forums/307516-sql-data-warehouse)反應頁面。  新增您的要求或投票其他要求，可協助我們將焦點放在最常需要的功能上。
