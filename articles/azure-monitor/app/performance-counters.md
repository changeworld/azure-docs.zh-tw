---
title: Application Insights 中的效能計數器 | Microsoft Docs
description: 監視 Application Insights 中的系統和自訂 .NET 效能計數器。
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: eb5e20403cc826619eb1f67de2fc4179e17b5aa4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322511"
---
# <a name="system-performance-counters-in-application-insights"></a>Application Insights 中的系統效能計數器

Windows 提供多種[效能計數器](/windows/desktop/perfctrs/about-performance-counters) (例如 CPU 使用、記憶體、磁碟和網路使用量)。 您也可以定義自己的效能計數器。 只要應用程式是在您具有系統管理存取權的內部部署主機或虛擬機器上於 IIS 下執行，則支援效能計數器集合。 雖然以 Azure Web Apps 執行的應用程式無法直接存取效能計數器，但 Application Insights 會收集可用計數器的子集。

## <a name="view-counters"></a>檢視計數器

[計量] 窗格會顯示一組預設的效能計數器。

![Application Insights 中所報告的效能計數器](./media/performance-counters/performance-counters.png)

目前設定為針對 ASP.NET/ASP.NET Core web 應用程式收集的預設計數器為：
- % Process\\Processor Time
- % Process\\Processor Time Normalized
- Memory\\Available Bytes
- ASP.NET 要求/秒
- 擲回的 .NET CLR 例外狀況數/秒
- ASP.NET ApplicationsRequest 執行時間
- Process\\Private Bytes
- Process\\IO Data Bytes/sec
- ASP.NET 應用程式\\應用程式佇列中要求數
- Processor(_Total)\\% Processor Time

## <a name="add-counters"></a>新增計數器

如果計量清單中未包含您想要的效能計數器，您可以新增該計數器。

1. 在本機伺服器上使用以下 PowerShell 命令，以找出伺服器中可用的計數器：

    `Get-Counter -ListSet *`

    (請參閱 [`Get-Counter`](/powershell/module/microsoft.powershell.diagnostics/get-counter?view=powershell-5.1)。)
2. 開啟 ApplicationInsights.config。

   * 如果您已在開發期間將 Application Insights 新增至應用程式，請編輯專案中的 ApplicationInsights.config，然後將它重新部署至伺服器。
3. 編輯效能收集器指示詞：

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET Core 的應用程式沒有 `ApplicationInsights.config`，因此上述方法對 ASP.NET Core 應用程式而言不正確。

您可以擷取標準計數器，也可以擷取您自己實作的計數器。 `\Objects\Processes` 是所有 Windows 系統上都提供的一個標準計數器範例。 `\Sales(photo)\# Items Sold` 是可能在 Web 服務中實作的自訂計數器範例。

格式為 `\Category(instance)\Counter"`，若是沒有執行個體的類別，則為 `\Category\Counter`。

不符合 `[a-zA-Z()/-_ \.]+` 的計數器名稱需要有 `ReportAs`；亦即，它們包含不在下列集合中的字元：字母、圓括號、正斜線、連字號、底線、空格、點。

如果您指定執行個體，系統會收集它做為報告度量的 "CounterInstanceName" 維度。

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>在程式碼中收集 ASP.NET Web 應用程式或 .NET/.NET Core 主控台應用程式的效能計數器
若要收集系統效能計數器並將其傳送至 Application Insights，可以採用下列程式碼片段：


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

或者，您可以透過您建立的自訂度量執行相同的作業︰

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>在程式碼中收集 ASP.NET Core Web 應用程式的效能計數器

修改 `Startup.cs` 類別中的 `ConfigureServices` 方法，如下所示。

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Analytics 中的效能計數器
您可以搜尋並顯示 [Analytics](../log-query/log-query-overview.md) 中的效能計數器報告。

**PerformanceCounters** 結構描述會公開每個效能計數器的 `category`、`counter` 名稱和 `instance` 名稱。  在每個應用程式的遙測中，您將只會看到該應用程式的計數器。 例如，若要查看哪些計數器可用︰ 

![Application Insights 分析中的效能計數器](./media/performance-counters/analytics-performance-counters.png)

(這裡的 'Instance' 係指效能計數器執行個體，而不是角色或伺服器機器執行個體。 效能計數器執行個體名稱一般會將計數器分段，例如依處理序或應用程式名稱的處理器時間。)

若要取得可用記憶體在最近一段時間的圖表︰ 

![Application Insights 分析中的記憶體時間圖表](./media/performance-counters/analytics-available-memory.png)

與其他遙測一樣，**performanceCounters** 也有 `cloud_RoleInstance` 資料行可指出應用程式執行所在主機伺服器執行個體的身分識別。 例如，若要比較不同機器上的應用程式效能︰ 

![Application Insights 分析中依角色執行個體分割的效能](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET 和 Application Insights 計數

*例外狀況率和例外狀況計量之間的差異為何？*

*  是系統效能計數器。 CLR 會計算所有擲回之已處理和未處理的例外狀況，並依據間隔的長度將總數分割為取樣間隔。 Application Insights SDK 會收集此結果並將它傳送至入口網站。

*  是在圖表的取樣間隔中由入口網站接收之 TrackException 報告的計數。 它只包含您程式碼中撰寫 TrackException 呼叫所在位置的已處理例外狀況，並且不包含所有的 [未處理例外狀況](./asp-net-exceptions.md)。 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>在 Azure Web Apps 中執行之應用程式的效能計數器

部署至 Azure Web Apps 的 ASP.NET 和 ASP.NET Core 應用程式都是在特殊的沙箱環境中執行。 此環境不允許直接存取系統效能計數器。 不過，有限的計數器子集會公開為環境變數，如[這裡](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables)所述。 適用於 ASP.NET 和 ASP.NET Core 的 Application Insights SDK 會從這些特殊環境變數中的 Azure Web Apps 收集效能計數器。 只有計數器的子集可在此環境中使用，如需完整清單請至[此處](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)。

## <a name="performance-counters-in-aspnet-core-applications"></a>ASP.NET Core 應用程式中的效能計數器

ASP.NET Core 效能計數器的支援受限制：

* 如果應用程式是在 Azure Web Apps (Windows) 中執行，[SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 版本2.4.1 和以後版本會收集效能計數器。
* 如果應用程式是在 Windows 中執行，而且目標是 `NETSTANDARD2.0` 或以後版本，SDK 版本2.7.1 和更新版本會收集效能計數器。
* 對於以 .NET Framework 為目標的應用程式，所有版本的 SDK 都支援效能計數器。
* SDK 2.8.0 和更新版本支援 Linux 中的 cpu/記憶體計數器。 Linux 不支援其他計數器。 在 Linux (和其他非 Windows 環境) 中取得系統計數器的建議方式是使用 [EventCounters](eventcounters.md)

## <a name="alerts"></a>警示
與其他計量一樣，您可以[設定警示](../platform/alerts-log.md)，在效能計數器超出您指定的界限時提出警告。 開啟 [警示] 窗格，然後按一下 [新增警示]。

## <a name="next-steps"></a><a name="next"></a>後續步驟

* [相依性追蹤](./asp-net-dependencies.md)
* [例外狀況追蹤](./asp-net-exceptions.md)

