---
title: Azure Application Insights 中的相依性追蹤 | Microsoft Docs
description: 使用 Application Insights 監視來自內部部署或 Microsoft Azure web 應用程式的相依性呼叫。
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: a7f42c19c835e4f5c49f4d7aa91504b606a09f5b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321372"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Azure 應用程式深入解析中的相依性追蹤 

「相依性」（ *dependency* ）是指應用程式所呼叫的元件。 這通常是使用 HTTP 呼叫的服務，或資料庫，或檔案系統。 [Application Insights](./app-insights-overview.md)會測量相依性呼叫的持續時間，不論其是否失敗，以及其他資訊（例如相依性的名稱等等）。 您可以調查特定的相依性呼叫，並將它們與要求和例外狀況相互關聯。

## <a name="automatically-tracked-dependencies"></a>自動追蹤的相依性

適用于 .NET 和 .NET Core 的 Application Insights Sdk 隨附于， `DependencyTrackingTelemetryModule` 其為自動收集相依性的遙測模組。 根據連結的官方檔設定時，會自動為[ASP.NET](./asp-net.md)和[ASP.NET Core](./asp-net-core.md)應用程式啟用此相依性集合。`DependencyTrackingTelemetryModule`隨附為[此](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)nuget 套件，並會在使用其中一個 nuget 套件或時自動帶入 `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.AspNetCore` 。

 `DependencyTrackingTelemetryModule`目前會自動追蹤下列相依性：

