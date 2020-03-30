---
title: Azure SQL 資料庫中的查詢性能問題的類型
description: 在本文中，瞭解 Azure SQL 資料庫中的查詢性能問題類型，並瞭解如何識別和解決與這些問題的查詢
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: e155321c2727bf4ee871ef7be7b61b6a523ec1fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256129"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Azure SQL 資料庫中可檢測到的查詢性能瓶頸類型

嘗試解決性能瓶頸時，首先確定查詢處於運行狀態或等候狀態時是否發生瓶頸。 根據此確定，適用不同的解析度。 使用下圖可説明瞭解可能導致運行相關問題或等待相關問題的因素。 本文討論了與每種類型的問題相關的問題和解決方法。

您可以使用 Azure SQL 資料庫[智慧見解](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns)或 SQL Server [DMV](sql-database-monitoring-with-dmvs.md)來檢測這些類型的性能瓶頸。

![工作負載狀態](./media/sql-database-monitor-tune-overview/workload-states.png)

**與運行相關的問題**：與運行相關的問題通常與編譯問題相關，從而導致資源不足或過度使用導致查詢計劃或執行問題。
**與輪候有關的問題**：與輪候有關的問題一般與：

- 鎖定 (封鎖)
- I/O
- 與 TempDB 使用相關的爭用
- 記憶體授與等候

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>導致查詢計劃不理想的編譯問題

SQL 查詢最佳化工具生成的次優計畫可能是查詢性能降低的原因。 SQL 查詢最佳化工具可能會生成次優計畫，因為缺少索引、陳舊的統計資訊、要處理的行數估計不正確或所需記憶體估計不准確。 如果您知道查詢在過去或另一個實例（託管實例或 SQL Server 實例）上執行得更快，請比較實際執行計畫以查看它們是否不同。

- 使用以下方法之一標識任何缺少的索引：

  - 使用[智慧見解](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index)。
  - 用於單個和池資料庫的[資料庫顧問](sql-database-advisor.md)。
  - DMV. 此示例顯示缺少索引的影響、如何使用 DMV 檢測[缺少的索引](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes)以及實現缺少的索引建議的影響。
