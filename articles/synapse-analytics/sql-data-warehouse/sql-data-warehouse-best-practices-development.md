---
title: 開發最佳做法安全開發的最佳做法
description: 開發 Synapse SQL 集區的解決方案時應該知道的建議和最佳作法。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f8db19b209ddefebd4a297f18fb9b178fcf40f21
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89457901"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Synapse SQL 集區的開發最佳做法

本文說明您在開發 SQL 集區解決方案時可遵循的指引和最佳做法。

## <a name="tune-query-performance-with-new-product-enhancements"></a>使用新的產品增強功能調整查詢效能

- [使用具體化檢視進行效能微調](performance-tuning-materialized-views.md)
- [透過已排序的叢集資料行存放區索引進行效能微調](performance-tuning-ordered-cci.md)
- [使用結果集快取進行效能微調](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>利用暫停和調整來降低成本

如需透過暫停和調整來降低成本的詳細資訊，請參閱[管理計算](sql-data-warehouse-manage-compute-overview.md)一文。

## <a name="maintain-statistics"></a>維護統計資料

SQL 集區可以設定為自動偵測資料行並建立統計資料。  最佳化工具建立的查詢計劃只能利用可用的統計資料。  

建議您為資料庫啟用 AUTO_CREATE_STATISTICS，並讓統計資料每天或每次載入之後更新，以確保查詢中所用資料行的統計資料一直為最新狀態。

如果您發現更新所有統計資料所需時間太長，可能要更謹慎選擇哪些資料行需要頻繁更新。 例如，您可能想要更新您每天都要加入新值的日期資料行。

> [!TIP]
> 對牽涉聯結的資料行、WHERE 子句中使用的資料行、在 GROUP BY 中找到的資料行加以更新統計資料，可以獲得最大效益。

另請參閱[管理資料表的統計資料](sql-data-warehouse-tables-statistics.md)、[CREATE STATISTICS](sql-data-warehouse-tables-statistics.md) 和 [UPDATE STATISTICS](sql-data-warehouse-tables-statistics.md#update-statistics)。

## <a name="hash-distribute-large-tables"></a>雜湊分散大型資料表

根據預設，資料表是以「循環配置資源」方式分散。  這項設計可讓使用者更容易開始建立資料表，而不必決定應該如何分散其資料表。  

循環配置資源的資料表在某些工作負載中執行良好，但某些狀況下選取分散資料行的執行效能會更好。  依資料行分散資料表的效能遠勝於循環配置資源資料表的最常見例子，是聯結兩個大型事實資料表。  

例如，如果您有一個依 order_id 分散的訂單資料表，以及一個也是依 order_id 分散的交易資料表，當您將訂單資料聯結至交易資料表上的 order_id，此查詢會變成傳遞查詢，也就是資料移動作業會被消除。  較少的步驟代表較快的查詢。  較少的資料移動也會讓查詢更快。  

載入分散的資料表時，請確定您的內送資料的分散式索引鍵沒有排序，因為這會拖慢載入。  下文進一步詳述選取分散資料行如何能提升效能，以及如何在 CREATE TABLE 陳述式的 WITH 子句中定義分散的資料表。

另請參閱[資料表概觀](sql-data-warehouse-tables-overview.md)、[資料表散發](sql-data-warehouse-tables-distribute.md)、[選取資料表散發](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、[CREATE TABLE](sql-data-warehouse-tables-overview.md) 和 [CREATE TABLE AS SELECT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>不要過度執行資料分割

雖然資料分割可以讓資料維護變得有效率 (透過分割切換或最佳化掃描將分割消除)，太多的資料分割會讓查詢變慢。  

通常在 SQL Server 上運作良好的高資料粒度分割策略，可能無法在 SQL 集區上運作良好。  如果每個資料分割的資料列少於 1 百萬，太多個資料分割也會減少叢集資料行存放區索引的效率。  

請記住，SQL 集區在幕後為您將資料分割成 60 的資料庫，因此如果您建立有 100 個分割的資料表，實際上會導致 6000 個分割。  每個工作負載都不同，因此最佳建議是嘗試不同的分割，找出最適合您工作負載的分割。  

> [!TIP]
> 請考慮使用比您的 SQL Server 上運作良好的資料粒度更低的粒度。  例如，考慮使用每週或每月資料分割，而不是每日資料分割。

另請參閱[資料表分割](sql-data-warehouse-tables-partition.md)。

## <a name="minimize-transaction-sizes"></a>將交易大小最小化

在交易中執行的 INSERT、UPDATE、DELETE 陳述式，失敗時必須回復。  為了將長時間回復的可能性降到最低，請盡可能將交易大小最小化。  這可以透過將 INSERT、UPDATE、DELETE 陳述式分成小部分來達成。  

例如，如果您預期您的 INSERT 需要 1 小時，可能的話，將 INSERT 分成 4 個部分，每個執行 15 分鐘。  利用特殊的最低限度記錄案例，例如 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空資料表，來降低回復的風險。  

另一個消除回復的作法是使用「僅中繼資料」作業 (像是資料分割切換) 進行資料管理。  例如，不要執行 DELETE 陳述式來刪除資料表中所有 order_date 為 2001 年 10 月的資料列，而是將資料每月分割後，再從另一個資料表將有空分割之資料的分割調動出來 (請參閱 ALTER TABLE 範例)。  

針對未分割的資料表，請考慮使用 CTAS 將您想要保留的資料寫入資料表中，而不是使用 DELETE。  如果 CTAS 需要的時間一樣長，則較安全的作業，是在它具有最小交易記錄的條件下執行它，且必要時可以快速地取消。

另請參閱[瞭解交易](sql-data-warehouse-develop-transactions.md)、[最佳化交易](sql-data-warehouse-develop-best-practices-transactions.md)、[資料表分](sql-data-warehouse-tables-partition.md)割、[TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和 [Create table as select (CTAS)](sql-data-warehouse-develop-ctas.md)。

## <a name="use-the-smallest-possible-column-size"></a>使用最小的可能資料行大小

在定義 DDL 時，使用可支援您的資料的最小資料類型，將能夠改善查詢效能。  這種方法對 CHAR 和 VARCHAR 資料行尤其重要。  

如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。  避免將所有字元資料行定義為較大的預設長度。  此外，將資料行定義為 VARCHAR (當它只需要這樣的大小時) 而非 NVARCHAR。

另請參閱[資料表概觀](sql-data-warehouse-tables-overview.md)、[資料表的資料類型](sql-data-warehouse-tables-data-types.md)和 [CREATE TABLE](sql-data-warehouse-tables-overview.md)。

## <a name="optimize-clustered-columnstore-tables"></a>將叢集資料行存放區資料表最佳化

叢集資料行存放區索引是將資料儲存在 SQL 集區中最有效率的方式之一。  根據預設，SQL 集區中的資料表會建立為「叢集資料行存放區」。  

> [!NOTE]
> 為了讓資料行存放區資料表的查詢達到最佳效能，良好的區段品質很重要。  

當資料列在記憶體不足的狀態下寫入資料行存放區資料表時，資料行存放區區段品質可能會降低。  壓縮的資料列群組中的資料列數目可以測量區段品質。  

如需偵測和改善叢集資料行存放區資料表區段品質的逐步指示，請參閱[資料表索引](sql-data-warehouse-tables-index.md)一文中的[資料行存放區索引品質不佳的原因](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)。  

由於高品質的資料行存放區區段很重要，因此最好使用中型或大型資源類別中的使用者識別碼來載入資料。 使用較低的[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)，表示您想要將更大型的資源類別指派給正在載入的使用者。

由於資料行存放區資料表通常要等到每個資料表有超過 1 百萬個資料列之後才會將資料推送到壓縮的資料行存放區區段，而且每個 SQL 集區資料表分割成 60 個資料表，一般而言，資料行存放區資料表對於查詢沒有好處，除非資料表有超過 6 千萬個資料列。  

小於 6 千萬列的資料表使用資料行存放區索引似乎不太合理，  但也無傷大雅。  

此外，如果您將資料分割，則您要考慮的是每個資料分割必須有 1 百萬個資料列，使用叢集資料行存放區索引才有益。  如果資料表有 100 個分割，則它至少必須擁有 60 億個資料列才會受益於叢集資料行存放區 (60 個散發 *100 個分割* 1 百萬個資料列)。  

如果在此範例中，您的資料表並沒有 60 億個資料列，請減少資料分割數目，或考慮改用堆積資料表。  使用次要索引搭配堆積資料表而不是資料行存放區資料表，也可能是值得進行的實驗，看看是否可以獲得較佳的效能。

> [!TIP]
> 查詢資料行存放區資料表時，如果您只選取您需要的資料行，查詢執行會更快速。  

另請參閱[資料表索引](sql-data-warehouse-tables-index.md)、[資料行存放區索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)和[重建資料行存放區索引](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)。

## <a name="next-steps"></a>後續步驟

如果您在此文件中找不到想要尋找的內容，請嘗試使用此頁面左邊的 [搜尋文件] 來搜尋所有 Azure Synapse Analytics 文件。  

[Microsoft 問與答問題](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)頁面，可讓您將問題張貼至其他使用者和 Azure Synapse Analytics 產品群組。  我們會主動監看這個論壇，以確保您的問題有其他使用者或是我們回答。  

如果您想要在 Stack Overflow 上詢問您的問題，我們也有 [Azure Synapse Analytics Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)。

使用 [Azure Synapse Analytics 意見反應](https://feedback.azure.com/forums/307516-sql-data-warehouse)頁面來提出功能要求。  加入您的要求或票選其他要求確實可協助我們決定功能的優先順序。
