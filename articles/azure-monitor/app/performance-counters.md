---
title: Application Insights 中的效能計數器 | Microsoft Docs
description: 監視 Application Insights 中的系統和自訂 .NET 效能計數器。
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: 94d2520c17867f6d70caffd002a76365a425986f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669874"
---
# <a name="system-performance-counters-in-application-insights"></a>Application Insights 中的系統效能計數器

Windows 提供多種[效能計數器](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) (例如 CPU 使用、記憶體、磁碟和網路使用量)。 您也可以定義自己的效能計數器。 只要應用程式在本地主機或具有管理存取權限的虛擬機器的 IIS 下運行，效能計數器集合即受支援。 儘管作為 Azure Web 應用運行的應用程式不能直接存取效能計數器，但應用程式見解會收集可用計數器的子集。

## <a name="view-counters"></a>檢視計數器

[計量] 窗格會顯示一組預設的效能計數器。

![Application Insights 中所報告的效能計數器](./media/performance-counters/performance-counters.png)

配置為為 ASP.NET/ASP.NET 核心 Web 應用程式收集的當前預設計數器是：
- 處理\\處理器時間百分比
- 進程\\處理器時間標準化百分比
- 記憶體\\可用位元組
- ASP.NET請求/秒
- .NET CLR 異常引發/秒
- ASP.NET應用程式請求執行時間
- 處理\\專用位元組
- 處理\\IO 資料位元組/秒
- 應用程式佇列\\中的ASP.NET應用程式請求
- 處理器（_Total）\\% 處理器時間

## <a name="add-counters"></a>新增計數器

如果計量清單中未包含您想要的效能計數器，您可以新增該計數器。

1. 在本機伺服器上使用以下 PowerShell 命令，以找出伺服器中可用的計數器：

    `Get-Counter -ListSet *`

    （參見[`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).）
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
> ASP.NET核心應用程式沒有`ApplicationInsights.config`，因此上述方法對 ASP.NET 核心應用程式無效。

您可以擷取標準計數器，也可以擷取您自己實作的計數器。 `\Objects\Processes` 是所有 Windows 系統上都提供的一個標準計數器範例。 `\Sales(photo)\# Items Sold` 是可能在 Web 服務中實作的自訂計數器範例。

格式為 `\Category(instance)\Counter"`，若是沒有執行個體的類別，則為 `\Category\Counter`。

不符合 `[a-zA-Z()/-_ \.]+` 的計數器名稱需要有 `ReportAs`；亦即，它們包含不在下列集合中的字元：字母、圓括號、正斜線、連字號、底線、空格、點。

如果您指定執行個體，系統會收集它做為報告度量的 "CounterInstanceName" 維度。

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>在代碼中收集效能計數器，以用於ASP.NET Web 應用程式或 .NET/.NET 核心主控台應用程式
若要收集系統效能計數器並將其傳送至 Application Insights，可以採用下列程式碼片段：


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

或者，您可以透過您建立的自訂度量執行相同的作業︰

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>在代碼中收集效能計數器，以ASP.NET核心 Web 應用程式

修改`ConfigureServices``Startup.cs`類中的方法，如下所示。

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
您可以搜尋並顯示 [Analytics](../../azure-monitor/app/analytics.md) 中的效能計數器報告。

**效能計數器**架構公開每個效能計數器的`category` `counter` 、 名稱`instance`和名稱。  在每個應用程式的遙測中，您將只會看到該應用程式的計數器。 例如，若要查看哪些計數器可用︰ 

![Application Insights 分析中的效能計數器](./media/performance-counters/analytics-performance-counters.png)

(這裡的 'Instance' 係指效能計數器執行個體，而不是角色或伺服器機器執行個體。 效能計數器執行個體名稱一般會將計數器分段，例如依處理序或應用程式名稱的處理器時間。)

若要取得可用記憶體在最近一段時間的圖表︰ 

![Application Insights 分析中的記憶體時間圖表](./media/performance-counters/analytics-available-memory.png)

與其他遙測一樣，**performanceCounters** 也有 `cloud_RoleInstance` 資料行可指出應用程式執行所在主機伺服器執行個體的身分識別。 例如，若要比較不同機器上的應用程式效能︰ 

![Application Insights 分析中依角色執行個體分割的效能](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>ASP.NET 和 Application Insights 計數

*例外狀況率和例外狀況計量之間的差異為何？*

* ** 是系統效能計數器。 CLR 會計算所有擲回之已處理和未處理的例外狀況，並依據間隔的長度將總數分割為取樣間隔。 Application Insights SDK 會收集此結果並將它傳送至入口網站。

* ** 是在圖表的取樣間隔中由入口網站接收之 TrackException 報告的計數。 它只包含您程式碼中撰寫 TrackException 呼叫所在位置的已處理例外狀況，並且不包含所有的 [未處理例外狀況](../../azure-monitor/app/asp-net-exceptions.md)。 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>在 Azure Web 應用中運行的應用程式的效能計數器

部署到 Azure Web 應用ASP.NET和ASP.NET核心應用程式都運行在特殊的沙箱環境中。 此環境不允許直接存取系統效能計數器。 但是，[此處所述，](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables)計數器的有限子集將作為環境變數公開。 用於ASP.NET和 ASP.NET核心的應用程式見解 SDK 從這些特殊的環境變數收集 Azure Web 應用的效能計數器。 此環境中只有計數器子集可用，因此可以在此處找到完整清單[。](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>ASP.NET核心應用程式中的效能計數器

對ASP.NET核心中的效能計數器的支援是有限的：

* 如果應用程式在 Azure Web 應用 （Windows） 中運行[，SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.4.1 和更高版本將收集效能計數器。
* 如果應用程式在 Windows 和目標中運行`NETSTANDARD2.0`，SDK 版本 2.7.1 和更高版本將收集效能計數器。
* 對於面向 .NET 框架的應用程式，SDK 的所有版本都支援效能計數器。
* SDK 版本 2.8.0 和更高版本支援 Linux 中的 cpu/記憶體計數器。 Linux 中不支援其他計數器。 在 Linux（和其他非 Windows 環境）中獲取系統計數器的推薦方法是使用[事件計數器](eventcounters.md)

## <a name="alerts"></a>警示
與其他計量一樣，您可以[設定警示](../../azure-monitor/app/alerts.md)，在效能計數器超出您指定的界限時提出警告。 開啟 [警示] 窗格，然後按一下 [新增警示]。

## <a name="next-steps"></a><a name="next"></a>後續步驟

* [相依性追蹤](../../azure-monitor/app/asp-net-dependencies.md)
* [例外狀況追蹤](../../azure-monitor/app/asp-net-exceptions.md)