- 嘗試應用[查詢提示](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)、[更新統計資料](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)或[重建索引](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)以獲得更好的計畫。 在 Azure SQL 資料庫中啟用[自動計畫更正](sql-database-automatic-tuning.md)，以自動緩解這些問題。

  [此示例](sql-database-performance-guidance.md#query-tuning-and-hinting)演示了由於參數化查詢而導致的次優查詢計劃的影響、如何檢測此條件以及如何使用查詢提示來解決。

- 嘗試更改資料庫相容性級別並實現智慧查詢處理。 SQL 查詢最佳化工具可能會生成不同的查詢計劃，具體取決於資料庫的相容性級別。 更高的相容性級別提供了更[智慧的查詢處理功能](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)。

  - 有關查詢處理的詳細資訊，請參閱[查詢處理體系結構指南](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide)。
  - 要更改資料庫相容性級別並閱讀有關相容性級別之間的差異的詳細資訊，請參閱[ALTER 資料庫](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。
  - 要瞭解有關基數估計的更多內容，請參閱[基數估計](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>使用不理想的查詢執行計畫解析查詢

以下各節討論如何使用不理想的查詢執行計畫解決查詢。

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>具有參數敏感計畫 （PSP） 問題的查詢

當查詢最佳化工具生成僅針對特定參數值（或一組值集）最優的查詢執行計畫時，就會發生參數敏感計畫 （PSP） 問題，然後緩存的計畫對於連續使用的參數值不理想執行。 然後，不理想的計畫可能會導致查詢性能問題並降低總體工作負載輸送量。

有關參數嗅探和查詢處理的詳細資訊，請參閱[查詢處理體系結構指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

幾種解決方法可以緩解 PSP 問題。 每個解決方法都有相關的權衡和缺點：

- 在每次查詢執行時使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示。 此因應措施會交易編譯時間和增加的 CPU，以獲得更好的計劃品質。 對於`RECOMPILE`需要高輸送量的工作負載，通常無法使用該選項。
- 使用[OPTION（OPTIMIZE FOR...）](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示來覆蓋實際參數值，該參數值具有生成足以滿足大多數參數值可能性的計畫。 此選項需要充分了解最佳的參數值和相關聯的計畫特性。
- 使用[選項（未知優化）](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示覆蓋實際參數值，而是使用密度向量平均值。 還可以通過在區域變數中捕獲傳入參數值，然後在謂詞中使用區域變數而不是使用參數本身來執行此操作。 對於此修復，平均密度必須*足夠好*。
- 使用[DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示完全禁用參數嗅探。
- 使用[KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示防止緩存中重新編譯。 此解決方法假定足夠好的常見計畫已經是緩存中的一個。 您還可以禁用自動統計資訊更新，以減少將刪除好計畫並編譯新的壞計畫的可能性。
- 通過重寫查詢並在查詢文本中添加提示，顯式使用[USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示強制計畫。 或者通過使用查詢存儲或啟用[自動調優](sql-database-automatic-tuning.md)來設置特定計劃。
- 使用程序的的巢狀集合取代單一程序，每個程序都可以根據條件式邏輯和相關聯的參數值來使用。
- 建立靜態程序定義的動態字串執行替代方案。

有關解決 PSP 問題的詳細資訊，請參閱以下博客文章：

- [我聞到一個參數](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. 動態 SQL 與過程與參數化查詢的計畫品質](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL Server 中的 SQL 查詢優化技術：參數嗅探](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>由不當參數化引起的編譯活動

當查詢具有文本時，資料庫引擎會自動對語句進行參數化，或者使用者顯式參數化語句以減少編譯次數。 使用相同的模式對查詢進行大量編譯，但不同的文本值可能會導致 CPU 使用率高。 同樣，如果僅對繼續具有文本的查詢進行部分參數化，則資料庫引擎不會進一步參數化查詢。

下面是部分參數化查詢的示例：

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

在此示例中，`t1.c1`採用`@p1`，但`t2.c2`繼續將 GUID 視為文本。 在這種情況下，如果更改 的值`c2`，則查詢將被視為不同的查詢，並且將發生新的編譯。 為了減少本示例中的編譯，您還將對 GUID 進行參數化。

以下查詢顯示查詢雜湊的查詢計數，以確定查詢是否已正確參數化：

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>影響查詢計劃更改的因素

查詢執行計畫重新編譯可能會導致生成的查詢計劃與原始緩存計畫不同。 由於各種原因，現有原始計畫可能會自動重新編譯：

- 查詢引用架構中的更改
- 查詢引用對表的資料更改
- 查詢上下文選項已更改

由於各種原因，編譯計畫可能會從緩存中彈出，例如：

- 實例重新開機
- 資料庫範圍配置更改
- 記憶體壓力
- 清除緩存的顯式請求

如果使用 RECOMPILE 提示，將不會緩存計畫。

重新編譯（或緩存逐出後的新編譯）仍可能導致生成與原始計畫相同的查詢執行計畫。 當計畫與先前計畫或原始計畫更改時，可能會進行以下解釋：

- **更改的實體設計**：例如，新創建的索引更有效地涵蓋查詢的要求。 如果查詢最佳化工具確定使用新索引比使用最初為查詢執行的第一個版本選擇的資料結構更優化，則新索引可能會用於新編譯。 對引用物件的任何物理更改都可能導致在編譯時選擇新的計畫。

- **伺服器資源差異**：當一個系統中的計畫與另一個系統中的計畫不同時，資源可用性（如可用處理器的數量）可能會影響生成的計畫。 例如，如果一個系統有多個處理器，則可能會選擇並行計畫。

- **不同的統計資訊**：與引用的物件關聯的統計資訊可能已更改，或者可能與原始系統的統計資訊有重大區別。 如果統計資訊發生更改併發生重新編譯，則查詢最佳化工具使用從更改時開始的統計資訊。 修訂後的統計資料的資料分佈和頻率可能與原始彙編的資料分佈和頻率不同。 這些更改用於創建基數估計值。 （*基數估計*是預期流經邏輯查詢樹的行數。對基數估計的更改可能會導致您可以選擇不同的物理運算子和相關的操作訂單。 即使對統計資訊稍作更改，也可能導致查詢執行計畫更改。

- **更改了資料庫相容性級別或基數估計版本**：對資料庫相容性級別的更改可以啟用可能導致不同查詢執行計畫的新策略和功能。 除了資料庫相容性級別外，禁用或啟用的跟蹤標誌 4199 或資料庫範圍配置QUERY_OPTIMIZER_HOTFIXES更改的狀態也會影響編譯時查詢執行計畫選擇。 跟蹤標誌 9481（強制遺留 CE）和 2312（強制預設 CE）也會影響計畫。

## <a name="resource-limits-issues"></a>資源限制問題

查詢性能慢與次優查詢計劃無關，缺少索引通常與資源不足或過度使用有關。 如果查詢計劃是最佳的，則查詢（和資料庫）可能達到資料庫、彈性池或託管實例的資源限制。 例如，服務等級的日誌寫入輸送量可能過多。

- 使用 Azure 門戶檢測資源問題：要查看資源限制是否存在問題，請參閱 SQL[資料庫資源監視](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring)。 有關單個資料庫和彈性池，請參閱[資料庫顧問性能建議](sql-database-advisor.md)和[查詢性能見解](sql-database-query-performance.md)。
- 使用[智慧洞察](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)檢測資源限制
- 使用[DMV](sql-database-monitoring-with-dmvs.md)檢測資源問題 ：

  - [系統dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 返回 SQL 資料庫的 CPU、I/O 和記憶體消耗。 每 15 秒間隔存在一行，即使資料庫中沒有活動也是如此。 歷程記錄資料會保留一個小時。
  - [系統resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 返回 Azure SQL 資料庫的 CPU 使用率和存儲資料。 資料以五分鐘間隔收集和聚合。
  - [許多累積耗用大量 CPU 的個別查詢](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

如果將問題確定為資源不足，則可以升級資源以提高 SQL 資料庫的容量以吸收 CPU 需求。 有關詳細資訊，請參閱在[Azure SQL 資料庫中縮放單個資料庫資源](sql-database-single-database-scale.md)，並在[Azure SQL 資料庫中縮放彈性池資源](sql-database-elastic-pool-scale.md)。 有關縮放託管實例的資訊，請參閱[服務層資源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="performance-problems-caused-by-increased-workload-volume"></a>工作量增加引起的性能問題

應用程式流量和工作負載容量的增加可能會導致 CPU 使用率增加。 但您必須小心正確診斷此問題。 當您看到高 CPU 問題時，請回答這些問題以確定增加是否是由工作負載卷的更改引起的：

- 來自應用程式的查詢是高 CPU 問題的原因嗎？
- 對於[可以標識的 CPU 限定率最高的查詢](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)：

  - 多個執行計畫與同一查詢相關聯嗎？ 如果是，為什麼？
  - 對於具有相同執行計畫的查詢，執行時間一致嗎？ 執行計數增加嗎？ 如果是這樣，工作負載的增加可能會導致性能問題。

總之，如果查詢執行計畫執行方式不同，但 CPU 使用率隨執行計數而增加，則性能問題可能與工作負載增加有關。

識別導致 CPU 問題的工作負載卷更改並不總是那麼容易。 請考慮下列因素：

- **更改的資源使用方式**：例如，考慮 CPU 使用率在較長時間內增加到 80% 的情況。 僅 CPU 使用率並不意味著工作負載容量已更改。 查詢執行計畫中的回歸和資料分佈的變化也有助於增加資源使用，即使應用程式執行相同的工作負載也是如此。

- **新查詢的外觀**：應用程式可能在不同時間驅動一組新的查詢。

- **請求數的增加或減少**：此方案是工作負載的最明顯度量。 查詢數目不一定會與更多的資源使用率相對應。 但是，假設其他因素保持不變，此指標仍然是一個重要信號。

使用智慧見解檢測[工作負載增加](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase)並[規劃回歸](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression)。

## <a name="waiting-related-problems"></a>與等待相關的問題

一旦消除了與執行問題相關的計畫次優計畫和*等待相關*問題，性能問題通常是查詢可能正在等待某些資源。 與等待相關的問題可能由：

- **阻止**：

  一個查詢可能保留資料庫中物件的鎖，而其他查詢則嘗試訪問相同的物件。 您可以使用[DMV](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries)或[智慧見解](sql-database-intelligent-insights-troubleshoot-performance.md#locking)來標識阻止查詢。
- **IO 問題**

  查詢可能正在等待將頁面寫入資料或日誌檔。 在這種情況下，請檢查 DMV `INSTANCE_LOG_RATE_GOVERNOR` `WRITE_LOG`中的`PAGEIOLATCH_*`、或等待統計資訊。 請參閱使用 DMV[來識別 IO 性能問題](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)。
- **TempDB 問題**

  如果工作負載使用臨時表，或者計畫中存在 TempDB 洩漏，則查詢的 TempDB 輸送量可能有問題。 請參閱使用 DMV 來[標識 TempDB 問題](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues)。
- **記憶體相關問題**

  如果工作負荷沒有足夠的記憶體，則頁面預期壽命可能會下降，或者查詢的記憶體可能少於所需的記憶體。 在某些情況下，查詢最佳化工具中的內置智慧將修復與記憶體相關的問題。 請參閱使用 DMV[來標識記憶體授予問題](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)。

### <a name="methods-to-show-top-wait-categories"></a>顯示頂部等待類別的方法

這些方法通常用於顯示等待類型的頂級類別：

- 使用智慧見解識別由於[等待時間增加](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)導致性能下降的查詢
- 使用[查詢存儲](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)查找每個查詢隨時間的等待統計資訊。 在查詢存放區中，等候類型會合併到等候類別中。 您可以在[sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)中找到等待類別的映射以等待類型。
- 使用[sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)返回有關在查詢操作期間執行的執行緒遇到的所有等待的資訊。 可以使用此彙總檢視診斷 Azure SQL 資料庫以及特定查詢和批次處理的性能問題。 查詢可以等待資源、佇列等待或外部等待。
- 使用[sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql)返回有關等待某些資源的任務佇列的資訊。

在高 CPU 方案中，查詢存儲和等待統計資訊可能不會反映 CPU 使用率，如果出現以下情況：

- 高 CPU 佔用的查詢仍在執行中。
- 發生容錯移轉時，高 CPU 佔用的查詢正在運行。

跟蹤查詢存儲和等待統計資訊的 DMV 僅顯示成功完成和超時查詢的結果。 在語句完成之前，它們不會顯示當前執行語句的資料。 使用動態管理檢視[sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)跟蹤當前正在執行的查詢和關聯的工作時間。

> [!TIP]
> 其他工具：
>
> - [老虎工具箱等待和閂鎖](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [老虎工具箱usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>後續步驟

[SQL 資料庫監視和調優概述](sql-database-monitor-tune-overview.md)