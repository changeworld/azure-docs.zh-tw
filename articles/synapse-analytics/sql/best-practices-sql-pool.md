---
title: SQL 池的最佳實作
description: 在使用 SQL 池時,應瞭解的建議和最佳實踐。
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427792"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Azure 突觸分析中 SQL 池的最佳做法

本文提供了一系列最佳實踐,以説明您在 Azure 突觸分析中實現 SQL 池的最佳性能。 下面您將找到構建解決方案時需要關注的基本指南和重要領域。 每個部分都向您介紹一個概念,然後向您介紹更深入地介紹該概念的更詳細的文章。

## <a name="sql-pools-loading"></a>SQL 池載入

有關 SQL 池載入指南,請參閱[載入資料的指南](data-loading-best-practices.md)。

## <a name="reduce-cost-with-pause-and-scale"></a>利用暫停和調整來降低成本

有關通過暫停和延伸降低成本的詳細資訊,請參閱[管理計算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

## <a name="maintain-statistics"></a>維護統計資料

雖然 SQL Server 自動檢測和創建或更新列上的統計資訊,但 SQL 池需要手動維護統計資訊。 您需要維護統計資訊,以確保 SQL 池計劃得到優化。  最佳化工具建立的計劃只能利用可用的統計資料。

> [!TIP]
> 建立每個資料行的範本統計資料是開始使用統計資料的簡單方式。  

 更新統計資料和對您的資料做重大變更一樣重要。  保守的作法是每天或每次載入之後更新統計資料。  建立和更新統計資料的效能與成本之間總有一些取捨。

要縮短統計資訊維護時間,請選擇哪些列具有統計資訊,或者需要最頻繁的更新。 例如,您可能希望更新日期列,其中可能每天添加新值。 重點介紹聯接中涉及的列、WHERE 子句中使用的列以及 GROUP BY 中的列的統計資訊。

有關統計資訊的其他資訊,請參閱[管理表統計資訊](develop-tables-statistics.md)、[創建統計資訊](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[更新統計資訊](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)的文章。

## <a name="group-insert-statements-into-batches"></a>將 INSERT 陳述式群組為批次

一次性載入到帶有 INSERT 語句的小表`INSERT INTO MyLookup VALUES (1, 'Type 1')`,例如 ,根據您的需要,可能是最佳方法。 但是,如果您需要全天載入數千行或數百萬行,則單例 INSERTS 可能不是最佳選擇。

解決此問題的一種方法是開發一個寫入檔的進程,然後開發另一個進程定期載入此檔。 有關詳細資訊,請參閱[INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章。

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 將資料快速載入及匯出

SQL 池支援透過多個工具(包括 Azure 資料工廠、PolyBase 和 BCP)載入和匯出數據。  若是小量的資料，效能不是那麼重要，任何工具都可以滿足您的需求。  

> [!NOTE]
> 當您載入或匯出大量資料或需要更快的性能時,Polybase 是最佳選擇。

您可使用 CTAS 或 INSERT INTO 來執行 PolyBase 載入。 CTAS將最大限度地減少事務日誌記錄,是載入數據的最快方式。 Azure 數據工廠還支援 PolyBase 負載,並可以實現類似於 CTAS 的性能。 PolyBase 支援各種檔案格式,包括 Gzip 檔案。

要最大化使用 Gzip 文本檔時的輸送量,請將檔分解為 60 個或更多檔,以最大化負載的並行性。  如需更快的總輸送量，請考慮同時載入資料。 有關本節相關主題的其他資訊包含在以下文章中:

- [載入資料](data-loading-overview.md)
- [使用 PolyBase 指南](data-loading-best-practices.md)
- [Azure SQL 池載入模式和策略](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [使用 Azure 資料工廠載入資料](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [使用 Azure Data Factory 移動資料](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [建立表做選擇 (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>載入並查詢外部資料表

聚基不是查詢的最佳值。 SQL 池的 Polybase 表目前僅支援 Azure blob 檔和 Azure 資料湖儲存。 這些文件沒有任何支援它們的計算資源。 因此,SQL 池無法卸載此工作,必須通過將其載入到 tempdb 來讀取整個檔,以便它可以讀取數據。

如果有多個查詢用於查詢此數據,則最好載入此數據一次,並且讓查詢使用本地表。 進一步參考庫指南包含在[使用 PolyBase 文章的指南](data-loading-best-practices.md)中。

## <a name="hash-distribute-large-tables"></a>雜湊分散大型資料表

根據預設，資料表是以「循環配置資源」方式分散。   此預設值使用戶可以輕鬆地開始創建表,而無需決定如何分配其表。 對於某些工作負載,迴圈表可能運行得足夠多。 但是,在大多數情況下,分發列提供更好的性能。  

由執行「迴圈」表的列分佈的表的最常見示例是聯接兩個大型事實表。  

例如,如果訂單表由 order_id 分發,並且事務表也由 order_id分發,則當您在order_id將訂單表加入到交易記錄表時,此查詢將成為傳遞查詢。 然後消除數據移動操作。 較少的步驟代表較快的查詢。 較少的資料移動也會讓查詢更快。

> [!NOTE]
> 載入分散式表時,不應對傳入數據進行排序。 這樣做會減慢您的負載。

下面提供的文章連結將為您提供有關通過選擇分發列提高性能的其他詳細資訊。 此外,您還可以在 CREATE TABLE 語句的 WITH 子句中找到有關如何定義分散式表的資訊:

- [表概述](develop-tables-overview.md)
- [表分佈](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [選取資料表發佈](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>不要過度執行資料分割

雖然分區數據可以通過分區切換或通過消除分區來優化掃描來有效地維護數據,但分區過多可能會減慢查詢速度。  通常,在 SQL Server 上可能很好地工作的高粒度分區策略在 SQL 池中可能不起作用。  

如果每個分區的行少於 100 萬行,則分區過多可能會降低群集列存儲索引的有效性。 SQL 池會自動將數據分區為 60 個資料庫。 因此,如果創建具有 100 個分區的表,則結果為 6000 個分區。 每個工作負載都不同,因此最好嘗試分區,看看哪些工作負荷最適合您的工作負載。  

需要考慮的一個選項是使用小於使用 SQL Server 實現的粒度。 例如,請考慮使用每周或每月分區,而不是每日分區。

有關分區的詳細資訊,有關分區的介紹在[「表分區」](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文中進行了詳細介紹。

## <a name="minimize-transaction-sizes"></a>將交易大小最小化

在事務中執行的插入、更新和刪除語句。 當他們失敗時,必須回滾它們。 要降低長時間回滾的可能性,請盡可能最小化事務大小。  通過將 INSERT、更新和刪除語句劃分為多個部分,可以最大限度地減少事務大小。 例如,如果您有一個"插入",預計需要 1 小時,則可以將 INSERT 分解為四個部分。 然後,每次運行將縮短為 15 分鐘。  

> [!TIP]
> 利用特殊的最小日誌記錄案例(如 CTAS、TRUNCATE、DROP TABLE 或 INSERT)到空錶,以降低回滾風險。  

另一個消除回復的作法是使用「僅中繼資料」作業 (像是資料分割切換) 進行資料管理。  例如,您可以每月對數據進行分區,而不是執行 DELETE 語句來刪除表中order_date 2001 年 10 月的所有行。 然後,您可以使用另一個表的空分區的數據切換出分區(請參閱 ALTER TABLE 範例)。  

對於未分區的表,請考慮使用 CTAS 將要保存在表中的數據寫入,而不是使用 DELETE。  如果 CTAS 需要相同的時間,則運行會更安全,因為它具有最少的事務日誌記錄,如果需要,可以快速取消。

有關本節相關內容的更多資訊,請參閱以下文章:

- [建立表做選擇 (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [了解交易](develop-transactions.md)
- [最佳化交易](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [資料表分割](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [截圖表](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [變更表](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>減少查詢結果大小

減小查詢結果大小有助於避免由大型查詢結果引起的用戶端問題。  您可以編輯查詢以減少返回的行數。 某些查詢生成工具允許您向每個查詢添加「頂部 N」語法。  您還可以將查詢結果 CETAS 到臨時表,然後使用 PolyBase 匯出進行低級處理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小的可能資料行大小

定義 DDL 時,請使用支援數據的最小資料類型,這樣做將提高查詢性能。  這項建議對CHAR和VARCHAR列尤其重要。  如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。  避免將所有字元資料行定義為較大的預設長度。  此外,當需要時將列定義為 VARCHAR,而不是使用 NVARCHAR。

請參閱[表概述](develop-tables-overview.md)、[表數據類型](develop-tables-data-types.md)和[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章,以便更詳細地回顧與上述資訊相關的基本概念。

## <a name="use-temporary-heap-tables-for-transient-data"></a>針對暫時性資料使用暫存堆積資料表

當您暫時將數據降落在 SQL 池上時,堆表通常會使整個進程更快。  如果僅在運行更多轉換之前載入資料來暫存資料,則將表載入到堆表比將資料載入到群集列儲存表要快。  

將資料載入到暫存表也會比將表載入到永久存儲快得多。  臨時表以"+"開頭,並且只能由創建它的會話訪問。 因此,它們只能在有限的情況下工作。 堆積資料表是在 CREATE TABLE 的 WITH 子句中定義。  如果您使用暫存資料表，請記得也在該暫存資料表上建立統計資料。

有關其他指導,請參閱[暫存表](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、[創建表](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[創建 TABLE 作為 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章。

## <a name="optimize-clustered-columnstore-tables"></a>將叢集資料行存放區資料表最佳化

群集列存儲索引是將數據存儲在 SQL 池中的最有效方法之一。  預設情況下,SQL 池中的表創建為群集列存儲。  為了讓資料行存放區資料表的查詢獲得最佳效能，良好的區段品質很重要。  當資料列在記憶體不足的狀態下寫入資料行存放區資料表時，資料行存放區區段品質可能會降低。  

段品質可以按壓縮行組中的行數來衡量。 有關檢測和改進群集列儲存表的段品質的分步說明,請參閱[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)文章中[的"列存儲索引品質差的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)"  

由於高品質的列存儲段很重要,因此最好使用大中型資源類中的使用者 IT 來載入數據。 使用較低的[資料倉儲單位](resource-consumption-models.md)，表示您想要將更大型的資源類別指派給正在載入的使用者。

柱存儲表通常不會將數據推送到壓縮列存儲段,直到每個表超過 100 萬行。 每個 SQL 池表被分區為 60 個表。 因此,除非表具有 6000 多萬行,否則列存儲表不會使查詢受益。  

> [!TIP]
> 對於行數少於 6000 萬行的表,具有列存儲索引可能不是最佳解決方案。  

如果對數據進行分區分區,則每個分區將需要有 100 萬行才能從群集列存儲索引中獲益。  對於具有 100 個分區的表,它需要至少 60 億行才能從群集列存儲中獲益(60 個分佈*100 個分區 100*個分區 100 萬行)。  

如果表沒有 60 億行,則有兩個主要選項。 要麼減少分區數,要麼考慮改用堆表。  可能還值得嘗試,看看通過使用具有輔助索引的堆表而不是列存儲表是否可以獲得更好的性能。

查詢資料行存放區資料表時，如果您只選取您需要的資料行，查詢執行會更快速。  有關表和列存儲索引的詳細資訊,可在[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[列庫索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[重建列儲存索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)文章中找到。

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用較大的資源類別來改善查詢效能

SQL 池使用資源組作為將記憶體分配給查詢的一種方式。 最初,所有使用者都分配給小資源類,該類每個分發分配 100 MB 的記憶體。  總是有 60 個分佈。 每個分佈至少為 100 MB。 系統範圍內存分配總量為 6,000 MB,或略低於 6 GB。  

有些查詢，像是大型聯結或載入叢集資料行存放區資料表，將受益於較大的記憶體配置。  某些查詢(如純掃描)將看不到任何好處。 利用較大的資源類會影響併發性。 因此,在將所有用戶移動到大型資源類之前,您需要牢記這些事實。

有關資源類的其他資訊,請參閱[工作負荷管理的資源類](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用較小的資源類來增加併發性

如果您注意到使用者查詢延遲時間長,則使用者可能在更大的資源類中運行。 此方案可促進併發槽的消耗,這可能導致其他查詢排隊。  要確定使用者查詢是否排隊,請運行`SELECT * FROM sys.dm_pdw_waits`以查看是否返回任何行。

[以工作負載管理和](../sql-data-warehouse/resource-classes-for-workload-management.md) [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)文章的資源類將為您提供更多資訊。

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 對查詢進行監視和最佳化

SQL 池有多個可用於監視查詢執行的 DMV。  下面的監視文章將引導您完成有關如何查看執行查詢詳細資訊的分步說明。  若要在這些 DMV 中快速找到查詢，可在您的查詢中使用 LABEL 選項。 有關其他詳細資訊,請參閱以下清單中包含的文章:

- [使用 DMV 監視工作負載](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [標籤](develop-label.md)
- [選項](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>後續步驟

另請參閱[疑難解答](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)文章,瞭解常見問題和解決方案。

如果您需要本文中未提供的資訊,請使用此頁面左側的「搜尋文檔」來搜索所有 SQL 池文檔。  [SQL 池論壇](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)是向其他使用者和 SQL 池產品組提出問題的地方。  

我們會主動監看這個論壇，以確保您的問題有其他使用者或是我們回答。  如果您喜歡在堆疊溢出上提問,我們也有一個 Azure [SQL 池堆疊溢出論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)。

對於功能請求,請使用[Azure SQL 池反饋](https://feedback.azure.com/forums/307516-sql-data-warehouse)頁。  添加您的請求或上投票的其他請求有助於我們專注於最需要的功能。
