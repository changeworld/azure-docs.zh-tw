---
title: 分析 Application Insights 中的 Azure Functions 遙測
description: 瞭解如何查看和查詢收集的 Azure Functions 遙測資料，並儲存在 Azure 應用程式 Insights 中。
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 1d8d9cc9a7a4111e98b1d9141957769d6f157d45
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027725"
---
# <a name="analyze-azure-functions-telemetry-in-application-insights"></a>分析 Application Insights 中的 Azure Functions 遙測 

Azure Functions 與 Application Insights 整合，讓您能夠監視函數應用程式。 Application Insights 會收集您的函數應用程式所產生的遙測資料，包括應用程式寫入至記錄檔的資訊。 建立函數應用程式時，通常會啟用 Application Insights 整合。 如果您的函數應用程式未設定檢測金鑰，您必須先 [啟用 Application Insights 整合](configure-monitoring.md#enable-application-insights-integration)。 

根據預設，從函數應用程式收集的資料會儲存在 Application Insights 中。 在 [Azure 入口網站](https://portal.azure.com)中，Application Insights 提供一組廣泛的遙測資料視覺效果。 您可以深入探討錯誤記錄檔，以及查詢事件和計量。 本文提供如何查看和查詢所收集資料的基本範例。 若要深入瞭解如何在 Application Insights 中探索函數應用程式資料，請參閱 [什麼是 Application Insights？](../azure-monitor/app/app-insights-overview.md)。 

若要深入瞭解資料保留和潛在的儲存體成本，請參閱 [Application Insights 中的資料收集、保留和儲存](../azure-monitor/app/data-retention-privacy.md)。   

## <a name="viewing-telemetry-in-monitor-tab"></a>在 [監視] 索引標籤中查看遙測

藉由[啟用 Application Insights 整合](configure-monitoring.md#enable-application-insights-integration)，您可以在 [監視器] 索引標籤中檢視遙測資料。

1. 在函式應用程式頁面中，選取在設定 Application Insights 之後至少執行過一次的函式。 然後，從左窗格中選取 [監視器]。 定期選取 [重新整理]，直到函式引動過程的清單出現為止。

   ![引動過程清單](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > 當遙測用戶端將資料分批傳輸到伺服器時，可能需要五分鐘的時間，清單才會出現。 此延遲不適用[即時計量資料流](../azure-monitor/app/live-stream.md)。 當您載入頁面時，該服務會連線到 Functions 主機，以便將記錄直接串流處理到頁面。

1. 若要查看特定函式引動過程的記錄，請選取該引動過程的 [日期 (UTC)] 資料行連結。 該引動過程的記錄輸出會顯示在新頁面中。

   ![引動過程詳細資料](media/functions-monitoring/invocation-details-ai.png)

1. 選擇 [在 Application Insights 中執行]，以檢視可在 Azure 記錄中擷取 Azure 監視器記錄資料的查詢來源。 如果這是您第一次在訂用帳戶中使用 Azure Log Analytics，系統就會要求您進行啟用。

1. 啟用 Log Analytics 之後，即會顯示下列查詢。 您可以看到查詢結果限制為過去30天 (`where timestamp > ago(30d)`) ，且結果顯示 () 不超過20個數據列 `take 20` 。 相反地，您函式的引動過程詳細資料清單適用於過去 30 天且無任何限制。

   ![Application Insights 分析引動過程清單](media/functions-monitoring/ai-analytics-invocation-list.png)

如需詳細資訊，請參閱本文稍後的[查詢遙測資料](#query-telemetry-data)。

## <a name="view-telemetry-in-application-insights"></a>在 Application Insights 中檢視遙測

若要從 [Azure 入口網站](https://portal.azure.com)中的函數應用程式開啟 Application Insights：

1. 在入口網站中流覽至您的函數應用程式。

1. 在左側頁面的 [**設定**] 下選取 **Application Insights** 。 

1. 如果這是您第一次使用 Application Insights 搭配您的訂用帳戶，系統會提示您啟用它。 若要這樣做，請選取 [ **開啟 Application Insights**]， **然後選取 [套用於下** 一個頁面]。

![從函式應用程式的 [概觀] 頁面開啟 Application Insights](media/functions-monitoring/ai-link.png)

如需如何使用 Application Insights 的相關資訊，請參閱 [Application Insights 文件](/azure/application-insights/)。 本節示範一些如何在 Application Insights 中檢視資料的範例。 如果您已經熟悉 Application Insights，即可直接前往[關於如何設定和自訂遙測資料的小節](configure-monitoring.md#configure-log-levels)。

![Application Insights 的 [概觀] 索引標籤](media/functions-monitoring/metrics-explorer.png)

評估函式中的行為、效能和錯誤時，Application Insights 的下列區域很實用：

| 調查 | 描述 |
| ---- | ----------- |
| **[失敗](../azure-monitor/app/asp-net-exceptions.md)** |  根據函式失敗和伺服器例外狀況，建立圖表和警示。 **作業名稱** 是函式名稱。 除非您實作自訂遙測來取得相依性，否則不會顯示相依性中的失敗。 |
| **[效能](../azure-monitor/app/performance-counters.md)** | 藉由檢視每個 **雲端角色執行個體** 的資源使用率和輸送量，來分析效能問題。 這種效能資料適用于將函式膠著在底層資源的情況下。 |
| **[計量](../azure-monitor/platform/metrics-charts.md)** | 建立以計量為基礎的圖表和警示。 計量包括函式引動過程的數目、執行時間和成功率。 |
| **[即時計量](../azure-monitor/app/live-stream.md)** | 以近乎即時的方式，查看計量資料的建立時間。 |

## <a name="query-telemetry-data"></a>查詢遙測資料

[Application Insights 分析](../azure-monitor/log-query/log-query-overview.md)可讓您在資料庫中，以資料表形式存取所有遙測資料。 分析會提供用於擷取、操作和視覺化資料的查詢語言。 

選擇 [記錄] 以探索或查詢記錄的事件。

![分析範例](media/functions-monitoring/analytics-traces.png)

以下的查詢範例會顯示過去 30 分鐘內每個背景工作角色的要求分佈狀況。

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

可用的資料表會顯示於左側 [結構描述] 索引標籤中。 您可以找到下表中函式引動過程所產生的資料：

| Table | 描述 |
| ----- | ----------- |
| **traces** | 執行時間所建立的記錄，以及您函式程式碼中的追蹤。 |
| **requests** | 每個函式引動過程一個要求。 |
| **exceptions** | 由執行階段擲回的任何例外狀況。 |
| **customMetrics** | 成功和失敗引動過程的計數、成功率、持續時間。 |
| **customEvents** | 由執行階段所追蹤的事件，例如：觸發函式的 HTTP 要求。 |
| **performanceCounters** | 函式執行所在的伺服器效能相關資訊。 |

其他資料表適用於可用性測試，以及用戶端和瀏覽器遙測。 您可以實作自訂遙測，將資料新增至它們。

在每個資料表內，一些 Functions 特有的資料會位於 `customDimensions` 欄位中。  例如，下列查詢會擷取記錄層級為 `Error` 的所有追蹤。

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

執行階段會提供 `customDimensions.LogLevel` 和 `customDimensions.Category` 欄位。 您可以在函式程式碼內撰寫的記錄中提供額外的欄位。 如需 c # 中的範例，請參閱《 .NET 類別庫開發人員指南》中的 [結構化記錄](functions-dotnet-class-library.md#structured-logging) 。

## <a name="consumption-plan-specific-metrics"></a>取用方案特有的計量

在取用 [方案](functions-scale.md#consumption-plan)中執行時，單一函數執行的執行 *成本* 會以 *GB 為單位* 來測量。 執行成本的計算方式是將其記憶體使用量與執行時間結合在一起。 若要深入瞭解，請參閱 [預估耗用量方案成本](functions-consumption-costs.md)。

下列遙測查詢適用于影響取用方案中執行的函式成本的計量。

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>後續步驟

深入瞭解監視 Azure Functions：

+ [監視 Azure Functions](functions-monitoring.md)
+ [如何設定 Azure Functions 的監視](configure-monitoring.md)

