---
title: Azure Synapse Analytics (先前稱為 SQL DW) 中 Synapse SQL 集區的最佳做法
description: 針對開發 Azure Synapse Analytics (先前稱為 SQL DW) 中 SQL 集區解決方案的建議和最佳做法。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6978855e9b32a3842e76d02ef543d86cf0673019
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206643"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics (先前稱為 SQL DW) 中 Synapse SQL 集區的最佳做法

本文集合讓您從 [SQL 集區](sql-data-warehouse-overview-what-is.md)部署獲得最佳效能的最佳做法。  本文旨在提供您一些基本指引，並強調重點的重要區域。  

## <a name="reduce-cost-with-pause-and-scale"></a>利用暫停和調整來降低成本

如需透過暫停和調整來降低成本的詳細資訊，請參閱[管理計算](sql-data-warehouse-manage-compute-overview.md)。

## <a name="maintain-statistics"></a>維護統計資料

SQL 集區可以設定為自動偵測資料行並建立統計資料。  最佳化工具建立的查詢計劃只能利用可用的統計資料。  

建議您為資料庫啟用 AUTO_CREATE_STATISTICS，並讓統計資料每天或每次載入之後更新，以確保查詢中所用資料行的統計資料一直為最新狀態。

如果您發現更新所有統計資料所需時間太長，可能要更謹慎選擇哪些資料行需要頻繁更新。 例如，您可能想要更新您每天都要加入新值的日期資料行。

> [!TIP]
> 對牽涉聯結的資料行、WHERE 子句中使用的資料行、在 GROUP BY 中找到的資料行加以更新統計資料，可以獲得最大效益。

