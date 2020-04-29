---
title: 在 Application Insights 中探索 .NET 追蹤記錄
description: 搜尋追蹤、NLog 或 Log4Net 所產生的記錄。
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276266"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>探索 Application Insights 中的 .NET/.NET Core 和 Python 追蹤記錄

將 ASP.NET/ASP.NET Core 應用程式的診斷追蹤記錄從 ILogger、NLog、log4Net 或傳送至[Azure 應用程式深入][start]解析。 針對 Python 應用程式，請在 OpenCensus Python 中使用 AzureLogHandler 傳送診斷追蹤記錄以進行 Azure 監視器。 然後您可以探索並搜尋它們。 這些記錄會與您應用程式中的其他記錄檔合併，因此您可以識別與每個使用者要求相關聯的追蹤，並將它們與其他事件和例外狀況報表相互關聯。

> [!NOTE]
> 您需要記錄檔捕捉模組嗎？ 這是適用于協力廠商記錄器的實用介面卡。 但如果您還沒有使用 NLog、log4Net 或 system.servicemodel，請考慮直接呼叫[**Application Insights TrackTrace （）**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) 。
>
>
## <a name="install-logging-on-your-app"></a>在您的 app 上安裝記錄
在您的專案中安裝您選擇的記錄架構，這應該會在 app.config 或 web.config 中產生專案。

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

或以滑鼠右鍵按一下您在方案總管中的專案，以**設定 Application Insights**。 選取 [**設定追蹤集合**] 選項。

