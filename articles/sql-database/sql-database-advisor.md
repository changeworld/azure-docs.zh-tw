---
title: 適用于單一和集區資料庫的 Database advisor 效能建議
description: Azure SQL Database 提供單一和集區資料庫的建議，可改善 Azure SQL Database 中的查詢效能。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5b0aeec851c8f514492e32792f48e955597ced5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096566"
---
# <a name="database-advisor-performance-recommendations-for-single-and-pooled-databases"></a>Database Advisor 單一和集區資料庫的效能建議

Azure SQL Database 會學習及適應您的應用程式。 針對單一和集區資料庫，SQL Database 有許多資料庫顧問，可提供可讓您將效能最大化的自訂建議。 這些資料庫顧問會持續評估和分析使用歷程記錄，並根據有助於改善效能的工作負載模式提供建議。

## <a name="performance-overview"></a>效能總覽

效能總覽提供資料庫效能的摘要，並協助您進行效能微調和疑難排解。

![Azure SQL Database 的效能總覽](./media/sql-database-performance/performance-overview-annotated.png)

- [建議]**** 圖格會提供適用於資料庫的微調建議分解 (如果有多個建議，則會顯示前三個)。 按一下此磚會帶您前往 [**[效能建議] 選項](sql-database-advisor-portal.md#viewing-recommendations)**。
- [微調活動] **** 圖格會提供資料庫進行中和已完成微調動作的摘要，讓您快速檢視調整活動的歷程記錄。 按一下此圖格會帶您前往您的資料庫的完整微調記錄檢視。
- **自動調整**圖格會顯示資料庫的**[自動調整](sql-database-automatic-tuning-enable.md)** 設定（自動套用至資料庫的微調選項）。 按一下此圖格會開啟 [自動化組態] 對話方塊。
- [資料庫查詢]**** 圖格會顯示資料庫的查詢效能摘要 (整體 DTU 使用量和排名最前面的資源取用查詢)。 按一下此圖格，即會帶您前往 [[查詢效能深入解析](sql-database-query-performance.md)]****。

## <a name="performance-recommendation-options"></a>效能建議選項

Azure SQL Database 中適用于單一和集區資料庫的效能建議選項如下：

| 效能建議 | 單一資料庫和集區資料庫支援 | 實例資料庫支援 |
| :----------------------------- | ----- | ----- |
| **建立索引建議**-建議建立可改善工作負載效能的索引。 | 是 | 否 |
| 卸載**索引建議**-建議每天移除多餘和重複的索引，但不包括唯一索引，以及長時間未使用的索引（>90 天）。 請注意，此選項與使用分割區切換和索引提示的應用程式不相容。 高階和商務關鍵服務層級不支援卸載未使用的索引。 | 是 | 否 |
| **參數化查詢建議（預覽）** -建議當您有一或多個查詢不斷重新編譯，但最後有相同的查詢執行計畫時，強制參數化。 | 是 | 否 |
| **修正架構問題建議（預覽）** -當 SQL Database 服務注意到 sql 資料庫上發生架構相關 SQL 錯誤數目的異常時，會顯示架構更正的建議。 Microsoft 即將淘汰「修正結構描述問題」的建議。 | 是 | 否 |

![Azure SQL Database 的效能建議](./media/sql-database-performance/performance-recommendations-annotated.png)

若要套用效能建議，請參閱套用[建議](sql-database-advisor-portal.md#applying-recommendations)。 若要查看建議的狀態，請參閱[監視作業](sql-database-advisor-portal.md#monitoring-operations)。

您也可以找到過去所套用微調動作的完整歷程記錄。

## <a name="create-index-recommendations"></a>建立索引建議

Azure SQL Database 會持續監視正在執行的查詢，並找出可改善效能的索引。 確信遺漏特定索引之後，就會建立新的 [建立索引]**** 建議。

評估一段時間之後，Azure SQL Database 會確定索引所能提升的效能。 根據評估的顯現效能，將建議分類為高、中或低。

使用建議建立的索引一律會標記為自動建立的索引。 您可以藉由查看[sys.databases 視圖](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql)，查看哪些索引是自動建立的。 自動建立的索引不會封鎖 ALTER/RENAME 命令。

如果您嘗試卸除有自動建立之索引的資料行。 自動建立的索引會隨該命令卸除。 一般索引會對已編製索引的資料行封鎖 ALTER/RENAME 命令。

套用索引建立建議之後，Azure SQL Database 會比較基準效能和查詢效能。 如果新索引改善了效能，建議就會標示為成功並可取得影響報告。 如果索引沒有改善效能，則會自動還原。 Azure SQL Database 使用此程式來確保建議改善資料庫效能。

任何**建立索引**建議都有輪詢原則，如果資料庫或集區的資源使用量很高時不允許套用建議。 輪詢原則會將 CPU、資料 IO、記錄 IO 和可用儲存體列入考慮。

如果 CPU、資料 IO 或記錄 IO 在過去30分鐘內高於80%，則建立索引建議會延遲。 如果在建立索引後，可用的儲存體低於 10%，則建議會進入錯誤狀態。 如果幾天後，自動調整仍認為該索引有所幫助，則程序會重新開始。

此程序會重複進行，直到有足夠的儲存體可用來建立索引，或直到索引不再是有所幫助的狀態為止。

## <a name="drop-index-recommendations"></a>卸除索引建議

除了偵測遺漏的索引之外，Azure SQL Database 持續分析現有索引的效能。 如果未使用索引，Azure SQL Database 會建議將它卸除。 在兩種情況下會建議使用卸除索引：

- 索引是另一個索引的複本 (編製和內含索引的資料行、分割、結構描述及篩選器相同)。
- 索引長時間都未使用 (93 天)。

卸除索引建議在實作後也會經過驗證。 如果效能提升，即可取得影響報告。 如果效能降低，則會還原建議。

## <a name="parameterize-queries-recommendations-preview"></a>參數化查詢建議（預覽）

當您有一或多個查詢不斷被重新編譯，但結果都是相同的查詢執行計劃時，即會出現*參數化查詢*建議。 這種狀況會製造套用強制參數化的機會。 而強制參數化允許查詢計畫納入快取並在未來重複使用，以改善效能並減少資源使用量。

針對 SQL Server 發出的每個查詢一開始需要重新編譯，以產生執行計畫。 每個產生的計畫都會新增至計畫快取。 相同查詢的後續執行可以從快取中重複使用這個計畫，而不需要進一步編譯。

因為每次非參數化值不同時，就會重新編譯執行計畫，所以採用非參數化值的查詢可能造成效能負擔。 在許多情況下，具有不同參數值的相同查詢都會產生相同的執行計畫。 不過，這些計畫仍會個別新增至計畫快取。

重新編譯執行計畫的程序會使用資料庫資源，增加查詢持續時間，以及造成計畫快取溢位。 而這些事件會導致系統從快取中回收計畫。 在資料庫上設定強制參數化選項，可以改變此 SQL Server 行為。

為了協助您評估此建議的影響，我們提供了實際 CPU 使用量和預計 CPU 使用量 (如同已套用建議) 之間的比較。 這項建議可協助您節省 CPU 資源。 也可以協助您減少計畫快取的查詢持續時間和負擔，這表示多個計畫可以留在快取中並加以重複使用。 您可以按一下 [套用]**** 命令，快速套用此建議。

套用此建議之後，即可在幾分鐘內於資料庫上啟用強制參數化。 它會啟動可持續約 24 小時的監視程序。 經過這段期間之後，您即可看到驗證報告。 此報告顯示資料庫在套用建議前後 24 小時的 CPU 使用量。 SQL Database 建議程式有一項安全機制，會在偵測到效能衰退時，自動還原所套用的建議。

## <a name="fix-schema-issues-recommendations-preview"></a>修正結構描述問題建議 (預覽)

> [!IMPORTANT]
> Microsoft 即將淘汰「修正結構描述問題」的建議。 建議您使用 [Intelligent Insights](sql-database-intelligent-insights.md) 來監視資料庫效能問題，包括「修正結構描述問題」建議先前涵蓋的結構描述問題。

當 Azure SQL Database 服務注意到 SQL 資料庫上發生的架構相關 SQL 錯誤數目異常時，就會出現**修正架構問題**的建議。 您的資料庫在一小時內遇到多個結構描述相關的錯誤時 (無效的資料行名稱、無效的物件名稱等)，通常會出現此建議。

「架構問題」是 SQL Server 中的語法錯誤類別。 當 SQL 查詢定義與資料庫結構描述定義不符時，就會發生這類問題。 例如，查詢預期的其中一個資料行可能在目標資料表中遺失，反之亦然。

當 Azure SQL Database 服務注意到 SQL 資料庫上發生的架構相關 SQL 錯誤數目異常時，就會出現「修正架構問題」建議。 下表顯示與結構描述問題相關的錯誤：

| SQL 錯誤碼 | 訊息 |
| --- | --- |
| 201 |程序或函數 '' 必須有參數 '**'，但未提供。 |
| 207 |無效的資料行名稱 '*'。 |
| 208 |無效的物件名稱 '*'。 |
| 213 |資料行名稱或提供的數值數量與資料表定義不相符。 |
| 2812 |找不到預存程序 ' *'。 |
| 8144 |程序或函數 * 指定了太多的引數。 |

## <a name="custom-applications"></a>自訂應用程式

開發人員可能會考慮使用 Azure SQL Database 的效能建議來開發自訂應用程式。 您可以透過[AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API 來存取資料庫的入口網站中所列的所有建議。

## <a name="next-steps"></a>後續步驟

- 如需如何自動調整資料庫索引和查詢執行計畫的詳細資訊，請參閱 [Azure SQL Database 自動調整](sql-database-automatic-tuning.md)。
- 如需如何使用效能問題的自動化診斷與根本原因分析來自動監視資料庫效能的詳細資訊，請參閱 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md)。
- 請參閱[查詢效能深入解析](sql-database-query-performance.md)，以了解和檢視排名最前面查詢的效能影響。
