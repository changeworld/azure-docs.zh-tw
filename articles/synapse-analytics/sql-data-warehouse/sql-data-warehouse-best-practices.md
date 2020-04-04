---
title: Azure 突觸分析(以前的 SQL DW)中突觸 SQL 池的最佳做法
description: 在 Azure 突觸分析(以前的 SQL DW)中為 SQL 池開發解決方案的建議和最佳實務。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 0a2a49546a31f6d767b5e89348dc6b703278d877
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633639"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Azure 突觸分析(以前的 SQL DW)中突觸 SQL 池的最佳做法

本文是一系列最佳實踐,可説明您從[SQL 池](sql-data-warehouse-overview-what-is.md)部署中獲得最佳性能。  本文的目的是為您提供一些基本指導,並突出重要的重點領域。  

## <a name="reduce-cost-with-pause-and-scale"></a>利用暫停和調整來降低成本

如需透過暫停和調整來降低成本的詳細資訊，請參閱[管理計算](sql-data-warehouse-manage-compute-overview.md)。

## <a name="maintain-statistics"></a>維護統計資料

SQL 池可以配置為自動檢測和創建列上的統計資訊。  優化程式創建的查詢計劃僅與可用統計資訊一樣好。  

我們建議您為資料庫啟用AUTO_CREATE_STATISTICS,並每天或每次載入後更新統計資訊,以確保查詢中使用的列統計信息始終保持最新。

如果您發現更新所有統計資訊需要很長時間,則可能需要嘗試對哪些列需要頻繁統計資訊更新進行更選擇性。 例如，您可能想要更新您每天都要加入新值的日期資料行。

> [!TIP]
> 通過更新聯接中涉及的列、WHERE 子句中使用的列和 GROUP BY 中的列,您將獲得最大的好處。

