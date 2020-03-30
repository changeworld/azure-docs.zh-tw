---
title: Azure Application Insights 中的相依性追蹤 | Microsoft Docs
description: 使用應用程式見解監視來自本地或 Microsoft Azure Web 應用程式的依賴項調用。
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1d4e8d1a0482257c92f47a00bd440e786c09c7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292116"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Azure 應用程式見解中的依賴項跟蹤 

*依賴項*是應用程式調用的外部元件。 這通常是使用 HTTP 呼叫的服務，或資料庫，或檔案系統。 [應用程式見解](../../azure-monitor/app/app-insights-overview.md)測量依賴項調用的持續時間，無論其失敗與否，以及依賴項名稱等其他資訊。 您可以調查特定的依賴項調用，並將它們與請求和異常相關聯。

## <a name="automatically-tracked-dependencies"></a>自動跟蹤依賴項

.NET 和 .NET 核心附帶`DependencyTrackingTelemetryModule`的應用程式見解 SDK 是自動收集依賴項的遙測模組。 根據連結的官方文檔配置時，將自動為[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)和[ASP.NET核心](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)應用程式啟用此依賴項集合。`DependencyTrackingTelemetryModule`作為[此](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)NuGet 包發貨，在使用 NuGet 包`Microsoft.ApplicationInsights.Web`或`Microsoft.ApplicationInsights.AspNetCore`時自動攜帶。

 `DependencyTrackingTelemetryModule`當前自動跟蹤以下依賴項：

