---
title: 在 Application Insights 中探索 .NET 追蹤記錄
description: 搜索由跟蹤、NLog 或 Log4Net 生成的日誌。
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276266"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>在應用程式見解中流覽 .NET/.NET 核心和 Python 跟蹤日誌

從 ILogger、NLog、log4Net 或 System.診斷.跟蹤到[Azure 應用程式見解][start]，發送ASP.NET/ASP.NET核心應用程式的診斷跟蹤日誌。 對於 Python 應用程式，使用 Azure 監視器 OpenCensus Python 中的 AzureLogHandler 發送診斷跟蹤日誌。 然後，您可以流覽和搜索它們。 這些日誌與應用程式中的其他日誌檔合併，因此您可以標識與每個使用者請求關聯的跟蹤，並將它們與其他事件和異常報告相關聯。

> [!NOTE]
> 是否需要日誌捕獲模組？ 它是協力廠商記錄器的有用配接器。 但是，如果您尚未使用 NLog、log4Net 或 System.診斷.Trace，請考慮直接調用[**應用程式見解 TrackTrace（）。**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
>
>
## <a name="install-logging-on-your-app"></a>在您的 app 上安裝記錄
在專案中安裝您選擇的日誌記錄框架，這將導致 app.config 或 Web.config 中的條目。

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

或者按右鍵解決方案資源管理器中的專案以**配置應用程式見解**。 選擇"**配置跟蹤收集**"選項。

> [!NOTE]
> 沒有 Application Insights 功能表或記錄收集器選項嗎？ 請嘗試進行[疑難排解](#troubleshooting)。

## <a name="manual-installation"></a>手動安裝
如果 Application Insights 安裝程式不支援您的專案類型 (例如 Windows 傳統型專案)，請使用這個方法。

1. 如果您打算使用 log4Net 或 NLog，請將它安裝在您的專案。
2. 在解決方案資源管理器中，按右鍵專案，然後選擇 **"管理 NuGet 包**"。
3. 搜索"應用程式見解"。
4. 選取下列其中一個套件：

   - 對於 ILogger：[微軟.擴展.日誌記錄.應用程式見解](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - 對於 NLog：[微軟.應用程式見解.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - 對於 Log4Net：[微軟.應用程式見解.Log4Net應用商](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - 用於系統.診斷：[微軟.應用程式洞察.跟蹤傾聽者](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [微軟.應用程式洞察.診斷來源傾聽者](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [微軟.應用程式洞察.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [微軟.應用程式洞察.事件來源傾聽者](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet 包安裝必要的程式集，並修改 Web.config 或 app.config（如果適用）。

## <a name="ilogger"></a>ILogger

有關將應用程式見解 ILogger 實現與主控台應用程式和 ASP.NET核心使用的示例，請參閱[應用程式見解記錄器提供程式 .NET 核心 ILogger 日誌](ilogger.md)。

## <a name="insert-diagnostic-log-calls"></a>插入診斷記錄呼叫
如果您使用 System.Diagnostics.Trace，典型的呼叫如下：

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

如果您更喜歡 log4net 或 NLog，請使用：

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>使用事件源事件
您可以將 [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 設定為要傳送至 Application Insights 作為追蹤的事件。 首先，安裝 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 套件。 然後編輯 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 檔案的 `TelemetryModules` 區段。

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

針對每個來源，您可以設定下列參數︰
 * **名稱**指定要收集的事件源的名稱。
 * **級別**指定要收集的日誌記錄級別：*嚴重*、*錯誤*、*資訊、**日誌始終*、*詳細或**警告*。
 * **關鍵字**（可選）指定要使用的關鍵字組合的整數值。

## <a name="use-diagnosticsource-events"></a>使用診斷源事件
您可以將 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 設定為要傳送至 Application Insights 作為追蹤的事件。 首先，[`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener)安裝 NuGet 包。 然後編輯[應用程式 Insights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)檔的"遙測模組"部分。

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

對於要跟蹤的每個診斷源，將帶有**Name**屬性集的條目添加到診斷源的名稱中。

## <a name="use-etw-events"></a>使用 ETW 事件
您可以配置 Windows （ETW） 事件的事件跟蹤，以便作為跟蹤發送到應用程式見解。 首先，安裝 `Microsoft.ApplicationInsights.EtwCollector` NuGet 套件。 然後編輯[應用程式 Insights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)檔的"遙測模組"部分。

> [!NOTE] 
> 僅當承載 SDK 的進程在性能日誌使用者或管理員的成員身份下運行時，才能收集 ETW 事件。

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

針對每個來源，您可以設定下列參數︰
 * **提供程式名稱**是要收集的 ETW 提供程式的名稱。
 * **提供程式 Guid**指定要收集的 ETW 提供程式的 GUID。 它可以使用而不是`ProviderName`。
 * **級別**設置要收集的日誌記錄級別。 它可以*是嚴重*，*錯誤*，*資訊，**日誌始終*，*詳細，* 或*警告*。
 * **關鍵字**（可選）設置要使用的關鍵字組合的整數值。

## <a name="use-the-trace-api-directly"></a>直接使用跟蹤 API
您可以直接呼叫 Application Insights 追蹤 API。 記錄配接器會使用此 API。

例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace 的優點在於您可以將較長的資料放在訊息中。 例如，您可以在該處編碼 POST 資料。

您還可以向郵件添加嚴重性級別。 並且，與其他遙測一樣，您可以添加屬性值來説明篩選或搜索不同的跟蹤集。 例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

這將使您能夠在["搜索"][diagnostic]中輕鬆篩選出與特定資料庫相關的特定嚴重性級別的所有消息。

## <a name="azureloghandler-for-opencensus-python"></a>用於開放Census Python 的 AzureLogHandler
Azure 監視器日誌處理常式允許您將 Python 日誌匯出到 Azure 監視器。

使用 Azure 監視器的[OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md)對應用程式進行檢測。

此示例演示如何向 Azure 監視器發送警告級別日誌。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>探索記錄
在偵錯模式下運行應用或即時部署應用。

在[應用在"應用程式見解"門戶][portal]中的概述窗格中，選擇["搜索][diagnostic]"。

例如，您可以：

* 篩選日誌跟蹤或具有特定屬性的項。
* 詳細檢查特定項目。
* 查找與同一使用者請求相關的其他系統日誌資料（具有相同的操作 Id）。
* 將頁面的配置另存為我的最愛。

> [!NOTE]
>如果應用程式發送大量資料，並且您對ASP.NET版 2.0.0-Beta3 或更高版本的應用程式見解 SDK 使用應用程式見解 SDK，*則自我調整採樣*功能可能運行並僅發送部分遙測資料。 [深入了解取樣。](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>疑難排解
### <a name="how-do-i-do-this-for-java"></a>如果是 Java，我要怎麼做？
使用 [Java 記錄配接器](../../azure-monitor/app/java-trace-logs.md)。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>專案內容功能表上沒有 Application Insights 選項
* 確保開發人員分析工具安裝在開發電腦上。 在視覺化工作室**工具** > **擴展和更新**中，查找**開發人員分析工具**。 如果未在 **"已安裝**"選項卡上，請打開"**連線**"選項卡並安裝它。
* 這可能是 Devloper 分析工具不支援的專案類型。 請使用 [手動安裝](#manual-installation)。

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>組態工具中沒有日誌配接器選項
* 首先安裝日誌記錄框架。
* 如果您使用的是 System.診斷.Trace，請確保它在[*Web.config*中配置](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)了它。
* 確保您擁有最新版本的應用程式見解。 在視覺化工作室中，轉到 **"工具** > **擴展和更新**"，然後打開 **"更新**"選項卡。如果存在**開發人員分析工具**，請選擇它以更新它。

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>我收到"檢測金鑰不能為空"錯誤訊息
您可能安裝了日誌記錄配接器 Nuget 包，而無需安裝應用程式見解。 在解決方案資源管理器中，按右鍵 *"應用程式見解.配置*"，然後選擇 **"更新應用程式見解**"。 系統將提示您登錄到 Azure 並創建應用程式見解資源或重用現有資源。 這應該解決問題。

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>我可以在診斷搜索中看到跟蹤，但不能看到其他事件
所有事件和請求都可能需要一段時間才能通過管道。

### <a name="how-much-data-is-retained"></a><a name="limits"></a>保留多少資料？
有幾個因素會影響保留的資料量。 有關詳細資訊，請參閱客戶事件指標頁[的限制](../../azure-monitor/app/api-custom-events-metrics.md#limits)部分。

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>我沒有看到一些日誌條目，我期望
如果應用程式發送大量資料，並且您對ASP.NET版本 2.0.0-Beta3 或更高版本使用應用程式見解 SDK，則自我調整採樣功能可能運行並僅發送部分遙測資料。 [深入了解取樣。](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>後續步驟

* [在 ASP.NET 中診斷失敗和例外狀況][exceptions]
* [瞭解有關搜索的更多][diagnostic]
* [設定可用性和回應性測試][availability]
* [疑難排解][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
