---
title: Azure SQL Database 中的查詢效能問題類型
description: 在本文中，瞭解 Azure SQL Database 中的查詢效能問題類型，並瞭解如何使用這些問題來識別及解決查詢
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256129"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Azure SQL Database 中可偵測的查詢效能瓶頸類型

當嘗試解決效能瓶頸時，請從判斷在查詢處於執行中狀態或等候狀態時，是否發生瓶頸。 不同的解決方式適用于這項決定。 請使用下列圖表來協助您瞭解可能導致執行相關問題或等待相關問題的因素。 這篇文章會討論與每個問題類型相關的問題和解決方式。

您可以使用 Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns)或 SQL Server [dmv](sql-database-monitoring-with-dmvs.md)來偵測這些類型的效能瓶頸。

![工作負載狀態](./media/sql-database-monitor-tune-overview/workload-states.png)

執行**相關的問題**：與執行相關的問題通常與編譯問題有關，因而導致不佳的查詢計劃或與過度使用資源相關的執行問題。
**等待相關問題**：與等待相關的問題通常與下列相關：

- 鎖定 (封鎖)
- I/O
- TempDB 使用量的相關爭用
- 記憶體授與等候

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>編譯問題導致不佳的查詢計劃

SQL 查詢最佳化工具所產生的次佳計畫可能是查詢效能變慢的原因。 SQL 查詢最佳化工具可能會產生不佳的計畫，因為遺漏索引、過時統計資料、要處理的資料列數目不正確，或所需的記憶體估計不精確。 如果您知道查詢是在過去或另一個實例（受控實例或 SQL Server 實例）上更快執行，請比較實際的執行計畫，以查看它們是否不同。

- 使用下列其中一種方法來識別任何遺漏的索引：

  - 使用[Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index)。
  - 適用于單一和集區資料庫的[Database Advisor](sql-database-advisor.md) 。
  - Dmv. 這個範例會顯示遺漏索引的影響、如何使用 Dmv 偵測遺漏的[索引](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes)，以及執行遺漏索引建議的影響。
