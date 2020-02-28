---
title: Azure 監視器中的記錄查詢總覽 |Microsoft Docs
description: 回答與記錄查詢相關的常見問題，並讓您開始使用它們。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670112"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure 監視器中的記錄查詢總覽
記錄查詢可協助您充分利用[Azure 監視器記錄](../platform/data-platform-logs.md)中收集的資料值。 功能強大的查詢語言可讓您聯結多個資料表的資料、匯總大型資料集，以及使用最少的程式碼來執行複雜的作業。 只要收集到支援的資料，就可以解決任何問題並執行分析，而且您會瞭解如何建立正確的查詢。

Azure 監視器中的某些功能（例如[深入](../insights/insights-overview.md)解析和[解決方案](../insights/solutions-inventory.md)）會處理記錄資料，而不會向您公開基礎查詢。 若要充分利用 Azure 監視器的其他功能，您應該瞭解查詢的建立方式，以及如何使用它們以互動方式分析 Azure 監視器記錄中的資料。

使用這篇文章做為瞭解 Azure 監視器中記錄查詢的起點。 它會回答常見的問題，並提供其他檔的連結，提供進一步的詳細資料和課程。

## <a name="how-can-i-learn-how-to-write-queries"></a>如何才能學習如何撰寫查詢？
如果您想要直接進行，可以從下列教學課程開始：

- [Azure 監視器中的 Log Analytics 入門](get-started-portal.md)。
- [開始使用 Azure 監視器中的記錄查詢](get-started-queries.md)。

當您瞭解基本概念之後，請使用您自己的資料或我們示範環境中的資料，從開始逐步解說多個課程： 

- [在 Azure 監視器記錄查詢中處理字串](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>記錄查詢所使用的語言為何？
Azure 監視器記錄是以[Azure 資料總管](/azure/data-explorer)為基礎，而且記錄查詢是使用相同的 Kusto 查詢語言（KQL）所撰寫。 這是設計成容易閱讀和撰寫的豐富語言，而且您應該能夠以最少的指引開始使用它。

請參閱[Azure 資料總管 KQL 檔](/azure/kusto/query)，以取得有關 KQL 的完整檔，並參考不同的可用功能。<br>
請參閱[開始使用 Azure 監視器中的記錄查詢](get-started-queries.md)，以取得使用 Azure 監視器記錄中的資料進行語言的快速逐步解說。
如需 Azure 監視器所使用之 KQL 版本的次要差異，請參閱[Azure 監視器記錄查詢語言差異](data-explorer-difference.md)。

## <a name="what-data-is-available-to-log-queries"></a>記錄查詢可以使用哪些資料？
Azure 監視器記錄檔中收集的所有資料都可以在記錄查詢中取得和分析。 不同的資料來源會將其資料寫入不同的資料表，但您可以在單一查詢中包含多個資料表，以分析多個來源的資料。 當您建立查詢時，您一開始會先判斷哪些資料表具有您要尋找的資料，因此您至少應該對 Azure 監視器記錄中資料的結構有基本瞭解。

如需填入 Azure 監視器記錄的不同資料來源清單，請參閱[Azure 監視器記錄的來源](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)。<br>
如需資料結構化方式的說明，請參閱[Azure 監視器記錄的結構](logs-structure.md)。

## <a name="what-does-a-log-query-look-like"></a>記錄查詢看起來是什麼樣子？
查詢可以簡單的單一資料表名稱來抓取該資料表中的所有記錄：

```Kusto
Syslog
```

或者，它可以篩選特定記錄、將其摘要，並在圖表中將結果視覺化：

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

若要進行更複雜的分析，您可以使用聯結來抓取多個資料表中的資料，以同時分析結果。

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
即使您不熟悉 KQL，您至少應該能夠找出這些查詢所使用的基本邏輯。 它們會以資料表名稱開頭，然後新增多個命令來篩選和處理該資料。 查詢可以使用任意數目的命令，而且您可以在熟悉可用的不同 KQL 命令時，撰寫更複雜的查詢。

如需介紹語言和一般功能的記錄查詢教學課程，請參閱[開始使用 Azure 監視器中的記錄查詢](get-started-queries.md)。<br>


## <a name="what-is-log-analytics"></a>什麼是 Log Analytics？
Log Analytics 是 Azure 入口網站中用來撰寫記錄查詢和以互動方式分析其結果的主要工具。 即使在 Azure 監視器中的其他地方使用記錄查詢，您通常會先使用 Log Analytics 來撰寫和測試查詢。

您可以從 Azure 入口網站中的數個位置啟動 Log Analytics。 可供 Log Analytics 使用的資料範圍取決於您啟動它的方式。 如需詳細資訊，請參閱[查詢範圍](scope.md)。

- 從 [ **Azure 監視器**] 功能表或 [ **Log Analytics 工作區**] 功能表中選取 [**記錄**]。
- 從 Application Insights 應用程式的 [**總覽**] 頁面選取 [**分析**]。
- 從 Azure 資源的功能表中選取 [**記錄**]。

![Log Analytics](media/log-query-overview/log-analytics.png)

如需介紹其中幾項功能的 Log Analytics 教學課程逐步解說，請參閱[開始使用 Azure 監視器中的 Log analytics](get-started-portal.md) 。

## <a name="where-else-are-log-queries-used"></a>還有使用記錄查詢的位置？
除了以互動方式使用 log Analytics 中的記錄查詢及其結果，您將在 Azure 監視器中使用查詢的區域包含下列各項：

- **警示規則。** [警示規則](../platform/alerts-overview.md)會主動識別您的工作區中資料的問題。  每個警示規則是根據以固定間隔自動執行的記錄搜尋。  會檢查結果來判斷是否應該建立警示。
- **儀表板。** 您可以將任何查詢的結果釘選到 [Azure 儀表板](../learn/tutorial-logs-dashboards.md)中，如此即可以視覺化方式顯示記錄和計量資料，並選擇性地將其與其他 Azure 使用者共用。
- **檢視。**  您可以使用[檢視設計工具](../platform/view-designer.md)，建立要包含在使用者儀表板中的資料視覺效果。  記錄查詢會在各個檢視中提供[圖格](../platform/view-designer-tiles.md)和[視覺效果部分](../platform/view-designer-parts.md)所使用的資料。  
- **匯出。**  當您從 Azure 監視器將記錄資料匯入到 Excel 或 [Power BI](../platform/powerbi.md) 時，您會建立定義要匯出之資料的記錄查詢。
- **Powershell。** 您可以從命令列或 Azure 自動化 runbook 執行 PowerShell 腳本，以使用[AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult)從 Azure 監視器取出記錄資料。  此 Cmdlet 需要查詢來決定要擷取的資料。
- **Azure 監視器記錄 API。**  [Azure 監視器記錄 API](https://dev.loganalytics.io) 可讓任何 REST API 用戶端從工作區擷取記錄資料。  API 要求包含針對 Azure 監視器執行的查詢，來判斷要擷取的資料。


## <a name="next-steps"></a>後續步驟
- 逐步解說在[Azure 入口網站中使用 Log Analytics 的教學](get-started-portal.md)課程。
- 逐步解說[撰寫查詢的教學](get-started-queries.md)課程。
