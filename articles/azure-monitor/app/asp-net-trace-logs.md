---
title: 在 Application Insights 中探索 .NET 追蹤記錄
description: 搜尋 Trace、NLog 或 Log4Net 產生的記錄。
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: d010fe4389e22c9909800f5329911b6b5619d7b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829528"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>在 Application Insights 中探索 .NET/.NET Core 和 Python 追蹤記錄

將 ASP.NET/ASP.NET Core 應用程式的診斷追蹤記錄從 ILogger、NLog、log4Net 或 System.Diagnostics.Trace 傳送至 [Azure Application Insights][start]。 若為 Python 應用程式，請在 OpenCensus Python for Azure Monitor 中使用 AzureLogHandler 傳送診斷追蹤記錄。 然後，您可以探索並搜尋這些記錄。 這些記錄會與來自應用程式的其他記錄檔合併，讓您可以識別與每個使用者要求相關聯的追蹤，並將這些追蹤與其他事件和例外狀況報告相互關聯。

> [!NOTE]
> 您需要記錄擷取模組嗎？ 對於第三方記錄器來說，其是一個有用的配接器。 但是，如果您還沒使用 NLog、log4Net 或 System.Diagnostics.Trace，請考慮直接呼叫 [**Application Insights TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)。
>
>
## <a name="install-logging-on-your-app"></a>在您的 app 上安裝記錄
在您的專案中安裝您選擇的記錄架構，這應該會在 app.config 或 web.config 中產生項目。

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>設定 Application Insights 收集記錄
如果您尚未這麼做，請[將 Application Insights 新增至您的專案](../../azure-monitor/app/asp-net.md)。 您將會看見包含記錄收集器的選項。

或者，在 [方案總管] 中以滑鼠右鍵按一下您的專案，來**設定 Application Insights**。 選取 [設定追蹤集合] 選項。

