---
title: 適用於主控台應用程式的 Azure Application Insights | Microsoft Docs
description: 監視 Web 應用程式的可用性、效能和使用方式。
ms.topic: conceptual
ms.date: 05/21/2020
ms.reviewer: lmolkova
ms.openlocfilehash: 28f86e32dd73e25079ca685538fd0cb6f351b2d9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87310458"
---
# <a name="application-insights-for-net-console-applications"></a>適用於 .NET 主控台應用程式的 Application Insights

[Application Insights](./app-insights-overview.md) 可讓您監視 Web 應用程式的可用性、效能和使用情況。

您需要 [Microsoft Azure](https://azure.com) 的訂用帳戶。 使用 Microsoft 帳戶登入，此帳戶可能是您針對 Windows、Xbox Live 或其他 Microsoft 雲端服務所擁有的帳戶。 您的小組可能已有 Azuare 組織訂用帳戶：請洽詢擁有者將您的 Microsoft 帳戶新增至其中。

> [!NOTE]
> *強烈建議您*針對任何主控台應用程式，使用[這裡](./worker-service.md)的[ApplicationInsights WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)套件和相關聯的指示。 此套件的目標 [`NetStandard2.0`](/dotnet/standard/net-standard) 是，因此可在 .Net Core 2.1 或更高版本中使用，並 .NET Framework 4.7.2 或更高版本。

## <a name="getting-started"></a>開始使用

* 在 [Azure 入口網站](https://portal.azure.com)中，建立 [Application Insights 資源](./create-new-resource.md)。 針對應用程式類型，選擇 [一般]。
* 取得檢測金鑰的副本。 在您建立之新資源的 [基本資訊] 下拉式清單中尋找金鑰。
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
> 遙測不會立即傳送。 遙測項目是由 ApplicationInsights SDK 批次處理並傳送。 在呼叫 `Track()` 方法之後立即結束的主控台應用程式中，除非 `Flush()` 和 `Sleep`/`Delay` 在應用程式結束之前完成，否則無法傳送遙測，如本文稍後的[完整範例](#full-example)所示。 如果您使用的是 `InMemoryChannel`，則不需要 `Sleep`。 有一個關於需要 `Sleep` 的現行問題，會在這裡追蹤：[ApplicationInsights-dotnet/issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


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
> .NET Core 上不支援讀取設定檔。 您可以考慮使用 [Application Insights SDK for ASP.NET Core](./asp-net-core.md)

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

如果您使用純 `TelemetryConfiguration()` 建構函式建立了設定，則需要額外啟用相互關聯支援。 如果您從檔案讀取設定，並使用了 `TelemetryConfiguration.CreateDefault()` 或 `TelemetryConfiguration.Active`，**則不需要啟用**。

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* 您可能也想要安裝效能計數器收集器模組並加以初始化，如[這裡](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/)所述


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

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
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
* [監視相依性](./asp-net-dependencies.md)，以查看 REST、SQL 或其他外部資源是否降低您的效能。
* [使用 API](./api-custom-events-metrics.md) 可傳送您自己的事件和計量，以取得您的應用程式效能和使用方式的更詳細檢視。