另請參考[管理表統計資訊](sql-data-warehouse-tables-statistics.md),[建立統計資訊](https://msdn.microsoft.com/library/ms188038.aspx)與[更新統計資訊](https://msdn.microsoft.com/library/ms187348.aspx)。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 對查詢進行監視和最佳化

SQL 池有多個可用於監視查詢執行的 DMV。  使用 DMV 監視工作負荷的文章詳細介紹了有關如何查看執行查詢詳細資訊的分步說明。  

若要在這些 DMV 中快速找到查詢，可在您的查詢中使用 LABEL 選項。

另請參閱使用 DMV、LABEL、OPTION、sys.dm_exec_sessions、sys.dm_pdw_exec_requests、sys.dm_pdw_request_steps、sys.dm_pdw_sql_requests、sys.dm_pdw_dms_workers、DBCC [OPTION](https://msdn.microsoft.com/library/ms190322.aspx) [sys.dm_exec_sessions]( https://msdn.microsoft.com/library/ms176013.aspx) [sys.dm_pdw_exec_requests](https://msdn.microsoft.com/library/mt203887.aspx) [sys.dm_pdw_request_steps](https://msdn.microsoft.com/library/mt203913.aspx) [sys.dm_pdw_sql_requests](https://msdn.microsoft.com/library/mt203889.aspx) [sys.dm_pdw_dms_workers](https://msdn.microsoft.com/library/mt203878.aspx) [PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)和[sys.dm_pdw_waits](https://msdn.microsoft.com/library/mt203893.aspx)[來監視您的工作負載](sql-data-warehouse-manage-monitor.md)。 [LABEL](sql-data-warehouse-develop-label.md)

## <a name="tune-query-performance-with-new-product-enhancements"></a>使用新產品增強功能調整查詢效能

- [使用具體化檢視進行效能微調](performance-tuning-materialized-views.md)
- [透過已排序的叢集資料行存放區索引進行效能微調](performance-tuning-ordered-cci.md)
- [使用結果集快取進行效能微調](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>將 INSERT 陳述式群組為批次

一次性載入到具有 INSERT 語句的小表,甚至定期重新載入查找,對於您的需求`INSERT INTO MyLookup VALUES (1, 'Type 1')`(如 )可能運行良好。  

不過，如果您一整天都得載入數千或數百萬個資料列，您可能會發現單一 INSERT 跟不上您的需求。  所以，請開發您自己的程序將它們寫入檔案，以及另一個程序定期執行並載入此檔案。

另請參閱[INSERT](https://msdn.microsoft.com/library/ms174335.aspx)。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 將資料快速載入及匯出

SQL 池支援透過多個工具(包括 Azure 資料工廠、PolyBase 和 BCP)載入和匯出數據。  若是小量的資料，效能不是那麼重要，任何工具都可以滿足您的需求。  不過，當您要載入或匯出大量資料，或者需要快速的效能時，PolyBase 是最佳選擇。  

PolyBase 旨在利用 MPP(大規模並行處理)架構,並將載入和匯出數據比任何其他工具快。  您可使用 CTAS 或 INSERT INTO 來執行 PolyBase 載入。  

> [!TIP]
> 使用 CTAS 可以減少交易記錄，是載入資料最快的方法。

Azure Data Factory 也支援 PolyBase 載入，並且可以達到與 CTAS 類似的效能。  PolyBase 支援各種不同的檔案格式，包括 Gzip 檔案。  
  
> [!NOTE]
> 要最大化使用 gzip 文本檔時的輸送量,請將檔分解為 60 個或更多檔,以最大化負載的並行性。   如需更快的總輸送量，請考慮同時載入資料。

另請參閱[載入資料](design-elt-data-loading.md)、[使用 PolyBase 的指南](guidance-for-loading-data.md)[、SQL 池載入模式和策略](https://blogs.msdn.microsoft.com/sqlcat/20../../),[使用 Azure 資料工廠載入資料]( ../../data-factory/load-azure-sql-data-warehouse.md)、使用https://msdn.microsoft.com/library/dn935026.aspx)Azure[資料工廠行動資料](../../data-factory/transform-data-using-machine-learning.md)(和[建立表作為選擇 (CTAS)。](sql-data-warehouse-develop-ctas.md)

## <a name="load-then-query-external-tables"></a>載入並查詢外部資料表

雖然 Polybase (也稱為外部資料表) 可能是載入資料最快的方法，卻並非最適合查詢。 Polybase 表目前僅支援 Azure blob 檔案和 Azure 資料湖儲存。 這些檔案沒有任何支援的計算資源。  

因此,SQL 池無法卸載此工作,因此必須通過將其載入到 tempdb 來讀取整個檔才能讀取數據。  所以，如果您有數個將會查詢此資料的查詢，最好能一次載入此資料，並讓查詢使用本機資料表。

另請參閱[使用 PolyBase 的指南](guidance-for-loading-data.md)。

## <a name="hash-distribute-large-tables"></a>雜湊分散大型資料表

根據預設，資料表是以「循環配置資源」方式分散。  這可讓使用者更容易開始建立資料表，而不必決定應該如何分散其資料表。  循環配置資源的資料表在某些工作負載中執行良好，但某些狀況下選取分散資料行的執行效能會更好。  

依資料行分散資料表的效能遠勝於循環配置資源資料表的最常見例子，是聯結兩個大型事實資料表。  

例如，如果您有一個依 order_id 分散的訂單資料表，以及一個也是依 order_id 分散的交易資料表，當您將訂單資料聯結至交易資料表上的 order_id，此查詢會變成傳遞查詢，也就是資料移動作業會被消除。  較少的步驟代表較快的查詢。  較少的資料移動也會讓查詢更快。  

> [!TIP]
> 載入分散的資料表時，請確定您的內送資料的分散式索引鍵沒有排序，因為這會拖慢載入。  

有關選擇分發列如何提高性能以及如何在 CREATE TABLE 語句的 WITH 子句中定義分散式表的詳細資訊,請參閱以下連結。

另請參考[表概述](sql-data-warehouse-tables-overview.md)、[表分配](sql-data-warehouse-tables-distribute.md)、[選擇表分配](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、[建立表](https://msdn.microsoft.com/library/mt203953.aspx)格、[建立表做為 SELECT](https://msdn.microsoft.com/library/mt204041.aspx)。

## <a name="do-not-over-partition"></a>不要過度執行資料分割

雖然分區數據可以通過分區切換或通過消除分區來優化掃描來有效地維護數據,但分區過多可能會減慢查詢速度。  通常,粒度分區策略(在 SQL Server 中可能運行良好)在 SQL 池中可能不太工作。  

如果每個資料分割的資料列少於 1 百萬，太多個資料分割也會減少叢集資料行存放區索引的效率。  請記住,在後台,SQL 池會將您的數據分區到 60 個資料庫中,因此,如果創建具有 100 個分區的表,這實際上會導致 6000 個分區。  

每個工作負載都不同，因此最佳建議是嘗試不同的分割，找出最適合您工作負載的分割。  請考慮比您的 SQL Server 上運作良好的資料粒度更低的粒度。  例如，考慮使用每週或每月資料分割，而不是每日資料分割。

另請參閱[表分區](sql-data-warehouse-tables-partition.md)。

## <a name="minimize-transaction-sizes"></a>將交易大小最小化

在交易中執行的 INSERT、UPDATE、DELETE 陳述式，失敗時必須回復。  為了將長時間回復的可能性降到最低，請盡可能將交易大小最小化。  這可以透過將 INSERT、UPDATE、DELETE 陳述式分成小部分來達成。  

例如,如果您有一個"插入",如果可能,您需要 1 小時,請將 INSERT 分成四個部分,每個部分將在 15 分鐘內執行。  利用特殊的最小日誌記錄案例(如 CTAS、TRUNCATE、DROP TABLE 或插入空錶)來降低回滾風險。  

另一個消除回復的作法是使用「僅中繼資料」作業 (像是資料分割切換) 進行資料管理。  例如,而不是執行 DELETE 語句來刪除表中order_date在 2001 年 10 月的所有行,您可以每月對資料進行分區,然後用另一個表的空分區的數據切換出分區(請參閱[ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)範例)。  

對於未分區的表,請考慮使用 CTAS 將要保存在表中的數據寫入,而不是使用 DELETE。  如果 CTAS 需要相同的時間,則運行該操作更安全,因為它具有最少的事務日誌記錄,如果需要,可以快速取消。

另請參閱[瞭解事務](sql-data-warehouse-develop-transactions.md)、[優化事務](sql-data-warehouse-develop-best-practices-transactions.md)、[表分區](sql-data-warehouse-tables-partition.md)[、TRUNCATE、ALTER](https://msdn.microsoft.com/library/ms177570.aspx)[TABLE](https://msdn.microsoft.com/library/ms190273.aspx)和[創建表作為選擇 (CTAS)。](sql-data-warehouse-develop-ctas.md)

## <a name="reduce-query-result-sizes"></a>減少查詢結果大小

此步驟可説明您避免由大型查詢結果引起的用戶端問題。  您可以編輯查詢以減少返回的行數。 某些查詢生成工具允許您向每個查詢添加「頂部 N」語法。  您還可以將查詢結果 CETAS 到臨時表,然後使用 PolyBase 匯出進行低級處理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小的可能資料行大小

定義 DDL 時,使用支援數據的最小數據類型將提高查詢性能。  此方法對於 CHAR 和 VARCHAR 列尤其重要。  

如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。  避免將所有字元資料行定義為較大的預設長度。  此外，將資料行定義為 VARCHAR (當它只需要這樣的大小時) 而非 NVARCHAR。

另請參考[表概述](sql-data-warehouse-tables-overview.md)、[表格類型](sql-data-warehouse-tables-data-types.md)、[建立表](https://msdn.microsoft.com/library/mt203953.aspx)格 。

## <a name="use-temporary-heap-tables-for-transient-data"></a>針對暫時性資料使用暫存堆積資料表

臨時登陸數據時,您可能會發現使用堆表會使整個進程更快。  如果您只要在執行其他轉換之前暫存載入的資料，則將資料表載入堆積資料表遠快於將資料載入叢集資料行存放區資料表。  

此外，將資料載入暫存資料表也會比將資料表載入永久儲存體更快速。  臨時表以"+"開頭,並且只能由創建它的會話訪問,因此它們只能在有限的情況下工作。

堆積資料表是在 CREATE TABLE 的 WITH 子句中定義。  如果您使用暫存資料表，請記得也在該暫存資料表上建立統計資料。

另請參考[暫照的暫照表](sql-data-warehouse-tables-temporary.md)、[建立表](https://msdn.microsoft.com/library/mt203953.aspx)格、[建立表做選擇](https://msdn.microsoft.com/library/mt204041.aspx)。

## <a name="optimize-clustered-columnstore-tables"></a>將叢集資料行存放區資料表最佳化

群集列存儲索引是將數據存儲在 SQL 池中的最有效方法之一。  預設情況下,SQL 池中的表創建為群集列存儲。  為了讓資料行存放區資料表的查詢獲得最佳效能，良好的區段品質很重要。  

當資料列在記憶體不足的狀態下寫入資料行存放區資料表時，資料行存放區區段品質可能會降低。  壓縮的資料列群組中的資料列數目可以測量區段品質。  有關檢測和改進群集列儲存表的段品質的分步說明,請參閱[表索引](sql-data-warehouse-tables-index.md)文章中[的"列存儲索引品質差的原因](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)"  

由於高品質的列存儲段很重要,因此最好使用大中型資源類中的使用者 IT 來載入數據。 使用較低的[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)，表示您想要將更大型的資源類別指派給正在載入的使用者。

由於列存儲表通常不會將數據推送到壓縮列存儲段,直到每個表超過 100 萬行,並且每個 SQL 池表被劃分為 60 個表,因此,根據經驗,除非表具有 6000 多行,否則列存儲表不會使查詢受益。  小於 6 千萬列的資料表使用資料行存放區索引似乎不太合理，  但也無傷大雅。  

此外，如果您將資料分割，則您要考慮的是每個資料分割必須有 1 百萬個資料列，使用叢集資料行存放區索引才有益。  如果表具有 100 個分區,則它需要至少 60 億行才能從群集列存儲中獲益(60 個分佈*100 個分區 100*個分區 100 萬行)。  

如果在此範例中，您的資料表並沒有 60 億個資料列，請減少資料分割數目，或考慮改用堆積資料表。  使用次要索引搭配堆積資料表而不是資料行存放區資料表，也可能是值得進行的實驗，看看是否可以獲得較佳的效能。

> [!TIP]
> 查詢資料行存放區資料表時，如果您只選取您需要的資料行，查詢執行會更快速。  

另請參閱[資料表索引](sql-data-warehouse-tables-index.md)、[資料行存放區索引指南](https://msdn.microsoft.com/library/gg492088.aspx)、[重建資料行存放區索引](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用較大的資源類別來改善查詢效能

SQL 池使用資源組作為將記憶體分配給查詢的一種方式。  開箱即用,所有使用者都分配給小資源類,該類每個分發授予 100 MB 的記憶體。  因為永遠會有 60 個散發，每個散發有至少 100 MB，整個系統的總記憶體配置為 6000 MB 或是剛好接近 6 GB。  

有些查詢，像是大型聯結或載入叢集資料行存放區資料表，將受益於較大的記憶體配置。  某些查詢(如純掃描)不會帶來任何好處。  但是,使用較大的資源類會減少併發性,因此在將所有用戶移動到大型資源類之前,您需要考慮此影響。

另請參閱[資源類,瞭解工作負載管理](resource-classes-for-workload-management.md)。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用較小的資源類別來增加並行存取

如果您注意到使用者查詢似乎具有長時間的延遲,則可能是您的使用者正在較大的資源類中運行,並且正在佔用許多併發槽,從而導致其他查詢排隊。  若要確認使用者的查詢是否被排入佇列，請執行 `SELECT * FROM sys.dm_pdw_waits` 看看是否會傳回任何資料列。

另請參閱[資源類,瞭解工作負載管理](resource-classes-for-workload-management.md)[,sys.dm_pdw_waits](https://msdn.microsoft.com/library/mt203893.aspx)。

## <a name="other-resources"></a>其他資源

另請參閱 [疑難排解](sql-data-warehouse-troubleshoot.md) 一文中的常見問題和解決方案。

如果在本文中未找到要查找的內容,請嘗試使用此頁面左側的"搜索文檔"來搜索所有 Azure Synapse 文檔。  [Azure 突觸論壇](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)是向其他使用者和 Azure Synapse 產品組發佈問題的地方。 我們會主動監看這個論壇，以確保您的問題有其他使用者或是我們回答。  

如果您喜歡在堆疊溢出上提問,我們也有一個 Azure[突觸堆疊溢出論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)。

請使用[Azure 同步反饋](https://feedback.azure.com/forums/307516-sql-data-warehouse)頁發出功能請求。  加入您的要求或票選其他要求確實可協助我們決定功能的優先順序。