> [!NOTE]
> 沒有 Application Insights 功能表或記錄收集器選項嗎？ 請嘗試進行[疑難排解](#troubleshooting)。

## <a name="manual-installation"></a>手動安裝
如果 Application Insights 安裝程式不支援您的專案類型 (例如 Windows 傳統型專案)，請使用這個方法。

1. 如果您打算使用 log4Net 或 NLog，請將它安裝在您的專案。
2. 在 [方案總管] 中，以滑鼠右鍵按一下您的專案，然後選取 [管理 NuGet 套件]。
3. 搜尋 "Application Insights"。
4. 選取下列其中一個套件：

   - 針對 ILogger：[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - 針對 NLog：[Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - 針對 Log4Net：[Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - 針對 System.Diagnostics：[Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet 套件會安裝必要的組件，並在適用的情況下修改 web.config 或 app.config。

## <a name="ilogger"></a>ILogger

如需使用 Application Insights ILogger 實作搭配主控台應用程式和 ASP.NET Core 的範例，請參閱 [ApplicationInsightsLoggerProvider for .NET Core ILogger 記錄](ilogger.md)。

## <a name="insert-diagnostic-log-calls"></a>插入診斷記錄呼叫
如果您使用 System.Diagnostics.Trace，典型的呼叫如下：

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

如果您偏好 log4net 或 NLog，請使用：

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>使用 EventSource 事件
您可以將 [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 設定為要傳送至 Application Insights 作為追蹤的事件。 首先，安裝 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 套件。 然後編輯 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 檔案的 `TelemetryModules` 區段。

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

針對每個來源，您可以設定下列參數︰
 * **Name** 指定要收集的 EventSource 名稱。
 * **Level** 指定要收集的記錄層級：*Critical*、*Error*、*Informational*、*LogAlways*、*Verbose* 或 *Warning*。
 * **Keywords** (選擇性) 指定要使用的關鍵字組合整數值。

## <a name="use-diagnosticsource-events"></a>使用 DiagnosticSource 事件
您可以將 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 設定為要傳送至 Application Insights 作為追蹤的事件。 首先，安裝 [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 套件。 然後編輯 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 檔案的 "TelemetryModules" 區段。

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

針對您想要追蹤的每個 DiagnosticSource，新增項目並將其 **Name** 屬性設定為 DiagnosticSource 的名稱。

## <a name="use-etw-events"></a>使用 ETW 事件
您可以設定要傳送至 Application Insights 作為追蹤的 Windows 事件追蹤 (ETW) 事件。 首先，安裝 `Microsoft.ApplicationInsights.EtwCollector` NuGet 套件。 然後編輯 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 檔案的 "TelemetryModules" 區段。

> [!NOTE] 
> 僅在裝載 SDK 的處理序是以效能記錄使用者或系統管理員成員的身分識別執行時，才可收集 ETW 事件。

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

針對每個來源，您可以設定下列參數︰
 * **ProviderName** 是要收集的 ETW 提供者名稱。
 * **ProviderGuid** 指定要收集的 ETW 提供者 GUID。 可以使用此 GUID 代替 `ProviderName`。
 * **Level** 設定要收集的記錄層級。 其可以是 *Critical*、*Error*、*Informational*、*LogAlways*、*Verbose* 或 *Warning*。
 * **Keywords** (選擇性) 設定要使用的關鍵字組合整數值。

## <a name="use-the-trace-api-directly"></a>直接使用追蹤 API
您可以直接呼叫 Application Insights 追蹤 API。 記錄配接器會使用此 API。

例如：

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow response - database01");
```

TrackTrace 的優點在於您可以將較長的資料放在訊息中。 例如，您可以在該處編碼 POST 資料。

您也可以將嚴重性層級新增至訊息。 就像其他遙測一樣，您可以新增屬性值，以協助篩選或搜尋不同的追蹤集。 例如：

  ```csharp
  var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
  telemetry.TrackTrace("Slow database response",
                 SeverityLevel.Warning,
                 new Dictionary<string,string> { {"database", db.ID} });
  ```

這可讓您在[搜尋][diagnostic]中輕鬆地篩選出與特定資料庫相關且具有特定嚴重性層級的所有訊息。

## <a name="azureloghandler-for-opencensus-python"></a>適用於 OpenCensus Python 的 AzureLogHandler
Azure 監視器記錄處理常式可讓您將 Python 記錄匯出至 Azure 監視器。

使用適用於 Azure 監視器的 [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) 檢測您的應用程式。

這個範例示範如何將警告層級的記錄檔傳送至 Azure 監視器。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>探索記錄
在偵錯模式中執行應用程式或即時部署應用程式。

在 [Application Insights 入口網站][portal]的應用程式概觀窗格中，選取 [[搜尋]][diagnostic]。

例如，您可以：

* 篩選記錄追蹤，或具有特定屬性的項目。
* 詳細檢查特定項目。
* 尋找與相同使用者要求 (具有相同的 OperationId) 相關的其他系統記錄檔資料。
* 將頁面的組態儲存為我的最愛。

> [!NOTE]
>如果您的應用程式傳送大量資料，且您是使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，則調*適性取樣功能*可能會運作，並只傳送一部分的遙測資料。 [深入了解取樣。](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>疑難排解
### <a name="how-do-i-do-this-for-java"></a>如果是 Java，我要怎麼做？
在收集現成記錄的 Java 無程式碼檢測 (建議動作) 中，請使用 [Java 3.0 代理程式](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)。

如果您使用 Java SDK，請使用 [Java 記錄配接器](../../azure-monitor/app/java-trace-logs.md)。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>專案內容功能表上沒有 Application Insights 選項
* 確定已在開發電腦上安裝 Developer Analytics Tools。 在 Visual Studio 的 [工具] > [擴充功能和更新] 中，尋找 [Developer Analytics Tools]。 如果其不在 [已安裝] 索引標籤上，請開啟 [線上] 索引標籤並加以安裝。
* 這可能是 Developer Analytics Tools 不支援的專案類型。 請使用 [手動安裝](#manual-installation)。

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>組態工具中沒有記錄配接器選項
* 請先安裝記錄架構。
* 如果您使用 System.Diagnostics.Trace，請確定已將其[設定在 *web.config* 中](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)。
* 確定您有最新版的 Application Insights。 在 Visual Studio 中，移至 [工具] > [擴充功能和更新]，然後開啟 [更新] 索引標籤。如果發現 **Developer Analytics Tools**，請選取以進行更新。

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>我收到「檢測金鑰不能是空白」的錯誤訊息
您可能已安裝記錄配接器 Nuget 套件，但未安裝 Application Insights。 在 [方案總管] 中，以滑鼠右鍵按一下 *ApplicationInsights.config*，然後選取 [更新 Application Insights]。 這時會提示您登入 Azure，並建立 Application Insights 資源或重複使用現有的資源。 這應該可修正問題。

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>我可以在診斷搜尋中看見追蹤，但是看不到其他事件
可能需要一段時間，所有事件和要求才會通過管線。

### <a name="how-much-data-is-retained"></a><a name="limits"></a>保留多少資料？
有好幾個因素會影響保留的資料量。 如需詳細資訊，請參閱客戶事件計量頁面的[限制](../../azure-monitor/app/api-custom-events-metrics.md#limits)區段。

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>我看不到我預期的一些記錄項目
如果您的應用程式傳送大量資料，且您是使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，則調適性取樣功能可能會運作，並只傳送一部分的遙測資料。 [深入了解取樣。](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>後續步驟

* [在 ASP.NET 中診斷失敗和例外狀況][exceptions]
* [深入了解搜尋][diagnostic]
* [設定可用性和回應性測試][availability]
* [疑難排解][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
