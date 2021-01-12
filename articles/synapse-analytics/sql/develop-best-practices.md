---
title: Synapse SQL 的開發最佳做法
description: 開發 Synapse SQL 時應該知道的建議和最佳做法。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1fd7649cac6b636873ca529fe9780429d86697c6
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120898"
---
# <a name="development-best-practices-for-synapse-sql"></a>Synapse SQL 的開發最佳做法

本文說明您在開發資料倉儲解決方案時可遵循的指引和最佳做法。 

## <a name="dedicated-sql-pool-development-best-practices"></a>專用的 SQL 集區開發最佳做法

### <a name="reduce-cost-with-pause-and-scale"></a>利用暫停和調整來降低成本

如需透過暫停和調整來降低成本的詳細資訊，請參閱[管理計算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="maintain-statistics"></a>維護統計資料

請確定您每天或每次載入之後更新統計資料。  建立和更新統計資料的效能與成本之間總有一些取捨。 如果您發現維護所有統計資料所花費的時間太長，請更謹慎選擇哪些資料行具有統計資料，或哪些資料行需要頻繁更新。  

例如，您可能會想要更新日期資料行，其中每天可能會加入新值。 

> [!NOTE]
> 對牽涉聯結的資料行、WHERE 子句中使用的資料行、在 GROUP BY 中找到的資料行加以統計資料，可以獲得最大效益。

另請參閱[管理資料表的統計資料](develop-tables-statistics.md)、[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)、[UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

### <a name="hash-distribute-large-tables"></a>雜湊分散大型資料表

根據預設，資料表是以「循環配置資源」方式分散。 這項功能可讓使用者輕鬆地開始建立資料表，而不需要決定其資料表的散發方式。  循環配置資源資料表可能會對某些工作負載執行足夠。 但是在大部分的情況下，選取散發資料行的執行效果會更好。  

依資料行分散資料表的效能遠勝於循環配置資源資料表的最常見例子，是聯結兩個大型事實資料表。  

例如，如果您有 order_id 的「訂單」資料表，以及「交易」資料表（也由 order_id 散發），則當您在 order_id 上將 orders 資料表聯結至「交易」資料表時，此查詢會變成傳遞查詢。 

這表示我們消除資料移動作業。  較少的步驟代表較快的查詢。  較少的資料移動也會讓查詢更快。

> [!TIP]
> 載入分散的資料表時，請確定您的內送資料的分散式索引鍵沒有排序，因為這會拖慢載入。  

關於選取分散資料行能如何提升效能，以及如何在 CREATE TABLE 陳述式的 WITH 子句中定義分散的資料表，如需詳細資料，請參閱以下的連結。

另請參閱[資料表概觀](develop-tables-overview.md)、[資料表散發](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[選取資料表散發](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)、[CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 和 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

### <a name="do-not-over-partition"></a>不要過度執行資料分割
雖然分割資料可以讓資料維護變得有效率 (透過分割切換或最佳化掃描將分割消除)，但太多的資料分割會讓查詢變慢。  很高的資料細微性資料分割策略，在 SQL Server 可能無法在專用的 SQL 集區上運作。  

> [!NOTE]
> 很高的資料細微性資料分割策略，在 SQL Server 可能無法在專用的 SQL 集區上運作。  

如果每個資料分割的資料列少於 1 百萬，太多個資料分割也會減少叢集資料行存放區索引的效率。 專用的 SQL 集區會將您的資料分割成60資料庫。 

因此，如果您建立具有 100 個分割區的資料表，則結果會是 6000 個分割區。  每個工作負載都不同，因此最佳建議是嘗試不同的分割，找出最適合您工作負載的分割。  

其中一個可考慮的選項是使用比您 SQL Server 上運作良好的資料粒度更低的粒度。  例如，考慮使用每週或每月資料分割，而不是每日資料分割。

另請參閱[資料表分割](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="minimize-transaction-sizes"></a>將交易大小最小化

在交易中執行的 INSERT、UPDATE、DELETE 陳述式，失敗時必須回復。  為了將長時間回復的可能性降到最低，請盡可能將交易大小最小化。  這可以透過將 INSERT、UPDATE、DELETE 陳述式分成小部分來達成。  

例如，如果您預期您的 INSERT 需要 1 小時，您可以將 INSERT 分成 4 個部分，藉此將每次執行縮短為 15 分鐘。

> [!TIP]
> 利用特殊的最低限度記錄案例，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空資料表，來降低回復的風險。  

另一個消除回復的作法是使用「僅中繼資料」作業 (像是資料分割切換) 進行資料管理。  

例如，不要執行 DELETE 陳述式來刪除資料表中所有 order_date 為 2001 年 10 月的資料列，而是將資料每月分割後，再從另一個資料表將有空分割之資料的分割調動出來 (請參閱 ALTER TABLE 範例)。  

針對未分割的資料表，請考慮使用 CTAS 將您想要保留的資料寫入資料表中，而不是使用 DELETE。  如果 CTAS 需要的時間一樣長，則較安全的作業，是在它具有最小交易記錄的條件下執行它，且必要時可以快速地取消。

另請參閱[了解交易](develop-transactions.md)、[最佳化交易](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[資料表分割](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 和 [Create Table As Select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="use-the-smallest-possible-column-size"></a>使用最小的可能資料行大小

在定義 DDL 時，使用可支援您的資料的最小資料類型，將能夠改善查詢效能。 對於 CHAR 和 VARCHAR 資料行而言，此動作特別重要。  

如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。  避免將所有字元資料行定義為較大的預設長度。  此外，將資料行定義為 VARCHAR （如果需要的話），而不是使用 NVARCHAR。

另請參閱[資料表概觀](develop-tables-overview.md)、[資料表的資料類型](develop-tables-data-types.md)和 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

### <a name="optimize-clustered-columnstore-tables"></a>將叢集資料行存放區資料表最佳化

叢集資料行存放區索引是將資料儲存在專用 SQL 集區中最有效率的方式之一。  依預設，專用 SQL 集區中的資料表會建立為叢集資料行存放區。  

為了讓資料行存放區資料表的查詢獲得最佳效能，良好的區段品質很重要。  當資料列在記憶體不足的狀態下寫入資料行存放區資料表時，資料行存放區區段品質可能會降低。  

壓縮的資料列群組中的資料列數目可以測量區段品質。  如需偵測和改善叢集資料行存放區資料表之區段品質的逐步指示，請參閱資料行存放區 [索引品質不佳的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) 和 [資料表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 文章。  

由於高品質的資料行存放區區段很重要，因此最好使用中型或大型資源類別中的使用者識別碼來載入資料。 使用較低的[資料倉儲單位](resource-consumption-models.md)，表示您想要將更大型的資源類別指派給正在載入的使用者。

由於資料行存放區資料表通常不會將資料推送至壓縮的資料行存放區區段，直到每個資料表有1000000個以上的資料列，而且每個專用的 SQL 集區資料表都分割成60資料表時，資料行存放區資料表將無法受益于查詢，除非資料表60000000有  

> [!TIP]
> 對於包含低於 6 千萬個資料列的資料表，具有資料行存放區索引可能不是最佳的解決方案。  

此外，如果您將資料分割，則您會想要考慮每個分割區都必須有1000000個數據列，才能受益于叢集資料行存放區索引。  如果資料表有 100 個分割區，則其至少必須擁有 60 億個資料列才會受益於叢集資料行存放區 (60 個散發 100 個分割區 100 萬個資料列)。  

如果您的資料表沒有 60 億個資料列，請減少分割區數目，或考慮改用堆積資料表。  使用次要索引搭配堆積資料表而非資料行存放區資料表，也可能是值得進行的實驗，看看是否可以獲得較佳的效能。

查詢資料行存放區資料表時，如果您只選取您需要的資料行，查詢執行會更快速。  

另請參閱[資料表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[資料行存放區索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)、[重建資料行存放區索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality)。

## <a name="serverless-sql-pool-development-best-practices"></a>無伺服器 SQL 集區開發最佳做法

### <a name="general-considerations"></a>一般考量

無伺服器 SQL 集區可讓您查詢 Azure 儲存體帳戶中的檔案。 它沒有本機儲存體或內嵌功能，這表示查詢目標的所有檔案都是無伺服器 SQL 集區的外部。 因此，與從儲存體讀取檔案相關的所有專案，都可能會影響查詢效能。

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>共置 Azure 儲存體帳戶和無伺服器 SQL 集區

若要將延遲降至最低，請共置您的 Azure 儲存體帳戶和無伺服器的 SQL 集區端點。 在工作區建立期間佈建的儲存體帳戶和端點都會位於相同區域。

為了達到最佳效能，如果您存取無伺服器 SQL 集區的其他儲存體帳戶，請確定它們位於相同的區域中。 否則，從遠端區域到端點區域的資料網路傳輸延遲將會增加。

### <a name="azure-storage-throttling"></a>Azure 儲存體節流

存取您儲存體帳戶的應用程式和服務可能會有很多個。 當應用程式、服務和無伺服器的 SQL 集區工作負載所產生的結合 IOPS 或輸送量超過儲存體帳戶的限制時，就會進行儲存體節流。 發生儲存節流時，對查詢效能會有顯著的負面影響。

一旦偵測到節流，無伺服器 SQL 集區內建此案例的處理。 無伺服器 SQL 集區會以較慢的步調提出要求，直到節流解決為止。 

不過，為了達到最佳的查詢執行，建議您不要在查詢執行期間，將儲存體帳戶與其他工作負載一起負擔。

### <a name="prepare-files-for-querying"></a>準備檔案以進行查詢

可能的話，您可以準備檔案以獲得更好的效能：

- 將 CSV 轉換成 Parquet - Parquet 是單欄式格式。 由於它已壓縮，因此它的檔案大小會比具有相同資料的 CSV 檔案小，而且無伺服器 SQL 集區需要較少的時間和儲存體要求來讀取它。
- 如果查詢是以單一大型檔案為目標，則好處是可將其分割成多個較小的檔案。
- 請盡可能將 CSV 檔案大小維持在小於 10 GB。
- 單一 OPENROWSET 路徑或外部資料表 LOCATION 的檔案大小最好相同。
- 若要藉由將資料分割儲存至不同資料夾或不同檔案名稱，來分割您的資料，請參閱[使用 filename 和 filepath 函式將特定資料分割設為目標](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)。

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用 fileinfo 和 filepath 函式以特定的分割區為目標

資料通常會組織成分割區。 您可以指示無伺服器 SQL 集區來查詢特定的資料夾和檔案。 這麼做會減少查詢需要讀取和處理的檔案數目和資料量。 

因此，您將可獲得更佳的效能。 如需詳細資訊，請參閱 [filename](query-data-storage.md#filename-function) 和 [filepath](query-data-storage.md#filepath-function) 函式和範例，以了解如何[查詢特定檔案](query-specific-files.md)。

如果您在儲存體中的資料未分割，請考慮將其分割，以便使用這些函式來最佳化以這些檔案為目標的查詢。

從無伺服器的 SQL 集區 [查詢 Azure Synapse 外部資料表的資料分割 Apache Spark](develop-storage-files-spark-tables.md) 時，查詢只會自動以所需的檔案為目標。

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 來增強查詢效能和聯結

[CETAS](develop-tables-cetas.md) 是無伺服器 SQL 集區中最重要的其中一項功能。 CETAS 是平行作業，可建立外部資料表中繼資料，並將 SELECT 查詢的結果匯出至儲存體帳戶中的一組檔案。

您可以使用 CETAS，將經常使用的查詢部分 (例如聯結的參考資料表) 儲存到新的一組檔案。 稍後，您可以聯結至這個單一外部資料表，而不是在多個查詢中重複常見的聯結。 

當 CETAS 產生 Parquet 檔案時，會在第一個查詢以這個外部資料表為目標時自動建立統計資料，而您將能獲得更佳的效能。

### <a name="next-steps"></a>後續步驟

如果您需要本文中未提供的資訊，請使用此頁面左側的 **搜尋 doc** 函式來搜尋所有的 SQL 集區檔。  您可以 [針對 Azure Synapse Analytics 的 Microsoft 問&問題頁面](/answers/topics/azure-synapse-analytics.html) ，為其他使用者和 Azure Synapse Analytics 產品群組提出問題。 我們會主動監看這個論壇，以確保您的問題有其他使用者或是我們回答。  

如果您想要在 Stack Overflow 上詢問您的問題，我們也有 [Azure Synapse Analytics Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)。