|相依性 |詳細資料|
|---------------|-------|
|Http/Https | 本機或遠端 HTTP/HTTPs 呼叫 |
|WCF 呼叫| 只有在使用以 Http 為基礎的系結時，才會自動追蹤。|
|SQL | 以進行的呼叫 `SqlClient` 。 如需瞭解 SQL 查詢，請參閱[此](#advanced-sql-tracking-to-get-full-sql-query)程式。  |
|[Azure 儲存體（Blob、資料表、佇列）](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure 儲存體用戶端所提出的呼叫。 |
|[EventHub 用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 和更新版本。 |
|[ServiceBus 用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3.0.0 和更新版本。 |
|Azure Cosmos DB | 只有在使用 HTTP/HTTPS 時才會自動追蹤。 Application Insights 不會擷取 TCP 模式。 |

如果您遺失相依性，或使用不同的 SDK，請確定它是在[自動收集](./auto-collect-dependencies.md)的相依性清單中。 如果不會自動收集相依性，您仍然可以使用追蹤相依性[呼叫](./api-custom-events-metrics.md#trackdependency)來手動進行追蹤。

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>在主控台應用程式中設定自動相依性追蹤

若要從 .NET 主控台應用程式自動追蹤相依性，請安裝 Nuget 套件 `Microsoft.ApplicationInsights.DependencyCollector` ，並將其初始化， `DependencyTrackingTelemetryModule` 如下所示：

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

針對 .NET Core 主控台應用程式 TelemetryConfiguration。 Active 已過時。 請參閱背景[工作服務檔](./worker-service.md)中的指引和[ASP.NET Core 監視檔](./asp-net-core.md)

### <a name="how-automatic-dependency-monitoring-works"></a>自動相依性監視如何運作？

相依性會使用下列其中一項技術自動收集：

* 針對 select 方法使用位元組程式碼檢測。 （從 StatusMonitor 或 Azure Web 應用程式擴充功能 InstrumentationEngine）
* EventSource 回呼
* DiagnosticSource 回呼（在最新的 .NET/.NET Core Sdk 中）

## <a name="manually-tracking-dependencies"></a>手動追蹤相依性

以下是一些不會自動收集的相依性範例，因此需要手動追蹤。

* 只有在使用 [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) 的情況下，才會自動追蹤 Azure Cosmos DB。 Application Insights 不會擷取 TCP 模式。
* Redis

針對不是由 SDK 自動收集的相依性，您可以使用標準自動收集模組所使用的[TRACKDEPENDENCY API](api-custom-events-metrics.md#trackdependency) ，以手動方式加以追蹤。

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

或者， `TelemetryClient` 提供擴充方法， `StartOperation` 並 `StopOperation` 可用於手動追蹤相依性，如下所示[here](custom-operations-tracking.md#outgoing-dependencies-tracking)

如果您想要關閉標準的相依性追蹤模組，請在 [針對 ASP.NET 應用程式的[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)中移除對 applicationinsights.config dependencytrackingtelemetrymodule 的參考。 如需 ASP.NET Core 應用程式，請遵循[這裡](asp-net-core.md#configuring-or-removing-default-telemetrymodules)的指示。

## <a name="tracking-ajax-calls-from-web-pages"></a>從網頁追蹤 AJAX 呼叫

若為網頁，Application Insights JavaScript SDK 會自動將 AJAX 呼叫收集為相依性。

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>先進的 SQL 追蹤以取得完整的 SQL 查詢

針對 SQL 呼叫，一律會收集伺服器和資料庫的名稱，並將其儲存為所收集之的名稱 `DependencyTelemetry` 。 還有一個稱為「資料」的額外欄位，它可以包含完整的 SQL 查詢文字。

針對 ASP.NET Core 的應用程式，現在需要使用來加入宣告 SQL 文字集合
```csharp
services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) => { module. EnableSqlCommandTextInstrumentation = true; });
```

對於 ASP.NET 應用程式，完整的 SQL 查詢文字會透過 byte 程式碼檢測的協助收集，這需要使用檢測引擎或使用[SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet 套件，而不是 SqlClient 程式庫。 啟用完整 SQL 查詢集合的平臺特定步驟如下所述：

| 平台 | 取得完整 SQL 查詢所需的步驟 |
| --- | --- |
| Azure Web 應用程式 |在您的 web 應用程式控制台中，[開啟 [Application Insights](../../azure-monitor/app/azure-web-apps.md) ] 分頁，並在 .net 底下啟用 SQL 命令 |
| IIS 伺服器（Azure VM、內部內部部署等等）。 | 請使用[SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet 套件，或使用狀態監視器 PowerShell 模組來[安裝檢測引擎](../../azure-monitor/app/status-monitor-v2-api-reference.md)並重新啟動 IIS。 |
| Azure 雲端服務 | 新增[啟動工作以安裝 StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> 您的應用程式應該在組建階段上架至 ApplicationInsights SDK，方法是安裝[ASP.NET](./asp-net.md)或[ASP.NET Core 應用程式](./asp-net-core.md)的 NuGet 套件 |
| IIS Express | 使用[SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet 套件。

除了上述平臺特定步驟之外，您還必須使用下列內容修改 applicationInsights.config 檔案，**以明確選擇啟用 SQL 命令集合**：

```xml
<Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
<EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
</Add>
```

在上述情況下，驗證所收集的 SDK 版本 `DependencyTelemetry` 是 ' rddp '，正確地驗證檢測引擎已正確安裝。 ' rdddsd ' 或 ' rddf ' 表示相依性是透過 DiagnosticSource 或 EventSource 回呼來收集，因此不會捕捉完整的 SQL 查詢。

## <a name="where-to-find-dependency-data"></a>哪裡可以找到相依性資料

* [應用程式對應](app-map.md)會以視覺化方式顯示您應用程式與相鄰元件之間的相依性。
* [交易診斷](transaction-diagnostics.md)會顯示統一、相互關聯的伺服器資料。
* [[瀏覽器]](javascript.md)索引標籤會顯示來自您使用者瀏覽器的 AJAX 呼叫。
* 從速度緩慢或失敗的要求逐一點選以檢查其相依性呼叫。
* [分析](#logs-analytics)可用來查詢相依性資料。

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> 診斷速度緩慢的要求

每個要求事件都會與您的應用程式處理要求時所追蹤的相依性呼叫、例外狀況和其他事件相關聯。 因此，如果某些要求的效能不佳，您可以找出這是因為相依性的回應緩慢。

### <a name="tracing-from-requests-to-dependencies"></a>進行從要求到相依性的追蹤

開啟 [**效能**] 索引標籤，並流覽至 [作業] 旁頂端**的 [相依**性] 索引標籤。

按一下 [整體] 底下的相依性**名稱**。 選取相依性之後，該相依性的持續時間分布圖表就會顯示在右邊。

![在 [效能] 索引標籤中，按一下頂端的 [相依性] 索引標籤，然後按圖表中的相依性名稱](./media/asp-net-dependencies/2-perf-dependencies.png)

按一下右下方的藍色 [**範例**] 按鈕，然後在範例上查看端對端交易詳細資料。

![按一下範例以查看端對端交易詳細資料](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>剖析您的即時網站

不清楚時間花在哪裡嗎？ [Application Insights](../../azure-monitor/app/profiler.md)分析工具會追蹤對您的即時網站發出的 HTTP 呼叫，並向您顯示程式碼中花費最長時間的函式。

## <a name="failed-requests"></a>失敗的要求

失敗的要求可能也會與失敗的相依性呼叫關聯。

我們可以移至左側的 [**失敗**] 索引標籤，然後按一下頂端的 [相依**性] 索引**標籤。

![按一下失敗要求的圖表](./media/asp-net-dependencies/4-fail.png)

在這裡，您將能夠看到失敗的相依性計數。 若要取得有關失敗發生的詳細資料，請嘗試按一下底端資料表中的相依性名稱。 您可以按一下右下方的藍色 [相依性 **]** 按鈕，以取得端對端交易詳細資料。

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

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*自動相依性收集器如何報告對相依性的呼叫失敗？*

* 失敗的相依性呼叫會將「成功」欄位設定為 False。 `DependencyTrackingTelemetryModule`不會報告 `ExceptionTelemetry` 。 [此處](data-model-dependency-telemetry.md)會說明相依性的完整資料模型。

## <a name="open-source-sdk"></a>開放原始碼 SDK
就像每個 Application Insights SDK，相依性集合模組也是開放原始碼。 閱讀並貢獻程式碼，或報告[官方 GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server)存放庫的問題。

## <a name="next-steps"></a>後續步驟

* [例外狀況](./asp-net-exceptions.md)
* [使用者和頁面資料](./javascript.md)
* [可用性](./monitor-web-app-availability.md)

