---
title: Synapse SQL 開發最佳實作
description: 在為 Synapse SQL 開發時,應瞭解的建議和最佳實踐。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: ed2638cfe4ab7e849e428729ccd17ffdeb6314af
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086346"
---
# <a name="development-best-practices-for-synapse-sql"></a>Synapse SQL 開發最佳實作
本文說明您在開發資料倉儲解決方案時可遵循的指引和最佳做法。 

## <a name="sql-pool-development-best-practices"></a>SQL 池開發最佳實作

### <a name="reduce-cost-with-pause-and-scale"></a>利用暫停和調整來降低成本

如需透過暫停和調整來降低成本的詳細資訊，請參閱[管理計算](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="maintain-statistics"></a>維護統計資料

請務必每天或在每次載入後更新統計資料。  建立和更新統計資料的效能與成本之間總有一些取捨。 如果您發現維護所有統計資訊需要很長時間,請更有選擇地確定哪些列具有統計資訊或哪些列需要頻繁更新。  

例如,您可能希望更新日期列,其中可以每天添加新值。 

> [!NOTE]
> 通過對聯接中涉及的列、WHERE 子句中使用的列以及 GROUP BY 中的列進行統計,您將獲得最大的好處。

另請參考[管理表統計資訊](develop-tables-statistics.md),[建立統計資訊](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest),[更新統計資訊](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="hash-distribute-large-tables"></a>雜湊分散大型資料表

根據預設，資料表是以「循環配置資源」方式分散。  這樣,用戶可以輕鬆地開始創建表,而無需決定如何分配表。  對於某些工作負載,迴圈表可能運行得足夠多。 但是,在大多數情況下,選擇分發列會執行更好的效果。  

依資料行分散資料表的效能遠勝於循環配置資源資料表的最常見例子，是聯結兩個大型事實資料表。  

例如,如果您有一個訂單表(由order_id分發)和一個事務表(也由order_id分發)在order_id上將訂單表加入到交易記錄表時,此查詢將成為傳遞查詢。 

這意味著我們消除了數據移動操作。  較少的步驟代表較快的查詢。  較少的資料移動也會讓查詢更快。

> [!TIP]
> 載入分散的資料表時，請確定您的內送資料的分散式索引鍵沒有排序，因為這會拖慢載入。  

有關選擇分發列如何提高性能以及如何在 CREATE TABLE 語句的「WITH」子句中定義分散式表的其他詳細資訊,請參閱以下連結。

另請參考[表概述](develop-tables-overview.md)、[表分配](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[選擇表分配](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)、[建立表](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)格與[建立表做為 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

### <a name="do-not-over-partition"></a>不要過度執行資料分割
雖然分區數據可以通過分區切換或通過消除分區來優化掃描來有效地維護數據,但分區過多可能會減慢查詢速度。  通常,在 SQL Server 上可能很好地工作的高粒度分區策略在 SQL 池中可能不起作用。  

> [!NOTE]
> 通常,在 SQL Server 上可能很好地工作的高粒度分區策略在 SQL 池中可能不起作用。  

如果每個資料分割的資料列少於 1 百萬，太多個資料分割也會減少叢集資料行存放區索引的效率。 SQL 池將您的數據分區到 60 個資料庫中。 

因此,如果創建具有 100 個分區的表,則結果為 6000 個分區。  每個工作負載都不同，因此最佳建議是嘗試不同的分割，找出最適合您工作負載的分割。  

需要考慮的一個選項是使用小於 SQL Server 中可能適用於您粒度的粒度。  例如，考慮使用每週或每月資料分割，而不是每日資料分割。

另請參閱[表分區](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="minimize-transaction-sizes"></a>將交易大小最小化

在交易中執行的 INSERT、UPDATE、DELETE 陳述式，失敗時必須回復。  為了將長時間回復的可能性降到最低，請盡可能將交易大小最小化。  這可以透過將 INSERT、UPDATE、DELETE 陳述式分成小部分來達成。  

例如,如果您有一個"插入",預計需要 1 小時,則可以將 INSERT 分成四個部分,從而將每次運行縮短為 15 分鐘。

> [!TIP]
> 利用特殊的最低限度記錄案例，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空資料表，來降低回復的風險。  

另一個消除回復的作法是使用「僅中繼資料」作業 (像是資料分割切換) 進行資料管理。  

例如，不要執行 DELETE 陳述式來刪除資料表中所有 order_date 為 2001 年 10 月的資料列，而是將資料每月分割後，再從另一個資料表將有空分割之資料的分割調動出來 (請參閱 ALTER TABLE 範例)。  

針對未分割的資料表，請考慮使用 CTAS 將您想要保留的資料寫入資料表中，而不是使用 DELETE。  如果 CTAS 需要的時間一樣長，則較安全的作業，是在它具有極小交易記錄的條件下執行它，且必要時可以快速地取消。

另請參閱[瞭解事務](develop-transactions.md)、[優化事務](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[表分區](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)[、TRUNCATE、ALTER](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)[TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)和[創建表作為選擇 (CTAS)。](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="use-the-smallest-possible-column-size"></a>使用最小的可能資料行大小

在定義 DDL 時，使用可支援您的資料的最小資料類型，將能夠改善查詢效能。  這對 CHAR 和 VARCHAR 資料行尤其重要。  

如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。  避免將所有字元資料行定義為較大的預設長度。  此外，將資料行定義為 VARCHAR (當它只需要這樣的大小時) 而非 NVARCHAR。

另請參考[表概述](develop-tables-overview.md)、[表格類型](develop-tables-data-types.md)與[建立表](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)格 。

### <a name="optimize-clustered-columnstore-tables"></a>將叢集資料行存放區資料表最佳化

群集列存儲索引是將數據存儲在 SQL 池中的最有效方法之一。  預設情況下,SQL 池中的表創建為群集列存儲。  

為了讓資料行存放區資料表的查詢獲得最佳效能，良好的區段品質很重要。  當資料列在記憶體不足的狀態下寫入資料行存放區資料表時，資料行存放區區段品質可能會降低。  

壓縮的資料列群組中的資料列數目可以測量區段品質。  有關檢測和改進群集列存儲表的段品質的分步說明,請參閱[列存儲索引品質差的原因](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)和[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。  

由於高品質的資料行存放區區段很重要，因此最好使用中型或大型資源類別中的使用者識別碼來載入資料。 使用較低的[資料倉儲單位](resource-consumption-models.md)，表示您想要將更大型的資源類別指派給正在載入的使用者。

由於列儲存表通常不會將數據推送到壓縮列存儲段,直到每個表超過 100 萬行,並且每個 SQL 池表被分區為 60 個表,因此,除非表具有 6000 多列行,否則列存儲表不會使查詢受益。  

> [!TIP]
> 對於行數少於 6000 萬行的表,具有 columstore 索引可能不是最佳解決方案。  

此外，如果您將資料分割，則您要考慮的是每個資料分割必須有 1 百萬個資料列，使用叢集資料行存放區索引才有益。  如果表具有 100 個分區,則它需要至少 60 億行才能從群集列存儲中獲益(60 個分佈*100 個分區 100*個分區 100 萬行)。  

如果表沒有 60 億行,請減少分區數或考慮改用堆表。  可能還值得嘗試,看看通過使用具有輔助索引的堆表而不是列存儲表是否可以獲得更好的性能。

查詢資料行存放區資料表時，如果您只選取您需要的資料行，查詢執行會更快速。  

另請參考[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)、[欄位索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest),[重建列儲存索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality)。

## <a name="sql-on-demand-development-best-practices"></a>SQL 需要開發最佳實作

### <a name="general-considerations"></a>一般考量

SQL 按需允許您查詢 Azure 儲存帳戶中的檔案。 它沒有本地存儲或引入功能,這意味著查詢目標的所有檔都位於 SQL 按需外部。 因此,與從存儲讀取檔相關的一切都可能會影響查詢性能。

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>依需為 Azure 儲存帳戶和 SQL 提供共同定位

為了最大程度地減少延遲,請共同定位 Azure 儲存帳戶和 SQL 按需終結點。 工作區創建期間預配的存儲帳戶和終結點位於同一區域。

為了獲得最佳性能,如果使用 SQL 按需存取其他儲存帳戶,請確保它們位於同一區域。 否則,數據從遠端區域傳輸到終結點區域的網路傳輸的延遲將增加。

### <a name="azure-storage-throttling"></a>Azure 儲存限制

多個應用程式和服務可以存取您的儲存帳戶。 當應用程式、服務和 SQL 按需工作負載生成的合併 IOPS 或輸送量超過存儲帳戶的限制時,就會發生存儲限制。 當發生存儲限制時,會對查詢性能產生重大的負面影響。

檢測到限制后,SQL 按需已內建處理此方案。 SQL 按需將以較慢的速度發出存儲請求,直到解決限制問題。 

但是,為了獲得最佳的查詢執行,建議您在查詢執行期間不要使用其他工作負載來強調存儲帳戶。

### <a name="prepare-files-for-querying"></a>準備用於查詢的檔案

如果可能,您可以準備檔案以取得最佳效能:

- 將 CSV 轉換為鑲木地板 – 鑲木地板是柱形格式。 由於它是壓縮的,它比具有相同數據的 CSV 檔的檔大小小,並且 SQL 按需讀取它所需的時間和存儲請求更少。
- 如果查詢以單個大型檔為目標,則將其拆分為多個較小的檔將受益匪淺。
- 請嘗試將 CSV 檔案大小保持在 10GB 以下。
- 最好為單個 OPENROWSET 路徑或外部表位置具有大小相等的檔。
- 以將分割區儲存到不同的資料夾或檔案名稱來劃分資料 - 檢查[使用檔案名稱與檔案路徑函數來定位特定分割區](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)。

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用檔案資訊與檔案路徑函數定位特定分割區

數據通常以分區方式組織。 您可以指示 SQL 按需查詢特定資料夾和檔。 這將減少查詢需要讀取和處理的文件數量和數據量。 

因此,您將獲得更好的性能。 有關詳細資訊,請查看[檔名](develop-storage-files-overview.md#filename-function)和[檔路徑](develop-storage-files-overview.md#filepath-function)函數以及如何[查詢特定檔](query-specific-files.md)的範例。

如果存儲中的數據未分區,請考慮對其進行分區,以便可以使用這些函數優化針對這些檔的查詢。

當從 SQL 按需[查詢分區 Spark 表](develop-storage-files-spark-tables.md)時,查詢將自動定位為僅需要的檔。

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 提高查詢效能和聯結

[CETAS](develop-tables-cetas.md)是 SQL 按需提供的最重要功能之一。 CETAS 是一個並行操作,用於創建外部表元數據並將 SELECT 查詢的結果匯出到存儲帳戶中的一組檔。

您可以使用 CETAS 將常用的查詢(如聯接的引用表)儲存到一組新的檔案。 稍後,您可以加入此外部表,而不是在多個查詢中重複公共聯接。 

當 CETAS 生成 Parquet 檔時,當第一個查詢針對此外部表時,將自動創建統計資訊,您將獲得更好的性能。

### <a name="next-steps"></a>後續步驟

如果您需要本文中未提供的資訊,請使用此頁面左側的「搜尋文檔」來搜索所有 SQL 池文檔。  [SQL 池論壇](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse)是向其他使用者和 SQL 池產品組提出問題的地方。  

我們會主動監看這個論壇，以確保您的問題有其他使用者或是我們回答。  如果您喜歡在堆疊溢出上提問,我們也有一個 Azure [SQL 池堆疊溢出論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)。
 