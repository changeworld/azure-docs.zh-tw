---
title: 適用於主控台應用程式的 Azure Application Insights | Microsoft Docs
description: 監視 Web 應用程式的可用性、效能和使用方式。
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: lmolkova
ms.openlocfilehash: baaea0f8055eeff0314fcf5fde00729ea8091d12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655424"
---
# <a name="application-insights-for-net-console-applications"></a>適用於 .NET 主控台應用程式的 Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) 可讓您監視 Web 應用程式的可用性、效能和使用情況。

您需要 [Microsoft Azure](https://azure.com) 的訂用帳戶。 使用 Microsoft 帳戶登入，此帳戶可能是您針對 Windows、Xbox Live 或其他 Microsoft 雲端服務所擁有的帳戶。 您的小組可能已有 Azuare 組織訂用帳戶：請洽詢擁有者將您的 Microsoft 帳戶新增至其中。

> [!NOTE]
> 有一個新的 Application Insights SDK，稱為[ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) ，可用來為任何主控台應用程式啟用 Application Insights。 建議您在[這裡](../../azure-monitor/app/worker-service.md)使用此封裝和相關聯的指示。 此套件的[`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)目標是，因此可在 .net Core 2.0 或更高版本中使用，並 .NET Framework 4.7.2 或更高版本。

## <a name="getting-started"></a>開始使用

* 在 [Azure 入口網站](https://portal.azure.com)中，建立 [Application Insights 資源](../../azure-monitor/app/create-new-resource.md)。 針對應用程式類型，選擇 [一般]****。
* 取得檢測金鑰的副本。 在您所建立之新資源的 [ **Essentials** ] 下拉式集中尋找金鑰。
* 安裝最新的 [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) \(英文\) 封裝。
* 在程式碼中設定檢測金鑰，然後再追蹤任何遙測 (或設定 APPINSIGHTS_INSTRUMENTATIONKEY 環境變數)。 在那之後，您應該能夠手動追蹤遙測，並在 Azure 入口網站上看到它

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> 遙測不會立即傳送。 ApplicationInsights SDK 會批次處理並傳送遙測專案。 在呼叫`Track()`方法之後立即結束的主控台應用程式中，除非和`Flush()` `Sleep`在應用程式結束之前完成，否則不會傳送遙測，如本文稍後的[完整範例](#full-example)所示。


* 安裝最新版的 [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) \(英文\) 封裝；它會自動追蹤 HTTP、SQL 或某些其他外部相依性呼叫。

您可以從程式碼或使用 `ApplicationInsights.config` 檔案，將 Application Insights 初始化並加以設定。 確定初始化會盡早發生。 

> [!NOTE]
> 參考 **ApplicationInsights.config** 的指示僅適用於目標為 .NET Framework 的應用程式，不適用於 .NET Core 應用程式。

### <a name="using-config-file"></a>使用設定檔

根據預設，Application Insights SDK 會在建立 `TelemetryConfiguration` 時，於工作目錄中尋找 `ApplicationInsights.config` 檔案

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

您也可以指定設定檔的路徑。

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

如需詳細資訊，請參閱[設定檔參考](configuration-with-applicationinsights-config.md)。

您可能會藉由安裝最新版的 [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) \(英文\) 封裝，來取得設定檔的完整範例。 以下是相當於程式碼範例之相依性集合的**最基本**設定。

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>從程式碼設定遙測集合
> [!NOTE]
> .NET Core 不支援讀取設定檔。 您可以考慮使用[適用于 ASP.NET Core 的 APPLICATION INSIGHTS SDK](../../azure-monitor/app/asp-net-core.md)

* 在應用程式啟動期間，建立並設定 `DependencyTrackingTelemetryModule` 執行個體；它必須是單一的，而且必須針對應用程式存留期加以保留。

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* 新增一般遙測初始設定式

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

如果您使用一般`TelemetryConfiguration()`的函式建立了設定，則需要另外啟用相互關聯支援。 如果您從檔案讀取設定（使用`TelemetryConfiguration.CreateDefault()`或`TelemetryConfiguration.Active`），則**不需要此**設定。

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* 您可能也會想要安裝並初始化效能計數器收集器模組，如[這裡](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)所述


#### <a name="full-example"></a>完整範例

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>後續步驟
* [監視相依性](../../azure-monitor/app/asp-net-dependencies.md)，以查看 REST、SQL 或其他外部資源是否降低您的效能。
* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md) 可傳送您自己的事件和計量，以取得您的應用程式效能和使用方式的更詳細檢視。