另請參閱[管理資料表的統計資料](sql-data-warehouse-tables-statistics.md)、[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 對查詢進行監視和最佳化

SQL 集區有數個 DMV 可用來監視查詢執行。  「使用 DMV 監視工作負載」一文會逐步解說如何查看執行中查詢的詳細資料。  

若要在這些 DMV 中快速找到查詢，可在您的查詢中使用 LABEL 選項。

另請參閱[使用 DMV 監視工作負載](sql-data-warehouse-manage-monitor.md)、[LABEL](sql-data-warehouse-develop-label.md)、[OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="tune-query-performance-with-new-product-enhancements"></a>使用新的產品增強功能調整查詢效能

- [使用具體化檢視進行效能微調](performance-tuning-materialized-views.md)
- [透過已排序的叢集資料行存放區索引進行效能微調](performance-tuning-ordered-cci.md)
- [使用結果集快取進行效能微調](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>將 INSERT 陳述式群組為批次

使用 INSERT 陳述式單次載入小型資料表、或甚至定期重新載入查閱，可能會和使用像 `INSERT INTO MyLookup VALUES (1, 'Type 1')` 這樣的陳述式一樣正常執行。  

不過，如果您一整天都得載入數千或數百萬個資料列，您可能會發現單一 INSERT 跟不上您的需求。  所以，請開發您自己的程序將它們寫入檔案，以及另一個程序定期執行並載入此檔案。

另請參閱 [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 將資料快速載入及匯出

SQL 集區支援透過數種工具 (包括 Azure Data Factory、PolyBase、BCP) 來載入及匯出資料。  若是小量的資料，效能不是那麼重要，任何工具都可以滿足您的需求。  不過，當您要載入或匯出大量資料，或者需要快速的效能時，PolyBase 是最佳選擇。  

PolyBase 利用 MPP (大量平行處理) 架構，因此載入及匯出巨量資料的速度比其他任何工具更快。  您可使用 CTAS 或 INSERT INTO 來執行 PolyBase 載入。  

> [!TIP]
> 使用 CTAS 可以減少交易記錄，是載入資料最快的方法。

Azure Data Factory 也支援 PolyBase 載入，並且可以達到與 CTAS 類似的效能。  PolyBase 支援各種不同的檔案格式，包括 Gzip 檔案。  
  
> [!NOTE]
> 若要在使用 gzip 文字檔案時獲得最大的輸送量，將檔案分成 60 個以上的檔案讓載入有最大化的平行處理。  如需更快的總輸送量，請考慮同時載入資料。

另請參閱[載入資料](design-elt-data-loading.md)、[PolyBase 使用指南](guidance-for-loading-data.md)、[SQL 集區載入模式和策略](https://blogs.msdn.microsoft.com/sqlcat/20../../)、[使用 Azure Data Factory 載入資料]( ../../data-factory/load-azure-sql-data-warehouse.md)、[使用 Azure Data Factory 移動資料](../../data-factory/transform-data-using-machine-learning.md)、[CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [Create table as select (CTAS)](sql-data-warehouse-develop-ctas.md)。

## <a name="load-then-query-external-tables"></a>載入並查詢外部資料表

雖然 Polybase (也稱為外部資料表) 可能是載入資料最快的方法，卻並非最適合查詢。 Polybase 資料表目前僅支援 Azure blob 檔案和 Azure Data Lake 儲存體。 這些檔案沒有任何支援的計算資源。  

因此，SQL 集區無法卸載此工作，因而必須將整個檔案載入 tempdb 以讀取資料。  所以，如果您有數個將會查詢此資料的查詢，最好能一次載入此資料，並讓查詢使用本機資料表。

另請參閱 [PolyBase 使用指南](guidance-for-loading-data.md)。

## <a name="hash-distribute-large-tables"></a>雜湊分散大型資料表

根據預設，資料表是以「循環配置資源」方式分散。  這可讓使用者更容易開始建立資料表，而不必決定應該如何分散其資料表。  循環配置資源的資料表在某些工作負載中執行良好，但某些狀況下選取分散資料行的執行效能會更好。  

依資料行分散資料表的效能遠勝於循環配置資源資料表的最常見例子，是聯結兩個大型事實資料表。  

例如，如果您有一個依 order_id 分散的訂單資料表，以及一個也是依 order_id 分散的交易資料表，當您將訂單資料聯結至交易資料表上的 order_id，此查詢會變成傳遞查詢，也就是資料移動作業會被消除。  較少的步驟代表較快的查詢。  較少的資料移動也會讓查詢更快。  

> [!TIP]
> 載入分散的資料表時，請確定您的內送資料的分散式索引鍵沒有排序，因為這會拖慢載入。  

關於選取分散資料行如何能提升效能，以及如何在 CREATE TABLE 陳述式的 WITH 子句中定義分散的資料表，如需詳細資訊請參閱以下的連結。

資料表[概觀](sql-data-warehouse-tables-overview.md)、[資料表散發](sql-data-warehouse-tables-distribute.md)、[選取資料表散發](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="do-not-over-partition"></a>不要過度執行資料分割

雖然分割資料可以讓資料維護變得有效率 (透過分割切換或最佳化掃描將分割消除)，但太多的資料分割會讓查詢變慢。  通常在 SQL Server 上運作良好的高資料粒度分割策略，可能無法在 SQL 集區上運作良好。  

如果每個資料分割的資料列少於 1 百萬，太多個資料分割也會減少叢集資料行存放區索引的效率。  請記住，SQL 集區在幕後為您將資料分割成 60 的資料庫，因此如果您建立有 100 個分割的資料表，實際上會導致 6000 個分割。  

每個工作負載都不同，因此最佳建議是嘗試不同的分割，找出最適合您工作負載的分割。  請考慮比您的 SQL Server 上運作良好的資料粒度更低的粒度。  例如，考慮使用每週或每月資料分割，而不是每日資料分割。

另請參閱[資料表分割](sql-data-warehouse-tables-partition.md)。

## <a name="minimize-transaction-sizes"></a>將交易大小最小化

在交易中執行的 INSERT、UPDATE、DELETE 陳述式，失敗時必須回復。  為了將長時間回復的可能性降到最低，請盡可能將交易大小最小化。  這可以透過將 INSERT、UPDATE、DELETE 陳述式分成小部分來達成。  

例如，如果您預期您的 INSERT 需要 1 小時，可能的話，將 INSERT 分成 4 個部分，每個執行 15 分鐘。  利用特殊的最低限度記錄案例，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空資料表，來降低回復的風險。  

另一個消除回復的作法是使用「僅中繼資料」作業 (像是資料分割切換) 進行資料管理。  例如，不要執行 DELETE 陳述式來刪除資料表中所有 order_date 為 2001 年 10 月的資料列，而是將資料每月分割後，再從另一個資料表將有空分割資料的分割調動出來 (請參閱 [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 範例)。  

針對未分割的資料表，請考慮使用 CTAS 將您想要保留的資料寫入資料表中，而不是使用 DELETE。  如果 CTAS 需要的時間一樣長，則較安全的作業，是在其具有最小交易記錄的條件下執行，且必要時可以快速地取消。

另請參閱[了解交易](sql-data-warehouse-develop-transactions.md)、[最佳化交易](sql-data-warehouse-develop-best-practices-transactions.md)、[資料表分割](sql-data-warehouse-tables-partition.md)、[TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [Create table as select (CTAS)](sql-data-warehouse-develop-ctas.md)。

## <a name="reduce-query-result-sizes"></a>減少查詢結果大小

此步驟可協助您避免由大型查詢結果造成的用戶端問題。  您可以編輯您的查詢，以減少傳回的資料列數目。 某些查詢產生工具可讓您將 "top N" 語法新增至每個查詢。  您也可以將查詢結果 CETAS 至暫存資料表，然後使用 PolyBase export 進行舊版處理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小的可能資料行大小

在定義 DDL 時，使用可支援您的資料的最小資料類型，將能夠改善查詢效能。  這種方法對 CHAR 和 VARCHAR 資料行尤其重要。  

如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。  避免將所有字元資料行定義為較大的預設長度。  此外，將資料行定義為 VARCHAR (當它只需要這樣的大小時) 而非 NVARCHAR。

另請參閱[資料表概觀](sql-data-warehouse-tables-overview.md)、[資料表的資料類型](sql-data-warehouse-tables-data-types.md)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="use-temporary-heap-tables-for-transient-data"></a>針對暫時性資料使用暫存堆積資料表

當您暫時登陸資料時，可能會發現使用堆積資料表會讓整個程序更快速。  如果您只要在執行其他轉換之前暫存載入的資料，則將資料表載入堆積資料表遠快於將資料載入叢集資料行存放區資料表。  

此外，將資料載入暫存資料表也會比將資料表載入永久儲存體更快速。  暫存資料表會以 "#" 開頭，且只有建立該資料表的工作階段才能夠存取，因此只能在有限的情況下運作。

堆積資料表是在 CREATE TABLE 的 WITH 子句中定義。  如果您使用暫存資料表，請記得也在該暫存資料表上建立統計資料。

另請參閱[暫存資料表](sql-data-warehouse-tables-temporary.md)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="optimize-clustered-columnstore-tables"></a>將叢集資料行存放區資料表最佳化

叢集資料行存放區索引是將資料儲存在 SQL 集區中最有效率的方式之一。  根據預設，SQL 集區中的資料表會建立為「叢集資料行存放區」。  為了讓資料行存放區資料表的查詢獲得最佳效能，良好的區段品質很重要。  

當資料列在記憶體不足的狀態下寫入資料行存放區資料表時，資料行存放區區段品質可能會降低。  壓縮的資料列群組中的資料列數目可以測量區段品質。  如需偵測和改善叢集資料行存放區資料表區段品質的逐步指示，請參閱[資料表索引](sql-data-warehouse-tables-index.md)一文中的[資料行存放區索引品質不佳的原因](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)。  

由於高品質的資料行存放區區段很重要，因此最好使用中型或大型資源類別中的使用者識別碼來載入資料。 使用較低的[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)，表示您想要將更大型的資源類別指派給正在載入的使用者。

由於資料行存放區資料表通常要等到每個資料表有超過 1 百萬個資料列之後才會將資料推送到壓縮的資料行存放區區段，而且每個 SQL 集區資料表分割成 60 個資料表，根據經驗法則，資料行存放區資料表對於查詢沒有好處，除非資料表有超過 6 千萬個資料列。  小於 6 千萬列的資料表使用資料行存放區索引似乎不太合理，  但也無傷大雅。  

此外，如果您將資料分割，則您要考慮的是每個資料分割必須有 1 百萬個資料列，使用叢集資料行存放區索引才有益。  如果資料表有 100 個分割區，則其至少必須擁有 60 億個資料列才會受益於叢集資料行存放區 (60 個散發 100 個分割區 100 萬個資料列)。  

如果在此範例中，您的資料表並沒有 60 億個資料列，請減少資料分割數目，或考慮改用堆積資料表。  使用次要索引搭配堆積資料表而不是資料行存放區資料表，也可能是值得進行的實驗，看看是否可以獲得較佳的效能。

> [!TIP]
> 查詢資料行存放區資料表時，如果您只選取您需要的資料行，查詢執行會更快速。  

另請參閱[資料表索引](sql-data-warehouse-tables-index.md)、[資料行存放區索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[重建資料行存放區索引](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用較大的資源類別來改善查詢效能

SQL 集區會使用資源群組，作為將記憶體配置給查詢的一種方式。  根據預設，所有使用者都會被指派小型資源類別，此類別授予每個散發 100 MB 的記憶體。  因為永遠會有 60 個散發，每個散發有至少 100 MB，整個系統的總記憶體配置為 6000 MB 或是剛好接近 6 GB。  

有些查詢，像是大型聯結或載入叢集資料行存放區資料表，將受益於較大的記憶體配置。  有些查詢，像是純掃描，則沒有任何好處。  但是，使用較大的資源類別會減少並行存取，因此您將所有的使用者移到大型資源類別之前，要先將這個影響列入考慮。

另請參閱[適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用較小的資源類別來增加並行存取

如果您注意到使用者查詢似乎長時間延遲，可能是您的使用者在較大資源類別中執行，佔用許多並行存取位置，而導致其他查詢排入佇列。  若要確認使用者的查詢是否被排入佇列，請執行 `SELECT * FROM sys.dm_pdw_waits` 看看是否會傳回任何資料列。

另請參閱[適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)、[sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="other-resources"></a>其他資源

另請參閱 [疑難排解](sql-data-warehouse-troubleshoot.md) 一文中的常見問題和解決方案。

如果您在此文件中找不到想要尋找的內容，請嘗試使用此頁面左邊的 [搜尋文件] 來搜尋所有 Azure Synapse Analytics 文件。  [Azure Synapse 的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)，可讓您將問題張貼至其他使用者和 Azure Synapse Analytics 產品群組。 我們會主動監看這個論壇，以確保您的問題有其他使用者或是我們回答。  

如果您比較想在 Stack Overflow上詢問您的問題，我們也有 [Azure Synapse Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)。

請使用 [Azure Synapse Analytics 意見反應](https://feedback.azure.com/forums/307516-sql-data-warehouse)頁面來提出功能要求。  加入您的要求或票選其他要求確實可協助我們決定功能的優先順序。
