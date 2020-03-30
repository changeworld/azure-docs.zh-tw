---
title: 開發最佳做法
description: 在為 Synapse SQL 池開發解決方案時，應瞭解的建議和最佳實踐。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a4902d2817c98b002b9767ad55d7ac60f5cb0c93
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350714"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Synapse SQL 池的開發最佳實踐
本文說明您在開發資料倉儲解決方案時可遵循的指引和最佳做法。 

## <a name="tune-query-performance-with-new-product-enhancements"></a>使用新產品增強功能調整查詢性能  
- [使用具體化檢視進行效能微調](performance-tuning-materialized-views.md)
- [透過已排序的叢集資料行存放區索引進行效能微調](performance-tuning-ordered-cci.md)
- [使用結果集快取進行效能微調](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>利用暫停和調整來降低成本
如需透過暫停和調整來降低成本的詳細資訊，請參閱[管理計算](sql-data-warehouse-manage-compute-overview.md)。 

## <a name="maintain-statistics"></a>維護統計資料
Synapse SQL 池可以配置為自動檢測和創建列上的統計資訊。  優化程式創建的查詢計劃僅與可用統計資訊一樣好。  我們建議您為資料庫啟用AUTO_CREATE_STATISTICS，並每天或每次載入後更新統計資料，以確保查詢中使用的列統計資訊始終保持最新。 

如果您發現更新所有統計資訊需要很長時間，則可能需要嘗試對哪些列需要頻繁統計資訊更新進行更選擇性。 例如，您可能想要更新您每天都要加入新值的日期資料行。 **通過更新聯接中涉及的列、WHERE 子句中使用的列和 GROUP BY 中的列，您將獲得最大的好處。**

另請參閱[管理資料表的統計資料](sql-data-warehouse-tables-statistics.md)、[CREATE STATISTICS](sql-data-warehouse-tables-statistics.md)、[UPDATE STATISTICS](sql-data-warehouse-tables-statistics.md#updating-statistics)

## <a name="hash-distribute-large-tables"></a>雜湊分散大型資料表
根據預設，資料表是以「循環配置資源」方式分散。  這種設計使使用者無需決定如何分配表即可輕鬆開始創建表。  循環配置資源的資料表在某些工作負載中執行良好，但某些狀況下選取分散資料行的執行效能會更好。  依資料行分散資料表的效能遠勝於循環配置資源資料表的最常見例子，是聯結兩個大型事實資料表。  例如，如果您有一個依 order_id 分散的訂單資料表，以及一個也是依 order_id 分散的交易資料表，當您將訂單資料聯結至交易資料表上的 order_id，此查詢會變成傳遞查詢，也就是資料移動作業會被消除。  較少的步驟代表較快的查詢。  較少的資料移動也會讓查詢更快。  此解釋只劃傷表面。 載入分散的資料表時，請確定您的內送資料的分散式索引鍵沒有排序，因為這會拖慢載入。  有關選擇分發列如何提高性能以及如何在 CREATE TABLE 語句的 WITH 子句中定義分散式表的詳細資訊，請參閱以下連結。

另請參閱[資料表概觀](sql-data-warehouse-tables-overview.md)、[資料表散發](sql-data-warehouse-tables-distribute.md)、[選取資料表散發](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、[CREATE TABLE](sql-data-warehouse-tables-overview.md)、[CREATE TABLE AS SELECT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>不要過度執行資料分割
雖然分區資料可以通過分區切換或通過消除分區來優化掃描來有效地維護資料，但分區過多可能會減慢查詢速度。  通常，在 SQL Server 上可能很好地工作的高細微性分區策略在 SQL 池中可能不起作用。  如果每個資料分割的資料列少於 1 百萬，太多個資料分割也會減少叢集資料行存放區索引的效率。  請記住，在後臺，SQL 池會將您的資料分區到 60 個資料庫中，因此，如果創建具有 100 個分區的表，這實際上會導致 6000 個分區。  每個工作負載都不同，因此最佳建議是嘗試不同的分割，找出最適合您工作負載的分割。  請考慮比您的 SQL Server 上運作良好的資料粒度更低的粒度。  例如，考慮使用每週或每月資料分割，而不是每日資料分割。

另請參閱 [資料表分割](sql-data-warehouse-tables-partition.md)

## <a name="minimize-transaction-sizes"></a>將交易大小最小化
在交易中執行的 INSERT、UPDATE、DELETE 陳述式，失敗時必須回復。  為了將長時間回復的可能性降到最低，請盡可能將交易大小最小化。  這可以透過將 INSERT、UPDATE、DELETE 陳述式分成小部分來達成。  例如，如果您有一個 INSERT，如果可能，您需要 1 小時，請將 INSERT 分成四個部分，每個部分將在 15 分鐘內運行。  利用特殊的最低限度記錄案例，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空資料表，來降低回復的風險。  另一個消除回復的作法是使用「僅中繼資料」作業 (像是資料分割切換) 進行資料管理。  例如，不要執行 DELETE 陳述式來刪除資料表中所有 order_date 為 2001 年 10 月的資料列，而是將資料每月分割後，再從另一個資料表將有空分割之資料的分割調動出來 (請參閱 ALTER TABLE 範例)。  針對未分割的資料表，請考慮使用 CTAS 將您想要保留的資料寫入資料表中，而不是使用 DELETE。  如果 CTAS 需要相同的時間，則運行該操作更安全，因為它具有最少的事務日誌記錄，如果需要，可以快速取消。

另請參閱[瞭解事務](sql-data-warehouse-develop-transactions.md)、[優化事務](sql-data-warehouse-develop-best-practices-transactions.md)、[表分區](sql-data-warehouse-tables-partition.md)[、TRUNCATE TABLE、ALTER](https://msdn.microsoft.com/library/ms177570.aspx) [TABLE、](https://msdn.microsoft.com/library/ms190273.aspx)[創建表作為選擇表 （CTAS）。](sql-data-warehouse-develop-ctas.md)

## <a name="use-the-smallest-possible-column-size"></a>使用最小的可能資料行大小
定義 DDL 時，使用支援資料的最小資料類型將提高查詢性能。  這對 CHAR 和 VARCHAR 資料行尤其重要。  如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。  避免將所有字元資料行定義為較大的預設長度。  此外，將資料行定義為 VARCHAR (當它只需要這樣的大小時) 而非 NVARCHAR。

另請參閱[資料表概觀](sql-data-warehouse-tables-overview.md)、[資料表的資料類型](sql-data-warehouse-tables-data-types.md)、[CREATE TABLE](sql-data-warehouse-tables-overview.md)

## <a name="optimize-clustered-columnstore-tables"></a>將叢集資料行存放區資料表最佳化
群集列存儲索引是將資料存儲在 SQL 池中的最有效方法之一。  預設情況下，SQL 池中的表創建為群集列存儲。  為了讓資料行存放區資料表的查詢獲得最佳效能，良好的區段品質很重要。  當資料列在記憶體不足的狀態下寫入資料行存放區資料表時，資料行存放區區段品質可能會降低。  壓縮的資料列群組中的資料列數目可以測量區段品質。  如需偵測和改善叢集資料行存放區資料表區段品質的逐步指示，請參閱[資料表索引](sql-data-warehouse-tables-index.md)一文中的[資料行存放區索引品質不佳的原因](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)。  由於高品質的列存儲段很重要，因此最好使用大中型資源類中的使用者 IT 來載入資料。 使用較低的[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)，表示您想要將更大型的資源類別指派給正在載入的使用者。

由於列存儲表通常不會將資料推送到壓縮列存儲段，直到每個表有 100 多萬行，並且每個 SQL 池表被分區為 60 個表，因此，根據經驗，列存儲表不會使查詢受益，除非表有超過 6000 萬行。  小於 6 千萬列的資料表使用資料行存放區索引似乎不太合理，  但也無傷大雅。  此外，如果您將資料分割，則您要考慮的是每個資料分割必須有 1 百萬個資料列，使用叢集資料行存放區索引才有益。  如果資料表有 100 個分割，則它至少必須擁有 60 億個資料列才會受益於叢集資料行存放區 (60 個散發 * 100 個分割 * 1 百萬個資料列)。  如果在此範例中，您的資料表並沒有 60 億個資料列，請減少資料分割數目，或考慮改用堆積資料表。  使用次要索引搭配堆積資料表而不是資料行存放區資料表，也可能是值得進行的實驗，看看是否可以獲得較佳的效能。

查詢資料行存放區資料表時，如果您只選取您需要的資料行，查詢執行會更快速。  

另請參閱[資料表索引](sql-data-warehouse-tables-index.md)、[資料行存放區索引指南](https://msdn.microsoft.com/library/gg492088.aspx)、[重建資料行存放區索引](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="next-steps"></a>後續步驟
如果在本文中找不到要查找的內容，請嘗試使用此頁面左側的"搜索文檔"來搜索所有 Azure Synapse 分析文檔。  我們也建立了 [Azure SQL 資料倉儲論壇](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)，讓您可以向其他使用者和 SQL 資料倉儲產品群組提出問題。  我們會主動監看這個論壇，以確保您的問題有其他使用者或是我們回答。  如果您比較想在 Stack Overflow上詢問您的問題，我們也有 [Azure SQL 資料倉儲 Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)。

最後，請使用 [Azure SQL 資料倉儲意見反應](https://feedback.azure.com/forums/307516-sql-data-warehouse) 頁面來提出功能要求。  加入您的要求或票選其他要求確實可協助我們決定功能的優先順序。


