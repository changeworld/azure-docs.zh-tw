---
title: Azure SQL Database 中的查詢效能問題類型
description: 在本文中，瞭解 Azure SQL Database 中的查詢效能問題類型，並瞭解如何使用這些問題來識別和解決查詢
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 4d0f5404a64eae99ced0dd797954ba042b50060f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217221"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Azure SQL Database 中可偵測的查詢效能瓶頸類型
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

當您嘗試解決效能瓶頸時，請先判斷瓶頸是否在查詢處於執行中狀態或等候狀態時發生。 不同的解決方式取決於這項判斷。 使用下圖可協助您瞭解可能導致執行相關問題或等候相關問題的因素。 本文將討論與每個問題類型相關的問題和解決方式。

您可以使用 Azure SQL Database [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) 或 SQL Server [dmv](database/monitoring-with-dmvs.md) 來偵測這些類型的效能瓶頸。

![工作負載狀態](./media/identify-query-performance-issues/workload-states.png)

執行 **相關的問題**：與運行相關的問題一般會與編譯問題有關，導致查詢計劃或資源不足或過度使用相關的執行問題。
**等候相關問題**：等候相關的問題通常與下列相關：

- 鎖定 (封鎖)
- I/O
- 與 TempDB 使用方式相關的爭用
- 記憶體授與等候

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>編譯問題導致無法進行查詢計劃

SQL 查詢最佳化工具所產生的次佳計畫可能是查詢效能變慢的原因。 SQL 查詢最佳化工具可能會產生較佳的計畫，原因是遺漏索引、過時的統計資料、要處理的資料列數目估計不正確，或所需記憶體的估計不正確。 如果您知道查詢在過去或另一個實例上執行的速度較快，請比較實際的執行計畫，查看它們是否不同。