|相依性 |詳細資料|
|---------------|-------|
|Http/HTTPs | 本地或遠端 HTTP/HTTPs 調用 |
|WCF 調用| 僅當使用基於 Http 的綁定時，才自動跟蹤。|
|SQL | 使用`SqlClient`進行的電話。 請參閱[此](#advanced-sql-tracking-to-get-full-sql-query)以捕獲 SQL 查詢。  |
|[Azure 存儲（Blob、表、佇列）](https://www.nuget.org/packages/WindowsAzure.Storage/) | 使用 Azure 存儲用戶端進行的調用。 |
|[事件中心用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 版本 1.1.0 及以上。 |
|[ServiceBus 用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 版本 3.0.0 及以上。 |
|Azure Cosmos DB | 僅在使用 HTTP/HTTPS 時自動跟蹤。 Application Insights 不會擷取 TCP 模式。 |

如果您缺少依賴項，或者使用其他 SDK 請確保它位於[自動收集的依賴項](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)清單中。 如果依賴項未自動收集，您仍可以使用[跟蹤依賴項調用](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)手動跟蹤它。

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>在主控台應用中設置自動依賴項跟蹤

要自動跟蹤來自 .NET 主控台應用的依賴項，請安裝`Microsoft.ApplicationInsights.DependencyCollector`Nuget 包`DependencyTrackingTelemetryModule`，並按照如下方式初始化：

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

對於 .NET 核心主控台應用遙測配置.活動已過時。 請參閱[工作人員服務文件](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)和[ASP.NET核心監控文檔中](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)的指南

### <a name="how-automatic-dependency-monitoring-works"></a>自動依賴項監控的工作原理是什麼？

使用以下技術之一自動收集依賴項：

* 在選擇方法周圍使用位元組代碼檢測。 （從狀態監視器或 Azure Web 應用擴展進行檢測引擎）
* 事件源回檔
* 診斷源回檔（在最新的 .NET/.NET 核心 SDK 中）

## <a name="manually-tracking-dependencies"></a>手動跟蹤依賴項

以下是一些依賴項示例，這些依賴項不會自動收集，因此需要手動跟蹤。

* 只有在使用 [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) 的情況下，才會自動追蹤 Azure Cosmos DB。 Application Insights 不會擷取 TCP 模式。
* Redis

對於 SDK 未自動收集的這些依賴項，您可以使用標準自動收集模組使用的[Track 依賴項 API](api-custom-events-metrics.md#trackdependency)手動跟蹤它們。

例如，如果您建置程式碼的組件不是您自己撰寫的，您可以計算對組件的所有呼叫，以找出它佔回應時間的比例。 若要在 Application Insights 中的相依性圖表中顯示此資料，請使用 `TrackDependency`傳送。

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

或者，`TelemetryClient`提供擴充方法`StartOperation`，`StopOperation`並可用於手動跟蹤依賴項，[如下所示](custom-operations-tracking.md#outgoing-dependencies-tracking)

如果要關閉標準依賴項跟蹤模組，請刪除[應用程式 Insights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)中對依賴項跟蹤遙測模組的引用，ASP.NET應用程式。 對於ASP.NET核心應用程式，請[按照此處](asp-net-core.md#configuring-or-removing-default-telemetrymodules)的說明操作。

## <a name="tracking-ajax-calls-from-web-pages"></a>跟蹤來自網頁的AJAX呼叫

對於網頁，應用程式見解 JavaScript SDK 會自動收集 AJAX 調用作為依賴項。

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>高級 SQL 追蹤，獲取完整的 SQL 查詢

對於 SQL 調用，伺服器和資料庫的名稱始終作為收集的`DependencyTelemetry`的名稱收集和存儲。 還有一個稱為"資料"的附加欄位，它可以包含完整的 SQL 查詢文本。

對於ASP.NET核心應用程式，無需執行其他步驟即可獲取完整的 SQL 查詢。

對於ASP.NET應用程式，使用位元組代碼檢測收集完整的 SQL 查詢，這需要檢測引擎。 需要其他特定于平臺的步驟，如下所述。

| Platform | 獲取完整 SQL 查詢所需的步驟 |
| --- | --- |
| Azure Web 應用程式 |在 Web 應用控制台中，[打開應用程式見解邊欄](../../azure-monitor/app/azure-web-apps.md)，並在 .NET 下啟用 SQL 命令 |
| IIS 伺服器（Azure VM、上部伺服器等）。 | 使用狀態監視器 PowerShell 模組[安裝檢測引擎](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md)並重新啟動 IIS。 |
| Azure 雲端服務 | 添加[啟動任務以安裝狀態監視器](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> 應用應在生成時通過為[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)或[ASP.NET核心應用程式](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)安裝 NuGet 包，在生成時將其裝機到應用程式 Insights SDK |
| IIS Express | 不支援

在上述情況下，驗證儀器引擎是否正確安裝的正確方法是驗證收集的`DependencyTelemetry`SDK 版本是"rddp"。 "rdddsd"或"rddf"表示依賴項是通過診斷源或事件源回檔收集的，因此不會捕獲完整的 SQL 查詢。

## <a name="where-to-find-dependency-data"></a>哪裡可以找到相依性資料

* [應用程式對應](app-map.md)會以視覺化方式顯示您應用程式與相鄰元件之間的相依性。
* [事務診斷](transaction-diagnostics.md)顯示統一的、相關的伺服器資料。
* [瀏覽器選項卡](javascript.md)顯示來自使用者瀏覽器的 AJAX 呼叫。
* 從速度緩慢或失敗的要求逐一點選以檢查其相依性呼叫。
* [分析](#logs-analytics)可用來查詢相依性資料。

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> 診斷速度緩慢的要求

每個請求事件都與應用處理請求時跟蹤的依賴項調用、異常和其他事件相關聯。 因此，如果某些請求表現不佳，您可以找出這是否是由於依賴項回應緩慢。

### <a name="tracing-from-requests-to-dependencies"></a>進行從要求到相依性的追蹤

打開 **"性能"** 選項卡，然後導航到操作旁邊的頂部的 **"依賴項**"選項卡。

按一下總體下**的依賴項名稱**。 選擇依賴項後，該依賴項的持續時間分布圖將顯示在右側。

![在"性能"選項卡中，按一下頂部的"依賴項"選項卡，然後按一下圖表中的"依賴項"名稱](./media/asp-net-dependencies/2-perf-dependencies.png)

按一下右下角的藍色 **"示例"** 按鈕，然後按一下示例以查看端到端交易記錄詳細資訊。

![按一下示例以查看端到端事務詳細資訊](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>剖析您的即時網站

不清楚時間花在哪裡嗎？ [應用程式見解探測器](../../azure-monitor/app/profiler.md)跟蹤對即時網站的 HTTP 調用，並顯示代碼中耗時最長的函數。

## <a name="failed-requests"></a>失敗的要求

失敗的要求可能也會與失敗的相依性呼叫關聯。

我們可以轉到左側的 **"失敗"** 選項卡，然後按一下頂部的依賴**項**選項卡。

![按一下失敗要求的圖表](./media/asp-net-dependencies/4-fail.png)

在這裡，您可以看到失敗的依賴項計數。 獲取有關嘗試按一下底部表中的依賴項名稱失敗事件的詳細資訊。 您可以按一下右下角的藍色**依賴項**按鈕，獲取端到端事務詳細資訊。

## <a name="logs-analytics"></a>記錄 (分析)

您可以在 [Kusto 查詢語言](/azure/kusto/query/)中追蹤相依性。 以下是一些範例。

* 尋找任何失敗的相依性呼叫：

``` Kusto

    dependencies | where success != "True" | take 10
```

* 尋找 AJAX 呼叫︰

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* 尋找與要求關聯的相依性呼叫：

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* 尋找與頁面檢視關聯的 AJAX 呼叫：

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*自動依賴項收集器如何報告對依賴項的失敗調用？*

* 失敗的依賴項調用將"成功"欄位設置為 False。 `DependencyTrackingTelemetryModule`不報告`ExceptionTelemetry`。 [此處](data-model-dependency-telemetry.md)介紹了依賴項的完整資料模型。

## <a name="open-source-sdk"></a>開放原始碼 SDK
與每個應用程式見解 SDK 一樣，依賴項收集模組也是開源的。 閱讀並貢獻代碼，或在[官方的 GitHub 存儲庫](https://github.com/Microsoft/ApplicationInsights-dotnet-server)中報告問題。

## <a name="next-steps"></a>後續步驟

* [異常](../../azure-monitor/app/asp-net-exceptions.md)
* [使用者和頁面資料](../../azure-monitor/app/javascript.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
