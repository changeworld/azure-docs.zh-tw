---
title: SQL 集區的最佳做法
description: 使用 SQL 集區時應該知道的建議和最佳做法。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 345794eead05a4ff5e5d1396df78878d2693f405
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834980"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中 SQL 集區的最佳做法

此文章提供最佳做法集合，以協助您在 Azure Synapse Analytics 中獲得 SQL 集區的最佳效能。 您將能在下面找到在建置解決方案時應注意的基本指導方針和重要領域。 每一節都會介紹一個概念，並引導您前往能更深入涵蓋該概念的詳細文章。

## <a name="sql-pools-loading"></a>SQL 集區載入

如需 SQL 集區載入指導方針，請參閱[載入資料的指南](data-loading-best-practices.md)。

## <a name="reduce-cost-with-pause-and-scale"></a>利用暫停和調整來降低成本

如需透過暫停和調整來降低成本的詳細資訊，請參閱[管理計算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

## <a name="maintain-statistics"></a>維護統計資料

雖然 SQL Server 會自動偵測資料行並建立或更新資料行上的統計資料，SQL 集區則需要手動維護統計資料。 您應該維護統計資料以確保 SQL 集區計劃能夠最佳化。  最佳化工具建立的計劃只能利用可用的統計資料。

> [!TIP]
> 建立每個資料行的範本統計資料是開始使用統計資料的簡單方式。  

更新統計資料和對您的資料做重大變更一樣重要。  保守的作法是每天或每次載入之後更新統計資料。  建立和更新統計資料的效能與成本之間總有一些取捨。

若要縮短統計資料維護時間，請仔細選擇具有統計資料，或是最常需要更新的資料行。 例如，您可以更新每天都可能加入新值的日期資料行。 請將焦點擺在取得牽涉聯結的資料行、在 WHERE 子句中使用的資料行，以及在 GROUP BY 中找到之資料行的統計資料。

關於統計資料的其他資訊，可以在[管理資料表統計資料](develop-tables-statistics.md)、[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)，以及 [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文章中找到。

## <a name="group-insert-statements-into-batches"></a>將 INSERT 陳述式群組為批次

視您的需求而定，使用 `INSERT INTO MyLookup VALUES (1, 'Type 1')` 之類的 INSERT 陳述式對小型資料表進行單次載入可能會是最佳的方法。 不過，如果您一整天都得載入數千或數百萬個資料列，單一 INSERT 可能不是最佳的。

解決此問題的其中一個方法，是開發一個會寫入檔案的處理序，然後再開發另一個會定期載入此檔案的處理序。 請參閱 [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 一文以取得詳細資訊。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 將資料快速載入及匯出

SQL 集區支援透過數種工具 (包括 Azure Data Factory、PolyBase 及 BCP) 來載入及匯出資料。  若是小量的資料，效能不是那麼重要，任何工具都可以滿足您的需求。  

> [!NOTE]
> 當您要載入或匯出大量資料，或者需要更快速的效能時，PolyBase 是最佳選擇。

您可使用 CTAS 或 INSERT INTO 來執行 PolyBase 載入。 CTAS 可以將交易記錄最小化，且為載入資料最快的方法。 Azure Data Factory 也支援 PolyBase 載入，而且可以達到與 CTAS 類似的效能。 PolyBase 支援各種不同的檔案格式，包括 Gzip 檔案。

若要在使用 Gzip 文字檔案時獲得最大的輸送量，請將檔案分成 60 個或更多的檔案，以將載入的平行處理最大化。 如需更快的總輸送量，請考慮同時載入資料。 下列文章包含此節相關主題的其他資訊：

- [資料載入](data-loading-overview.md)
- [使用 PolyBase 指南](data-loading-best-practices.md)
- [Azure SQL 集區載入模式和策略](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/) \(英文\)
- [使用 Azure Data Factory 載入資料](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [使用 Azure Data Factory 移動資料](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [如選取建立資料表 (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>載入並查詢外部資料表

PolyBase 並不是查詢的最佳選項。 適用於 SQL 集區的 PolyBase 資料表目前只支援 Azure Blob 檔案和 Azure Data Lake 儲存體。 這些檔案沒有任何支援的計算資源。 因此，SQL 集區無法卸載此工作，且必須將整個檔案載入 tempdb 並加以讀取，才能讀取該資料。

如果您有數個查詢此資料的查詢，最好單次載入此資料，並讓查詢使用本機資料表。 進一步的 Polybase 指導方針已包含在[使用 PolyBase 的指南](data-loading-best-practices.md)一文中。

## <a name="hash-distribute-large-tables"></a>雜湊分散大型資料表

根據預設，資料表是以「循環配置資源」方式分散。   此預設值可讓使用者更輕鬆地開始建立資料表，而不必決定應該如何散發其資料表。 循環配置資源資料表針對某些工作負載可能可以提供足夠的執行效能。 但在大部分的情況下，散發資料行可以提供更佳的效能。  

依資料行散發之資料表的效能勝過循環配置資源資料表的最常見例子，是聯結兩個大型事實資料表。  

例如，如果您有一個依 order_id 散發的訂單資料表，以及一個也是依 order_id 散發的交易資料表，當您根據 order_id 將訂單資料聯結至交易資料表時，此查詢會變成傳遞查詢。 這將會排除資料移動作業。 較少的步驟代表較快的查詢。 較少的資料移動也會讓查詢更快。

> [!NOTE]
> 載入分散式資料表時，您的傳入資料不應該根據散發索引鍵排序。 這麼做將會使載入變慢。

下面所提供的文章連結將提供透過選取散發資料行來改善效能的其他詳細資料。 此外，您也可以找到如何在 CREATE TABLE 陳述式的 WITH 子句中定義分散式資料表的相關資訊：

- [資料表概觀](develop-tables-overview.md)
- [資料表散發](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [選取資料表發佈](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>不要過度執行資料分割

雖然分割資料對於透過分割切換來維護資料，或是透過分割消除來最佳化掃描而言可能會很有效，但太多的資料分割會讓查詢變慢。  通常，在 SQL Server 上運作良好的高細微性分割策略，可能無法在 SQL 集區上運作良好。  

如果每個資料分割的資料列少於 1 百萬個，太多的資料分割可能會降低叢集資料行存放區索引的效率。 SQL 集區會自動將您的資料分割成 60 個資料庫。 因此，如果您建立具有 100 個分割區的資料表，則結果會是 6000 個分割區。 每個工作負載都不同，因此最佳建議是對資料分割進行實驗，以找出最適合您工作負載的方式。  

其中一個可考慮的選項，是使用低於您利用 SQL Server 所實作的細微性。 例如，考慮使用每週或每月資料分割，而不是每日資料分割。

如需資料分割的詳細資訊，請參閱[資料表資料分割](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="minimize-transaction-sizes"></a>將交易大小最小化

INSERT、UPDATE 及 DELETE 陳述式都會在交易中執行。 當其失敗時，就必須加以復原。 為了降低長時間復原的可能性，請盡可能將交易大小最小化。  可以透過將 INSERT、UPDATE 及 DELETE 陳述式分成小部分，來達成交易大小的最小化。 例如，如果您預期您的 INSERT 需要 1 小時，您可以將 INSERT 分成四個部分。 每次執行都將會縮短為 15 分鐘。  

> [!TIP]
> 利用特殊的最低限度記錄案例 (例如 CTAS、TRUNCATE、DROP TABLE 或 INSERT) 來清空資料表，以降低復原的風險。  

另一個消除回復的作法是使用「僅中繼資料」作業 (像是資料分割切換) 進行資料管理。  例如，您可以每月分割您的資料，而不是執行 DELETE 陳述式以刪除資料表中 order_date 位於 2001 年 10 月的所有資料列。 然後您可以將具有資料的分割區，切換為來自另一個資料表的空資料區 (請參閱 ALTER TABLE 範例)。  

針對未分割的資料表，請考慮使用 CTAS 將您想要保留的資料寫入資料表中，而不是使用 DELETE。  就算 CTAS 需要的時間一樣長，其執行起來仍然比較安全，因為其具有最小的交易記錄，且必要時可以快速地取消。

下列文章包含此節相關內容的詳細資訊：

- [如選取建立資料表 (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [了解交易](develop-transactions.md)
- [最佳化交易](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [資料表分割](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>減少查詢結果大小

減少查詢結果大小可協助您避免由大型查詢結果造成的用戶端問題。  您可以編輯您的查詢，以減少傳回的資料列數目。 某些查詢產生工具可讓您將 "top N" 語法新增至每個查詢。  您也可以透過 CETAS 將查詢結果存至暫存資料表，然後使用 PolyBase 匯出進行下層處理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小的可能資料行大小

在定義 DDL 時，請使用可支援您的資料的最小資料類型，這麼做將能改善查詢效能。  這建議對 CHAR 和 VARCHAR 資料行尤其重要。  如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。  避免將所有字元資料行定義為較大的預設長度。  此外，請在只需要 VARCHAR 的情況下將資料行定義為 VARCHAR，而非使用 NVARCHAR。

請參閱[資料表預覽](develop-tables-overview.md)、[資料表資料類型](develop-tables-data-types.md)及 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文章，以取得與上述資訊相關之基本概念的詳細檢閱。

## <a name="use-temporary-heap-tables-for-transient-data"></a>針對暫時性資料使用暫存堆積資料表

當您暫時將資料登陸於 SQL 集區上時，堆積資料表通常能使整體程序更加快速。  如果您只要在執行其他轉換之前暫存載入的資料，則將資料表載入堆積資料表將會遠快於將資料載入叢集資料行存放區資料表。  

將資料載入暫存資料表的載入速度，也會比將資料表載入永久儲存體更快速。  暫存資料表會以 "#" 開頭，且只有建立該資料表的工作階段才能夠加以存取。 因此，其只能在有限的案例下運作。 堆積資料表是在 CREATE TABLE 的 WITH 子句中定義。  如果您使用暫存資料表，請記得也在該暫存資料表上建立統計資料。

如需其他指導方針，請參閱[暫存資料表](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)，以及 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文章。

## <a name="optimize-clustered-columnstore-tables"></a>將叢集資料行存放區資料表最佳化

叢集資料行存放區索引是將資料儲存在 SQL 集區中最有效率的方式之一。  根據預設，SQL 集區中的資料表會建立為「叢集資料行存放區」。  為了讓資料行存放區資料表的查詢獲得最佳效能，良好的區段品質很重要。  當資料列在記憶體不足的狀態下寫入資料行存放區資料表時，資料行存放區區段品質可能會降低。  

可以依壓縮資料列群組中的資料列數目來測量區段品質。 如需偵測和改善叢集資料行存放區資料表區段品質的逐步指示，請參閱[資料表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中的[資料行存放區索引品質不佳的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)。  

由於高品質的資料行存放區區段很重要，因此最好使用中型或大型資源類別中的使用者識別碼來載入資料。 使用較低的[資料倉儲單位](resource-consumption-models.md)，表示您想要將更大型的資源類別指派給正在載入的使用者。

資料行存放區資料表通常不會將資料推送到壓縮的資料行存放區區段中，直到每個資料表都有超過 1 百萬個資料列為止。 每個 SQL 集區資料表都會分割成 60 個資料表。 因此，除非資料表有超過 6 千萬個資料列，否則資料行存放區資料表並無法使查詢受益。  

> [!TIP]
> 對於包含低於 6 千萬個資料列的資料表，具有資料行存放區索引可能不是最佳的解決方案。  

如果您將資料分割，每個分割區都必須有 1 百萬個資料列，才能受益於叢集資料行存放區索引。  針對有 100 個分割區的資料表，其至少必須擁有 60 億個資料列才能受益於叢集資料行存放區 (60 個散發 *100 個分割區* 1 百萬個資料列)。  

如果您的資料表沒有 60 億個資料列，您主要有兩個選項。 您可以減少分割區數目，或考慮改為使用堆積資料表。  使用次要索引搭配堆積資料表 (而非資料行存放區資料表) 也可能是值得進行的實驗，以查看是否可以獲得較佳的效能。

查詢資料行存放區資料表時，如果您只選取您需要的資料行，查詢執行會更快速。  如需資料表和資料行存放區索引的詳細資訊，請參閱[資料表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[資料行存放區索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)，以及[重建資料行存放區索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)文章。

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用較大的資源類別來改善查詢效能

SQL 集區會使用資源群組，作為將記憶體配置給查詢的方式。 一開始，系統會將所有使用者指派到小型資源類別，此類別會授與每個散發 100 MB 的記憶體。  一律會有 60 個散發。 系統最少會提供 100 MB 給每個散發。 整個系統的記憶體配置總計為 6,000 MB，或是接近 6 GB。  

有些查詢，像是大型聯結或載入叢集資料行存放區資料表，將受益於較大的記憶體配置。  這對某些查詢 (例如純掃描) 將不會有任何益處。 利用較大的資源類別會影響並行處理。 因此，在將所有使用者移動至較大的資源類別之前，請先記得這些事實。

如需資源類別的其他資訊，請參閱[適用於工作負載管理的資源類別](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用較小的資源類別來增加並行存取

如果您在使用者查詢中發現長時間的延遲，您的使用者可能是在較大的資源類別中執行。 此案例會提升並行存取插槽的耗用量，其可能會導致系統將其他查詢排入佇列。  若要判斷系統是否將使用者的查詢排入佇列，請執行 `SELECT * FROM sys.dm_pdw_waits` 以查看是否會傳回任何資料列。

[適用於工作負載管理的資源類別](../sql-data-warehouse/resource-classes-for-workload-management.md)和 [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文章將能提供詳細資訊。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 對查詢進行監視和最佳化

SQL 集區有數個 DMV 可用來監視查詢執行。  下列監視相關文章會逐步解說如何檢視執行中查詢的詳細資料。  若要在這些 DMV 中快速找到查詢，可在您的查詢中使用 LABEL 選項。 如需其他詳細資訊，請參閱下列清單所包含的文章：

- [使用 DMV 監視工作負載](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [LABEL](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>後續步驟

另請參閱[疑難排解](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中的常見問題和解決方案。

如果您需要此文章未提供的資訊，請搜尋[適用於 Azure Synapse 的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) \(英文\)，其為您可以向其他使用者與 SQL 集區產品小組提出問題的地方。  

我們會主動監看這個論壇，以確保您的問題有其他使用者或是我們回答。  如果您比較想在 Stack Overflow 上提出您的問題，我們也有 [Azure SQL 集區 Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw) \(英文\)。

針對功能要求，請使用 [Azure SQL 集區意見反應](https://feedback.azure.com/forums/307516-sql-data-warehouse) \(英文\) 頁面。  加入您的要求或為其他要求投票，可協助我們專注在最具需求的功能上。