- 使用下列其中一種方法來識別遺漏的索引：

  - 使用 [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index)。
  - 適用于單一和集區資料庫的[Database Advisor](database/database-advisor-implement-performance-recommendations.md) 。
  - Dmv. 此範例說明遺漏索引的影響、如何使用 Dmv 偵測 [遺漏的索引](database/performance-guidance.md#identifying-and-adding-missing-indexes) ，以及如何執行遺漏索引建議的影響。
- 嘗試套用 [查詢提示](/sql/t-sql/queries/hints-transact-sql-query)、 [更新統計資料](/sql/t-sql/statements/update-statistics-transact-sql)或 [重建索引](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) ，以取得更好的計畫。 在 Azure SQL Database 中啟用 [自動計畫更正](../azure-sql/database/automatic-tuning-overview.md) ，以自動減輕這些問題。

  此 [範例](database/performance-guidance.md#query-tuning-and-hinting) 顯示因為參數化查詢、如何偵測這種狀況，以及如何使用查詢提示來解決，而導致次佳查詢計劃的影響。

- 請嘗試變更資料庫相容性層級和執行智慧型查詢處理。 根據您資料庫的相容性層級而定，SQL 查詢最佳化工具可能會產生不同的查詢計劃。 相容性層級較高可提供更 [聰明的查詢處理功能](/sql/relational-databases/performance/intelligent-query-processing)。

  - 如需查詢處理的詳細資訊，請參閱 [查詢處理架構指南](/sql/relational-databases/query-processing-architecture-guide)。
  - 若要變更資料庫相容性層級，並深入瞭解相容性層級之間的差異，請參閱 [ALTER database](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。
  - 若要閱讀有關基數估計的詳細資訊，請參閱 [基數估計](/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>使用次佳的查詢執行計畫來解析查詢

下列各節將討論如何使用最佳的查詢執行計畫來解析查詢。

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> 具有參數敏感性計畫 (PSP) 問題的查詢

如果查詢最佳化工具產生的查詢執行計畫僅適用于特定參數值 (或) 值的集合，且快取的計畫不是用於連續執行的參數值，則會發生參數敏感性計畫 (PSP) 問題。 不是最佳的方案則會導致查詢效能問題，並降低整體工作負載輸送量。

如需參數探查和查詢處理的詳細資訊，請參閱 [查詢處理架構指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

有幾個因應措施可減輕 PSP 問題。 每個因應措施都有相關的取捨和缺點：

- 在每次查詢執行時使用 [RECOMPILE](/sql/t-sql/queries/hints-transact-sql-query) 查詢提示。 此因應措施會交易編譯時間和增加的 CPU，以獲得更好的計劃品質。 此 `RECOMPILE` 選項通常不適用於需要高輸送量的工作負載。
- 使用 [選項 (OPTIMIZE FOR ... ) ](/sql/t-sql/queries/hints-transact-sql-query) 查詢提示，以一般參數值來覆寫實際參數值，此值會產生足以滿足大部分參數值可能性的計畫。 此選項需要充分了解最佳的參數值和相關聯的計畫特性。
- 您可以使用 [選項 (OPTIMIZE FOR UNKNOWN) ](/sql/t-sql/queries/hints-transact-sql-query) 查詢提示來覆寫實際參數值，而改為使用密度向量平均值。 您也可以藉由在本機變數中捕捉傳入參數值，然後使用述詞內的區域變數，而不是使用參數本身，來達成此目的。 針對此修正，平均密度必須 *夠好*。
- 使用 [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query) 查詢提示，完全停用參數探查。
- 使用 [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query) 查詢提示來防止在快取中重新編譯。 此因應措施假設有足夠的一般方案是快取中的計畫。 您也可以停用自動統計資料更新，以降低良好計畫將被收回的機率，以及將會編譯新的不良計畫。
- 藉由重寫查詢並在查詢文字中加入提示，以明確地使用 [USE plan](/sql/t-sql/queries/hints-transact-sql-query) 查詢提示來強制執行計畫。 或者，使用查詢存放區或啟用 [自動調整](../azure-sql/database/automatic-tuning-overview.md)來設定特定計劃。
- 使用程序的的巢狀集合取代單一程序，每個程序都可以根據條件式邏輯和相關聯的參數值來使用。
- 建立靜態程序定義的動態字串執行替代方案。

如需解決 PSP 問題的詳細資訊，請參閱下列 blog 文章：

- [我嗅覺參數](/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor 與動態 SQL 的比較，與程式化查詢的計畫品質比較](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)
- [SQL Server 中的 SQL 查詢優化技術：參數探查](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>因為參數化不當所造成的編譯活動

當查詢具有常值時，資料庫引擎會自動參數化語句，或使用者明確參數化語句來減少編譯次數。 使用相同模式但不同常值的查詢有大量的編譯，可能會導致高 CPU 使用率。 同樣地，如果您只部分參數化繼續具有常值的查詢，資料庫引擎就不會再將查詢參數化。

以下是部分參數化查詢的範例：

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F';
```

在此範例中， `t1.c1` 會採用 `@p1` ，但會 `t2.c2` 繼續以 GUID 作為常值。 在此情況下，如果您變更的值 `c2` ，系統會將查詢視為不同的查詢，並會發生新的編譯。 為了減少此範例中的編譯，您也會將 GUID 參數化。

下列查詢顯示依查詢雜湊的查詢計數，以判斷查詢是否已正確參數化：

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
ORDER BY count (distinct p.query_id) DESC;
```

### <a name="factors-that-affect-query-plan-changes"></a>影響查詢計劃變更的因素

查詢執行計畫重新編譯可能會產生與原始快取計畫不同的查詢計劃。 基於各種原因，可能會自動重新編譯現有的原始方案：

- 此查詢會參考架構中的變更
- 查詢會參考資料表的資料變更
- 查詢內容選項已變更

已編譯的計畫可能會因為各種原因而從快取中退出，例如：

- 實例重新開機
- 資料庫範圍的設定變更
- 記憶體壓力
- 清除快取的明確要求

如果您使用重新編譯提示，則不會快取方案。

重新編譯在快取收回之後 (或重新編譯編譯) 仍可能導致產生與原始相同的查詢執行計畫。 當計畫從先前或原始方案變更時，可能會有下列說明：

- **已變更的實體設計**：例如，新建立的索引會更有效地涵蓋查詢的需求。 如果查詢最佳化工具決定使用新的索引比原先針對第一版查詢執行所選取的資料結構更理想，則新的索引可能會用於新的編譯。 參考物件的任何實體變更可能會在編譯時期產生新的計畫選擇。

- **伺服器資源差異**：當某個系統中的計畫與另一個系統中的計畫不同時，資源可用性（例如可用的處理器數目）可能會影響所產生的計畫。 例如，如果一個系統有更多處理器，可能會選擇平行計畫。

- **不同的統計資料**：與參考物件相關聯的統計資料可能已變更，或可能與原始系統的統計資料不同。 如果統計資料變更，而且發生重新編譯，則查詢最佳化工具會在其變更時從其開始使用統計資料。 修訂過的統計資料資料分佈和頻率可能與原始編譯的不同。 這些變更會用來建立基數估計值。  (*基數估計值* 是預期要流經邏輯查詢樹狀結構的資料列數目。 ) 基數估計值的變更，可能會讓您選擇不同的實體運算子和相關聯的作業順序。 即使統計資料的次要變更可能會導致變更查詢執行計畫。

- **變更資料庫相容性層級或基數估算器版本**：對資料庫相容性層級所做的變更，可以啟用可能產生不同查詢執行計畫的新策略和功能。 除了資料庫相容性層級以外，已停用或已啟用的追蹤旗標4199或資料庫範圍設定的變更狀態 QUERY_OPTIMIZER_HOTFIXES 也可能會影響編譯時期的查詢執行計畫選擇。 追蹤旗標 9481 (強制舊版 CE) 和 2312 (強制預設 CE) 也會影響此計畫。

## <a name="resource-limits-issues"></a>資源限制問題

效能緩慢的查詢效能與較佳的查詢計劃和遺漏的索引相關，通常與資源不足或過度過度相關。 如果查詢計劃是最佳的，則查詢 (和資料庫) 可能會達到資料庫、彈性集區或受控實例的資源限制。 範例可能會超出服務層級的記錄寫入輸送量。

- 使用 Azure 入口網站偵測資源問題：若要查看資源限制是否為問題，請參閱 [SQL Database 資源監視](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring)。 針對單一資料庫和彈性集區，請參閱 [Database Advisor 效能建議](database/database-advisor-implement-performance-recommendations.md) 和 [查詢效能深入](database/query-performance-insight-use.md)解析。
- 使用[Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)偵測資源限制
- 使用 [dmv](database/monitoring-with-dmvs.md)偵測資源問題：

  - [Sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV 會傳回資料庫的 CPU、i/o 和記憶體耗用量。 每隔15秒的間隔都會有一個資料列存在，即使資料庫中沒有任何活動。 歷程記錄資料會保留一個小時。
  - [Sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV 會傳回 AZURE SQL DATABASE 的 CPU 使用量和儲存體資料。 資料會以五分鐘的間隔收集和匯總。
  - [累積耗用高 CPU 的許多個別查詢](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

如果您將問題識別為資源不足，則可以升級資源以增加資料庫的容量，以吸收 CPU 需求。 如需詳細資訊，請參閱在 [Azure SQL Database 中調整單一資料庫資源](database/single-database-scale.md) ，以及 [Azure SQL Database 中調整彈性集區資源](database/elastic-pool-scale.md)。 如需調整受控實例的相關資訊，請參閱 [服務層級資源限制](managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>提高工作負載量所造成的效能問題

增加應用程式流量和工作負載量可能會造成 CPU 使用量增加。 但是您必須小心地診斷此問題。 當您看到高 CPU 的問題時，請回答這些問題，以判斷此增加是否因工作負載量的變更而造成：

- 應用程式的查詢是否為高 CPU 問題的原因？
- 針對 [您可以識別的最高 CPU 耗用查詢](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)：

  - 有多個執行計畫與相同的查詢相關聯嗎？ 若是如此，為什麼？
  - 針對具有相同執行計畫的查詢，執行時間是否一致？ 執行計數是否增加？ 如果是這樣，工作負載的增加可能會導致效能問題。

總而言之，如果查詢執行計畫未以不同的方式執行，但 CPU 使用率隨著執行計數而增加，則效能問題可能與工作負載增加有關。

找出驅動 CPU 問題的工作負載磁片區變更不一定很容易。 請考慮下列因素：

- **變更的資源使用量**：例如，假設 CPU 使用率在一段長時間內增加到80% 的案例。 CPU 使用量只是表示工作負載磁片區已變更。 即使應用程式執行相同的工作負載，查詢執行計畫中的回歸和資料散發中的變更也會造成更多的資源使用量。

- **新查詢的外觀**：應用程式可能會在不同的時間驅動一組新的查詢。

- **要求數目增加或減少**：此案例是最明顯的工作負載量值。 查詢數目不一定會與更多的資源使用率相對應。 但是，如果其他因素未變更，則此計量仍是很重要的信號。

使用 Intelligent Insights 來偵測 [工作負載增加](database/intelligent-insights-troubleshoot-performance.md#workload-increase) 和 [規劃回歸](database/intelligent-insights-troubleshoot-performance.md#plan-regression)。

## <a name="waiting-related-problems"></a>等候相關的問題

一旦您消除了與執行問題相關的次佳計畫和 *等候相關* 問題，效能問題通常是查詢可能正在等候某些資源。 等候相關的問題可能是由下列原因所造成：

- **封鎖**：

  一個查詢可能會保存資料庫中物件的鎖定，而其他則嘗試存取相同的物件。 您可以使用 [dmv](database/monitoring-with-dmvs.md#monitoring-blocked-queries) 或 [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#locking)來識別封鎖查詢。 如需詳細資訊，請參閱 [瞭解及解決 AZURE SQL 封鎖問題](database/understand-resolve-blocking.md)。
- **IO 問題**

  查詢可能正在等待頁面寫入資料或記錄檔。 在此情況下，請 `INSTANCE_LOG_RATE_GOVERNOR` 檢查 `WRITE_LOG` DMV 中的、或 `PAGEIOLATCH_*` 等候統計資料。 請參閱使用 Dmv [找出 IO 效能問題](database/monitoring-with-dmvs.md#identify-io-performance-issues)。
- **TempDB 問題**

  如果工作負載使用臨時表，或計畫中有 TempDB 溢出，則查詢可能會有 TempDB 輸送量的問題。 請參閱使用 Dmv 來 [識別 TempDB 問題](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues)。
- **記憶體相關問題**

  如果工作負載沒有足夠的記憶體，頁面的預期壽命可能會下降，或者查詢可能會比所需的記憶體少。 在某些情況下，查詢最佳化工具中的內建智慧會修正記憶體相關問題。 請參閱使用 Dmv [找出記憶體授與問題](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)。

### <a name="methods-to-show-top-wait-categories"></a>顯示前幾個等候類別的方法

這些方法通常用來顯示等候類型的最上層類別：

- 使用 Intelligent Insights 來識別因為[等待時間增加](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)而效能降低的查詢
- 使用 [查詢存放區](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) 來尋找一段時間內每個查詢的等候統計資料。 在查詢存放區中，等候類型會合併到等候類別中。 您可以在 [sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)中找到等候類別對應至等候類型。
- 使用 [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) 傳回在查詢作業期間執行的執行緒所遇到之所有等候的相關資訊。 您可以使用此匯總視圖來診斷 Azure SQL Database 的效能問題，以及特定查詢和批次的效能問題。 查詢可能正在等候資源、佇列等候或外部等候。
- 使用 [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 來傳回有關等候某些資源之工作佇列的資訊。

在高 CPU 案例中，如果有下列情況，查詢存放區和等候統計資料可能不會反映 CPU 使用量：

- 高 CPU 耗用的查詢仍在執行。
- 當發生容錯移轉時，會執行高 CPU 耗用的查詢。

追蹤查詢存放區和等候統計資料的 Dmv 只會顯示已成功完成和超時查詢的結果。 除非語句完成，否則它們不會顯示目前執行之語句的資料。 使用動態管理檢視 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 來追蹤目前執行中的查詢和相關聯的背景工作時間。

> [!TIP]
> 其他工具：
>
> - [TigerToolbox 等候和閂鎖](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>後續步驟

[SQL Database 監視和微調總覽](database/monitor-tune-overview.md)