> [!NOTE]
> 沒有 Application Insights 功能表或記錄收集器選項嗎？ 請嘗試進行[疑難排解](#troubleshooting)。

## <a name="manual-installation"></a>手動安裝
如果 Application Insights 安裝程式不支援您的專案類型 (例如 Windows 傳統型專案)，請使用這個方法。

1. 如果您打算使用 log4Net 或 NLog，請將它安裝在您的專案。
2. 在方案總管中，以滑鼠右鍵按一下您的專案，然後選取 [**管理 NuGet 套件**]。
3. 搜尋「Application Insights」。
4. 選取下列其中一個套件：

   - 針對 ILogger： [ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - 針對 NLog： [ApplicationInsights. NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - 針對 Log4Net： [ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - 針對 [ApplicationInsights]： [TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [ApplicationInsights. DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [ApplicationInsights. microsoft.applicationinsights.etwcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [ApplicationInsights. microsoft.applicationinsights.eventsourcelistener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet 套件會安裝必要的元件，並修改 web.config 或 app.config （如果適用的話）。

## <a name="ilogger"></a>ILogger

如需搭配主控台應用程式和 ASP.NET Core 使用 Application Insights ILogger 執行的範例，請參閱[ApplicationInsightsLoggerProvider for .Net Core ILogger logs](ilogger.md)。

## <a name="insert-diagnostic-log-calls"></a>插入診斷記錄呼叫
如果您使用 System.Diagnostics.Trace，典型的呼叫如下：

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

如果您偏好 log4net 或 NLog，請使用：

    logger.Warn("Slow response - database01");

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
 * **名稱**指定要收集的 EventSource 名稱。
 * **層級**會指定要收集的記錄層級： [*重大*]、[*錯誤*]、 * *[資訊]、[ *LogAlways*]、[*詳細**資訊*]
 * **關鍵字**（選擇性）指定要使用的關鍵字組合之整數值。

## <a name="use-diagnosticsource-events"></a>使用 DiagnosticSource 事件
您可以將 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 設定為要傳送至 Application Insights 作為追蹤的事件。 首先，安裝[`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 套件。 然後編輯[ApplicationInsights](../../azure-monitor/app/configuration-with-applicationinsights-config.md)的 "TelemetryModules" 區段。

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

針對您想要追蹤的每個 DiagnosticSource，新增**名稱**屬性設為 DiagnosticSource 名稱的專案。

## <a name="use-etw-events"></a>使用 ETW 事件
您可以設定 Windows 事件追蹤（ETW）事件以追蹤的形式傳送到 Application Insights。 首先，安裝 `Microsoft.ApplicationInsights.EtwCollector` NuGet 套件。 然後編輯[ApplicationInsights](../../azure-monitor/app/configuration-with-applicationinsights-config.md)的 "TelemetryModules" 區段。

> [!NOTE] 
> 只有在裝載 SDK 的進程是在「效能記錄使用者」或「系統管理員」成員的身分識別下執行時，才能收集 ETW 事件。

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

針對每個來源，您可以設定下列參數︰
 * **ProviderName**是要收集的 ETW 提供者名稱。
 * **ProviderGuid**指定要收集的 ETW 提供者 GUID。 它可以用來取代`ProviderName`。
 * **層級**會設定要收集的記錄層級。 這可能是*重要*、*錯誤*、*資訊*、 *LogAlways*、*詳細*訊息或*警告*。
 * **關鍵字**（選擇性）設定要使用的關鍵字組合之整數值。

## <a name="use-the-trace-api-directly"></a>直接使用追蹤 API
您可以直接呼叫 Application Insights 追蹤 API。 記錄配接器會使用此 API。

例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace 的優點在於您可以將較長的資料放在訊息中。 例如，您可以在該處編碼 POST 資料。

您也可以將嚴重性層級新增至您的訊息。 而和其他遙測一樣，您可以加入屬性值來協助篩選或搜尋不同的追蹤集。 例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

這可讓您在[搜尋][diagnostic]與特定資料庫相關的特定嚴重性層級的所有訊息時，輕鬆地篩選出。

## <a name="azureloghandler-for-opencensus-python"></a>適用于 OpenCensus Python 的 AzureLogHandler
Azure 監視器記錄處理常式可讓您將 Python 記錄匯出至 Azure 監視器。

使用適用于 Azure 監視器的[OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)來檢測您的應用程式。

這個範例示範如何將警告層級的記錄檔傳送至 Azure 監視器。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>探索記錄
在「偵測模式」中執行您的應用程式或即時部署。

在[Application Insights 入口網站][portal]的應用程式 [總覽] 窗格中，選取 [[搜尋][diagnostic]]。

例如，您可以：

* 篩選記錄追蹤或具有特定屬性的專案。
* 詳細檢查特定項目。
* 尋找與相同的使用者要求相關的其他系統記錄檔資料（具有相同的 OperationId）。
* 將頁面的設定儲存為我的最愛。

> [!NOTE]
>如果您的應用程式傳送大量資料，而且您使用的是 Application Insights SDK for ASP.NET version 2.0.0-Beta3 或更新版本，則*適應性取樣*功能可能會運作，並只傳送部分遙測。 [深入了解取樣。](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>疑難排解
### <a name="how-do-i-do-this-for-java"></a>如果是 Java，我要怎麼做？
使用 [Java 記錄配接器](../../azure-monitor/app/java-trace-logs.md)。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>專案內容功能表上沒有 Application Insights 選項
* 請確定已在開發電腦上安裝 Developer Analytics Tools。 在 Visual Studio**工具** > ] [**擴充功能和更新**] 中，尋找**Developer Analytics Tools**。 如果不在 [**已安裝**] 索引標籤上，請開啟 [**線上**] 索引標籤並安裝它。
* 這可能是 Devloper 分析工具不支援的專案類型。 請使用 [手動安裝](#manual-installation)。

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>設定工具中沒有任何記錄檔介面卡選項
* 請先安裝記錄架構。
* 如果您使用的是 System.webserver，請確定您已[在*web.config*中設定](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)它。
* 請確定您有最新版本的 Application Insights。 在 Visual Studio 中，移至 [**工具** > ] [**擴充功能和更新**]，然後開啟 [**更新**] 索引標籤。如果**Developer Analytics Tools** ，請選取它來更新它。

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>我收到「檢測金鑰不能是空的」錯誤訊息
您可能已安裝記錄介面卡 Nuget 封裝，但未安裝 Application Insights。 在方案總管中，以滑鼠右鍵按一下 [ *ApplicationInsights*]，然後選取 [**更新 Application Insights**。 系統會提示您登入 Azure 並建立 Application Insights 資源，或重複使用現有資源。 這應該會修正問題。

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>我可以在診斷搜尋中看到追蹤，而不是其他事件
所有事件和要求都可能需要一些時間才能通過管線。

### <a name="how-much-data-is-retained"></a><a name="limits"></a>保留多少資料？
有數個因素會影響所保留的資料量。 如需詳細資訊，請參閱客戶事件計量頁面的[限制](../../azure-monitor/app/api-custom-events-metrics.md#limits)一節。

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>我看不到我預期的一些記錄檔專案
如果您的應用程式傳送長篇大論的資料量，而且您使用適用于 ASP.NET 版本2.0.0 的 Application Insights SDK-Beta3 或更新版本，則適應性取樣功能可能會運作，並只傳送部分遙測。 [深入了解取樣。](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>後續步驟

* [在 ASP.NET 中診斷失敗和例外狀況][exceptions]
* [深入瞭解搜尋][diagnostic]
* [設定可用性和回應性測試][availability]
* [疑難排解][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