- 請嘗試套用[查詢提示](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)、[更新統計資料](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)或[重建索引](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)，以取得更好的計畫。 請在 Azure SQL Database 中啟用[自動計畫更正](sql-database-automatic-tuning.md)，以自動減輕這些問題。

  這個[範例](sql-database-performance-guidance.md#query-tuning-and-hinting)會顯示因為參數化查詢、如何偵測這種情況，以及如何使用查詢提示來解析，而導致次佳查詢計劃的影響。

- 請嘗試變更資料庫相容性層級，並執行智慧型查詢處理。 SQL 查詢最佳化工具可能會根據資料庫的相容性層級，產生不同的查詢計劃。 較高的相容性層級提供更[聰明的查詢處理功能](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)。

  - 如需查詢處理的詳細資訊，請參閱[查詢處理架構指南](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide)。
  - 若要變更資料庫相容性層級並進一步瞭解相容性層級之間的差異，請參閱[ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。
  - 若要閱讀有關基數估計的詳細資訊，請參閱[基數估計](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>使用次佳查詢執行計畫來解析查詢

下列各節將討論如何使用次佳的查詢執行計畫來解析查詢。

### <a name="ParamSniffing"></a>具有參數敏感性計畫（PSP）問題的查詢

當查詢最佳化工具產生的查詢執行計畫僅適用于特定參數值（或一組值）時，就會發生「參數敏感性計畫」（PSP）問題，而快取的計畫則不會針對連續使用的參數值進行優化。job. 不是最佳的計畫會導致查詢效能問題，並降低整體工作負載輸送量。

如需參數探查和查詢處理的詳細資訊，請參閱[查詢處理架構指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

有數種因應措施可減輕 PSP 問題。 每個因應措施都有相關聯的取捨和缺點：

- 在每次查詢執行時使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示。 此因應措施會交易編譯時間和增加的 CPU，以獲得更好的計劃品質。 對於需要高輸送量的工作負載而言，通常不會有 `RECOMPILE` 選項。
- 使用[選項（OPTIMIZE FOR ...）](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示，以產生足以滿足大部分參數值可能性的計畫之一般參數值來覆寫實際的參數值。 此選項需要充分了解最佳的參數值和相關聯的計畫特性。
- 使用[選項（[針對未知的優化）](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) ] 查詢提示來覆寫實際的參數值，改為使用密度向量平均值。 您也可以在本機變數中捕捉傳入參數值，然後在述詞中使用區域變數，而不是使用參數本身來執行此動作。 針對此修正，平均密度必須*夠好*。
- 使用[DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示，完全停用參數探查。
- 使用[KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示來避免在快取中重新編譯。 這個因應措施假設已有足夠的一般計畫，也就是快取中的方案。 您也可以停用自動統計資料更新，以降低將會收回良好計畫的機會，並編譯新的錯誤計畫。
- 藉由重寫查詢並在查詢文字中加入提示，以明確地使用[USE plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示來強制執行計畫。 或藉由使用查詢存放區或啟用[自動調整](sql-database-automatic-tuning.md)來設定特定計劃。
- 使用程序的的巢狀集合取代單一程序，每個程序都可以根據條件式邏輯和相關聯的參數值來使用。
- 建立靜態程序定義的動態字串執行替代方案。

如需解決 PSP 問題的詳細資訊，請參閱下列 blog 文章：

- [我嗅覺一個參數](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor 與動態 SQL 與程式的比較，以及參數化查詢的計畫品質](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL Server 中的 SQL 查詢優化技術：參數探查](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>參數化不正確所造成的編譯活動

當查詢具有常值時，資料庫引擎會自動參數化語句或使用者明確參數化語句，以減少編譯的次數。 使用相同模式但不同常值之查詢的大量編譯，可能會導致高 CPU 使用率。 同樣地，如果您只部分參數化繼續具有常值的查詢，則資料庫引擎不會進一步將查詢參數化。

以下是部分參數化查詢的範例：

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

在此範例中，`t1.c1` 會 `@p1`，但 `t2.c2` 會繼續採用 GUID 作為常值。 在此情況下，如果您變更 `c2`的值，則會將查詢視為不同的查詢，並會進行新的編譯。 為了減少此範例中的編譯，您也會將 GUID 參數化。

下列查詢會依查詢雜湊顯示查詢計數，以判斷查詢是否已正確參數化：

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

### <a name="factors-that-affect-query-plan-changes"></a>影響查詢計劃變更的因素

查詢執行計畫重新編譯可能會產生與原始快取計畫不同的查詢計劃。 現有的原始方案可能會因為各種原因而自動重新編譯：

- 查詢會參考架構中的變更
- 查詢會參考資料表的資料變更
- 查詢內容選項已變更

已編譯的計畫可能會因為各種原因而從快取中取出，例如：

- 實例重新開機
- 資料庫範圍設定變更
- 記憶體壓力
- 明確要求清除快取

如果您使用重新編譯提示，則不會快取計畫。

重新編譯（或快取收回之後的全新編譯）仍然可能產生與原始相同的查詢執行計畫。 當計畫從先前或原始計畫變更時，這些說明可能如下：

- **已變更的實體設計**：例如，新建立的索引會更有效地涵蓋查詢的需求。 新的索引可能會在新的編譯中使用，如果查詢最佳化工具決定使用新的索引比原先針對第一個查詢執行版本所選取的資料結構更理想。 對參考物件進行的任何實體變更，可能會在編譯時期產生新的計畫選擇。

- **伺服器資源差異**：當某個系統中的計畫與另一個系統中的方案不同時，資源可用性（例如可用的處理器數目）可能會影響所產生的計畫。 例如，如果一個系統有更多的處理器，可能會選擇平行計畫。

- **不同的統計資料**：與參考物件相關聯的統計資料可能已變更，或可能與原始系統的統計資料不同。 如果統計資料變更，而且發生重新編譯，則查詢最佳化工具會在其變更時，使用從開始的統計資料。 修訂過的統計資料「資料散發」和「頻率」可能與原始編譯的不同。 這些變更會用來建立基數估計值。 （*基數估計值*是預期會流經邏輯查詢樹狀結構的資料列數目）。基數估計值的變更可能會導致您選擇不同的實體運算子和相關聯的作業順序。 即使是統計資料的次要變更，也可能會導致變更的查詢執行計畫。

- **變更的資料庫相容性層級或基數估計工具版本**：資料庫相容性層級的變更可以啟用可能會產生不同查詢執行計畫的新策略和功能。 除了資料庫相容性層級之外，已停用或啟用的追蹤旗標4199或資料庫範圍設定的變更狀態 QUERY_OPTIMIZER_HOTFIXES 也會在編譯時期影響查詢執行計畫選擇。 追蹤旗標9481（強制舊版 CE）和2312（強制預設 CE）也會影響計畫。

## <a name="resource-limits-issues"></a>資源限制問題

查詢效能緩慢與次佳查詢計劃不相關，而且遺漏索引通常與資源不足或過度用。 如果查詢計劃是最佳的，查詢（和資料庫）可能會達到資料庫、彈性集區或受控實例的資源限制。 例如，可能會有超過服務層級的記錄寫入輸送量。

- 使用 Azure 入口網站偵測資源問題：若要查看資源限制是否為問題，請參閱[SQL Database 資源監視](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring)。 針對單一資料庫和彈性集區，請參閱[Database Advisor 效能建議](sql-database-advisor.md)和[查詢效能深入](sql-database-query-performance.md)解析。
- 使用[Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)偵測資源限制
- 使用[dmv](sql-database-monitoring-with-dmvs.md)偵測資源問題：

  - [Dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 會傳回 SQL 資料庫的 CPU、i/o 和記憶體耗用量。 每隔15秒的間隔會有一個資料列，即使資料庫中沒有任何活動。 歷程記錄資料會保留一個小時。
  - [Resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 會傳回 AZURE SQL DATABASE 的 CPU 使用量和儲存體資料。 資料會收集並匯總五分鐘的間隔。
  - [許多累積耗用高 CPU 的個別查詢](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

如果您將問題識別為資源不足，您可以升級資源，以增加 SQL 資料庫的容量以吸收 CPU 需求。 如需詳細資訊，請參閱在[Azure SQL Database 中調整單一資料庫資源](sql-database-single-database-scale.md)和[在 Azure SQL Database 中調整彈性集區資源](sql-database-elastic-pool-scale.md)。 如需調整受控實例的相關資訊，請參閱[服務層資源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="performance-problems-caused-by-increased-workload-volume"></a>因工作負載量增加而造成的效能問題

應用程式流量和工作負載量的增加，可能會造成 CPU 使用量增加。 但是您必須小心地診斷這個問題。 當您看到高 CPU 問題時，請回答這些問題，以判斷增加是否由工作負載磁片區的變更所造成：

- 應用程式中的查詢是否為高 CPU 問題的原因？
- 針對[您可以識別的最高 CPU 耗用查詢](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)：

  - 是否有多個與相同查詢相關聯的執行計畫？ 若是如此，為什麼？
  - 針對具有相同執行計畫的查詢，執行時間是否一致？ 執行計數是否增加？ 若是如此，工作負載的增加可能會造成效能問題。

總而言之，如果查詢執行計畫未以不同的方式執行，但 CPU 使用量隨著執行計數增加，則效能問題可能與工作負載增加有關。

找出驅動 CPU 問題的工作負載磁片區變更並不容易。 請考慮下列因素：

- **已變更的資源使用量**：例如，假設 CPU 使用量增加到80% 長一段時間的情況。 CPU 使用量本身並不表示工作負載磁片區已變更。 即使應用程式執行相同的工作負載，查詢執行計畫中的回歸和資料散發中的變更也可能會導致更多的資源使用量。

- **新查詢的外觀**：應用程式可能會在不同的時間驅動一組新的查詢。

- **要求數目增加或減少**：此案例是最明顯的工作負載量值。 查詢數目不一定會與更多的資源使用率相對應。 不過，此計量仍然是很重要的信號，假設其他因素不變。

使用 Intelligent Insights 來偵測[工作負載增加](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase)和[規劃回歸](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression)。

## <a name="waiting-related-problems"></a>等待相關問題

一旦您消除了與執行問題相關的次佳計畫和*等候相關*問題，效能問題通常是因為查詢可能正在等候某些資源。 等待相關的問題可能是由下列原因所造成：

- **封鎖**：

  一個查詢可能會持有資料庫中物件的鎖定，而有些則嘗試存取相同的物件。 您可以使用[dmv](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries)或[Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#locking)來識別封鎖查詢。
- **IO 問題**

  查詢可能正在等候頁面寫入資料或記錄檔。 在此情況下，請檢查 DMV 中的 `INSTANCE_LOG_RATE_GOVERNOR`、`WRITE_LOG`或 `PAGEIOLATCH_*` 等候統計資料。 請參閱使用 Dmv 來[識別 IO 效能問題](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)。
- **TempDB 問題**

  如果工作負載使用臨時表，或計畫中有 TempDB 溢出，則查詢可能會有 TempDB 輸送量的問題。 請參閱使用 Dmv 來[識別 TempDB 問題](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues)。
- **記憶體相關問題**

  如果工作負載沒有足夠的記憶體，則分頁生命預期可能會下降，或查詢可能會取得比所需更少的記憶體。 在某些情況下，查詢最佳化工具中的內建智慧功能將會修正記憶體相關問題。 請參閱使用 Dmv 來[識別記憶體授與問題](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)。

### <a name="methods-to-show-top-wait-categories"></a>顯示最上層等候類別的方法

這些方法通常用來顯示等候類型的最上層類別：

- 使用 Intelligent Insights 來識別效能降低的查詢，因為[等待時間增加](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- 使用[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)來尋找一段時間內每個查詢的等候統計資料。 在查詢存放區中，等候類型會合併到等候類別中。 您可以在 sys.databases 中找到等候類型的對應， [query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)。
- 使用[dm_db_wait_stats sys.databases](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)來傳回查詢作業期間執行的執行緒所遇到之所有等候的相關資訊。 您可以使用這個匯總視圖來診斷 Azure SQL Database 的效能問題，以及特定查詢和批次。 查詢可以等候資源、佇列等候或外部等候。
- 使用[dm_os_waiting_tasks sys.databases](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql)來傳回在某些資源上等待之工作佇列的相關資訊。

在高 CPU 案例中，如果下列情況，查詢存放區和等候統計資料可能不會反映 CPU 使用量：

- 高 CPU 耗用的查詢仍在執行中。
- 發生容錯移轉時，耗用大量 CPU 的查詢正在執行。

追蹤查詢存放區和等候統計資料的 Dmv 只會顯示成功完成和超時查詢的結果。 它們在語句完成之前，不會顯示目前正在執行之語句的資料。 使用動態管理檢視[sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)來追蹤目前執行中的查詢和相關聯的背景工作時間。

> [!TIP]
> 其他工具：
>
> - [TigerToolbox 等候和閂鎖](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>後續步驟

[SQL Database 監視和微調總覽](sql-database-monitor-tune-overview.md)