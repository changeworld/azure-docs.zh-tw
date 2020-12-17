---
title: 在 Azure Service Fabric、雲端服務和虛擬機器中啟用 .NET 應用程式的快照偵錯工具 |Microsoft Docs
description: 為 Azure Service Fabric、雲端服務和虛擬機器中的 .NET 應用程式啟用快照偵錯工具
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: aa6577a6ae7f7ca1d938bbbb062557684076c78d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656444"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>為 Azure Service Fabric、雲端服務和虛擬機器中的 .NET 應用程式啟用快照偵錯工具

如果您的 ASP.NET 或 ASP.NET core 應用程式在 Azure App Service 中執行，強烈建議您 [透過 Application Insights 入口網站頁面啟用快照偵錯工具](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)。 但是，如果您的應用程式需要自訂的快照偵錯工具設定或預覽版本的 .NET core，則除了 [透過 Application Insights 入口網站頁面啟用](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)的指示 ***之外，還*** 應遵循此指示。

如果您的應用程式在 Azure Service Fabric、雲端服務、虛擬機器或內部部署機器中執行，則應該使用下列指示。 

## <a name="configure-snapshot-collection-for-aspnet-applications"></a>設定 ASP.NET 應用程式的快照集集合

1. 如果您尚未這麼做，請[在 Web 應用程式中啟用 Application Insights](./asp-net.md)。

2. 將 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件納入您的應用程式。

3. 如有需要，自訂新增至 [ApplicationInsights.config](./configuration-with-applicationinsights-config.md)的快照偵錯工具設定。預設的快照偵錯工具設定通常是空的，而且所有設定都是選擇性的。 以下範例顯示相當於預設設定的設定：

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. 快照集只會收集向 Application Insights 回報的例外狀況。 在某些情況下 (例如，舊版的 .NET 平台)，您可能需要[設定例外狀況集合](./asp-net-exceptions.md#exceptions)，才能在入口網站中查看快照集的例外狀況。


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>使用 ASP.NET Core 2.0 或更高版本來設定應用程式的快照集集合

1. 如果您尚未這麼做，請[在 ASP.NET Core Web 應用程式中啟用 Application Insights](./asp-net-core.md)。

    > [!NOTE]
    > 請確定您的應用程式參考的是 2.1.1 版或更新版本的 Microsoft.ApplicationInsights.AspNetCore 封裝。

2. 將 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件納入您的應用程式。

3. 修改您應用程式的 `Startup` 類別，以新增和設定快照集收集器的遙測處理器。
    1. 如果使用 [ApplicationInsights. Microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件版本1.3.5 或更新版本，請將下列 using 語句新增至 `Startup.cs` 。

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       在的類別中，于 ConfigureServices 方法的結尾新增下列程式 `Startup` `Startup.cs` 。

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. 如果使用 [ApplicationInsights. Microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件版本1.3.4 或以下版本，請將下列 using 語句新增至 `Startup.cs` 。

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```

       將下列 `SnapshotCollectorTelemetryProcessorFactory` 類別新增至 `Startup` 類別。

       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;

               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;

               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        將 `SnapshotCollectorConfiguration` 和 `SnapshotCollectorTelemetryProcessorFactory` 服務新增至啟動管線：

        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

               // TODO: Add other services your application needs here.
           }
       }
       ```

4. 如有需要，請將 SnapshotCollectorConfiguration 區段新增至 appsettings.js，以自訂快照偵錯工具設定。 快照偵錯工具設定中的所有設定都是選擇性的。 以下範例顯示相當於預設設定的設定：

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>設定其他 .NET 應用程式的快照集集合

1. 如果您的應用程式尚未使用 Application Insights 進行檢測，請從[啟用 Application Insights 及設定檢測金鑰](./windows-desktop.md)著手。

2. 在您的應用程式中新增 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件。

3. 快照集只會收集向 Application Insights 回報的例外狀況。 您可能需要修改程式碼才能回報例外狀況。 例外狀況處理程式碼取決於應用程式的結構，但有一個範例如下：
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>後續步驟

- 產生可觸發例外狀況的應用程式流量。 然後，等候10到15分鐘，將快照集傳送至 Application Insights 實例。
- 請參閱 Azure 入口網站中的 [快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) 集。
- 如需疑難排解快照偵錯工具問題的協助，請參閱 [快照偵錯工具疑難排解](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

