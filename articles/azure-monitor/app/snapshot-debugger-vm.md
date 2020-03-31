---
title: 為 Azure 服務結構、雲服務和虛擬機器中的 .NET 應用啟用快照調試器 |微軟文檔
description: 為 Azure 服務結構、雲服務和虛擬機器中的 .NET 應用啟用快照調試器
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 194a2da23c8fb405c492df8f6ee173cc97fde4ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671332"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>為 Azure 服務結構、雲服務和虛擬機器中的 .NET 應用啟用快照調試器

如果ASP.NET或ASP.NET核心應用程式在 Azure 應用服務中運行，則強烈建議[通過應用程式見解門戶頁啟用快照調試器](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)。 但是，如果應用程式需要自訂的快照調試器配置或 .NET 核心的預覽版本，則除了[通過應用程式見解門戶頁啟用](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)的說明***外***，還應遵循此說明。

如果應用程式在 Azure 服務結構、雲服務、虛擬機器或本地電腦中運行，則應使用以下說明。 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>設定 ASP.NET 應用程式的快照集集合

1. 如果您尚未這麼做，請[在 Web 應用程式中啟用 Application Insights](../../azure-monitor/app/asp-net.md)。

2. 將 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件納入您的應用程式。

3. 如果需要，自訂添加到[應用程式 Insights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)的快照調試器配置。預設快照調試器配置大多為空，所有設置都是可選的。 下面是顯示等效于預設配置的配置的示例：

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

4. 快照集只會收集向 Application Insights 回報的例外狀況。 在某些情況下 (例如，舊版的 .NET 平台)，您可能需要[設定例外狀況集合](../../azure-monitor/app/asp-net-exceptions.md#exceptions)，才能在入口網站中查看快照集的例外狀況。


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>使用 ASP.NET Core 2.0 或以上為應用程式佈建快照集合

1. 如果您尚未這麼做，請[在 ASP.NET Core Web 應用程式中啟用 Application Insights](../../azure-monitor/app/asp-net-core.md)。

    > [!NOTE]
    > 請確定您的應用程式參考的是 2.1.1 版或更新版本的 Microsoft.ApplicationInsights.AspNetCore 封裝。

2. 將 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件納入您的應用程式。

3. 修改您應用程式的 `Startup` 類別，以新增和設定快照集收集器的遙測處理器。
    1. 如果使用[Microsoft.應用程式見解.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包版本 1.3.5 或以上，則使用 語句`Startup.cs`將以下內容添加到 。

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       在 中的`Startup``Startup.cs`類中的佈建服務方法的末尾添加以下內容。

       ```csharp
            services.AddSnapshotCollector((configuration) => Configuration.Bind(nameof(SnapshotCollectorConfiguration), configuration));
       ```
    2. 如果使用[Microsoft.應用程式見解.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包版本 1.3.4 或以下，則使用 語句`Startup.cs`將以下內容添加到 。

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

4. 如果需要，通過將快照收集器配置部分添加到 appsettings.json 來自訂快照調試器配置。 快照調試器配置中的所有設置都是可選的。 下面是顯示等效于預設配置的配置的示例：

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

1. 如果您的應用程式尚未使用 Application Insights 進行檢測，請從[啟用 Application Insights 及設定檢測金鑰](../../azure-monitor/app/windows-desktop.md)著手。

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

- 生成應用程式可觸發異常的流量。 然後，等待 10 到 15 分鐘才能將快照發送到應用程式見解實例。
- 請參閱 Azure 門戶中的[快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)。
- 有關解決快照調試器問題的疑難排解，請參閱[快照調試器故障排除](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。
