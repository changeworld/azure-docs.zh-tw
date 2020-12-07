---
title: ASP.NET Core 應用程式 Azure 應用程式見解 |Microsoft Docs
description: 監視 ASP.NET Core Web 應用程式的可用性、效能和使用方式。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/30/2020
ms.openlocfilehash: 8505e67e3db7460308d208ce4f83d29a1fb4d862
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763191"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 應用程式的 Application Insights

本文說明如何啟用 [ASP.NET Core](/aspnet/core) 應用程式的 Application Insights。 當您完成本文中的指示時，Application Insights 將會從您的 ASP.NET Core 應用程式收集要求、相依性、例外狀況、效能計數器、心跳和記錄。

我們將在這裡使用的範例是以的 [MVC 應用程式](/aspnet/core/tutorials/first-mvc-app) 為目標 `netcoreapp3.0` 。 您可以將這些指示套用至所有 ASP.NET Core 應用程式。 如果您使用的是背景 [工作角色服務](/aspnet/core/fundamentals/host/hosted-services#worker-service-template)，請使用 [這裡](./worker-service.md)的指示。

## <a name="supported-scenarios"></a>支援的案例

[適用于 ASP.NET Core 的 APPLICATION INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)可以監視您的應用程式，不論其執行位置或方式為何。 如果您的應用程式正在執行，且具有與 Azure 的網路連線能力，則可以收集遙測。 支援任何 .NET Core 支援的 Application Insights 監視。 支援涵蓋：
* **作業系統**： Windows、Linux 或 Mac。
* **裝載方法**：內部處理序或跨處理序。
* **部署方法**：架構相依或獨立式。
* **網頁伺服器**：IIS (網際網路資訊伺服器) 或 Kestrel。
* **裝載平台**：Azure App Service、Azure VM、Docker、Azure Kubernetes Service (AKS) 等的 Web Apps 功能。
* **.Net core 版本**：所有正式 [支援](https://dotnet.microsoft.com/download/dotnet-core) 的 .net Core 版本。
* **IDE**： Visual Studio、VS Code 或命令列。

> [!NOTE]
> ASP.NET Core 3.x 需要 [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) 或更新版本。

## <a name="prerequisites"></a>先決條件

- 正常運作的 ASP.NET Core 應用程式。 如果您需要建立 ASP.NET Core 應用程式，請遵循此 [ASP.NET Core 教學](/aspnet/core/getting-started/)課程。
- 有效的 Application Insights 檢測金鑰。 需要有此金鑰才能將任何遙測資料傳送至 Application Insights。 如果您需要建立新的 Application Insights 資源以取得檢測金鑰，請參閱 [建立 Application Insights 資源](./create-new-resource.md)。

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>啟用 Application Insights 伺服器端遙測 (Visual Studio) 

針對 Visual Studio for Mac 請使用 [手動指引](#enable-application-insights-server-side-telemetry-no-visual-studio)。 只有 Visual Studio 的 Windows 版本支援此程式。

1. 在 Visual Studio 中，開啟您的專案。

    > [!TIP]
    > 如果您想要的話，可以為專案設定原始檔控制，以便追蹤 Application Insights 進行的所有變更。 若要啟用原始檔控制，**請選取 [** 檔案  >  **加入至原始檔控制**]。

2. 選取 [**專案**  >  **加入 Application Insights 遙測**]。

3. 選取 **[馬上開始]** 。 根據您 Visual Studio 的版本而定，此選項的文字可能會有所不同。 某些較舊的版本會改用 [ **開始免費** ] 按鈕。

4. 選取您的訂用帳戶。 然後選取 [**資源**  >  **註冊**]。

5. 將 Application Insights 新增至您的專案之後，請檢查以確認您使用的是最新穩定版本的 SDK。 移至 **Project**  >  **管理 NuGet 套件**  >  **ApplicationInsights. AspNetCore**。 如有需要，請選擇 [ **更新**]。

     ![顯示要在哪裡選取更新 Application Insights 套件的螢幕擷取畫面](./media/asp-net-core/update-nuget-package.png)

6. 如果您已遵循選用提示，並將專案新增至原始檔控制，請移至 [ **View**  >  **Team Explorer**  >  **變更**]。 然後選取每個檔案，以查看 Application Insights 遙測所做變更的差異查看。

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>啟用 Application Insights 伺服器端遙測 (不 Visual Studio) 

1. 安裝 [適用于 ASP.NET Core 的 APPLICATION INSIGHTS SDK NuGet 套件](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)。 建議您一律使用最新的穩定版本。 在 [開放原始碼 GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/releases)存放庫中尋找 SDK 的完整版本資訊。

    下列程式碼範例會顯示要新增至專案檔案的變更 `.csproj` 。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. 將加入 `services.AddApplicationInsightsTelemetry();` 至 `ConfigureServices()` 類別中的方法 `Startup` ，如下列範例所示：

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. 設定檢測金鑰。

    雖然您可以提供檢測金鑰做為的引數 `AddApplicationInsightsTelemetry` ，但建議您在 configuration 中指定檢測金鑰。 下列程式碼範例顯示如何在中指定檢測金鑰 `appsettings.json` 。 `appsettings.json`在發佈期間，請務必將複製到應用程式的根資料夾。

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    或者，您也可以在下列任一環境變數中指定檢測金鑰：

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    例如：

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY` 通常會在 [Azure Web Apps](./azure-web-apps.md?tabs=net)中使用，但也可以用於支援此 SDK 的所有位置。  (如果您正在進行無程式碼 web 應用程式監視，則如果您未使用連接字串，則需要此格式。 ) 

    您現在也可以使用 [連接字串](./sdk-connection-string.md?tabs=net)，而不用設定檢測金鑰。

    > [!NOTE]
    > 在程式碼中指定的檢測金鑰會優先于透過 `APPINSIGHTS_INSTRUMENTATIONKEY` 其他選項贏得的環境變數。

### <a name="user-secrets-and-other-configuration-providers"></a>使用者秘密和其他設定提供者

如果您想要將檢測金鑰儲存在 ASP.NET Core 使用者密碼，或從另一個設定提供者抓取，您可以使用多載搭配 `Microsoft.Extensions.Configuration.IConfiguration` 參數。 例如： `services.AddApplicationInsightsTelemetry(Configuration);` 。
從 ApplicationInsights. AspNetCore 版本 [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)開始，呼叫 `services.AddApplicationInsightsTelemetry()` 會自動從應用程式讀取檢測金鑰 `Microsoft.Extensions.Configuration.IConfiguration` 。 不需要明確地提供 `IConfiguration` 。

## <a name="run-your-application"></a>執行您的應用程式

執行您的應用程式，並對其提出要求。 遙測現在應該流向 Application Insights。 Application Insights SDK 會自動向您的應用程式收集傳入的 web 要求，以及下列遙測。

### <a name="live-metrics"></a>即時計量    

您可以使用[即時計量](./live-stream.md)來快速確認是否已正確設定 Application Insights 監視。 雖然可能需要幾分鐘的時間，遙測才會出現在入口網站和分析中，但即時計量會以近乎即時的方式顯示正在執行之進程的 CPU 使用量。 它也可以顯示其他遙測，例如要求、相依性、追蹤等等。

### <a name="ilogger-logs"></a>ILogger 記錄

預設設定會收集 `ILogger` 嚴重性和更新版本的記錄 `Warning` 。 您可以 [自訂](#how-do-i-customize-ilogger-logs-collection)此設定。

### <a name="dependencies"></a>相依性

依預設會啟用相依性集合。 [本文說明](asp-net-dependencies.md#automatically-tracked-dependencies) 自動收集的相依性，同時也包含執行手動追蹤的步驟。

### <a name="performance-counters"></a>效能計數器

ASP.NET Core 中的 [效能計數器](./performance-counters.md) 支援有限：

* 如果應用程式是在 Azure Web Apps (Windows) 中執行，SDK 版本2.4.1 和以後版本會收集效能計數器。
* 如果應用程式是在 Windows 中執行，而且目標是 `NETSTANDARD2.0` 或以後版本，SDK 版本2.7.1 和更新版本會收集效能計數器。
* 對於以 .NET Framework 為目標的應用程式，所有版本的 SDK 都支援效能計數器。
* SDK 2.8.0 和更新版本支援 Linux 中的 cpu/記憶體計數器。 Linux 不支援其他計數器。 在 Linux (和其他非 Windows 環境) 中取得系統計數器的建議方式是使用 [EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` 預設為啟用。 [EventCounter](eventcounters.md)教學課程中的指示說明如何設定要收集的計數器清單。

## <a name="enable-client-side-telemetry-for-web-applications"></a>針對 web 應用程式啟用用戶端遙測

上述步驟足以協助您開始收集伺服器端遙測資料。 如果您的應用程式具有用戶端元件，請遵循後續步驟來開始收集 [使用量遙測](./usage-overview.md)。

1. 在中 `_ViewImports.cshtml` ，加入插入：

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. 在中 `_Layout.cshtml` ，于 `HtmlHelper` 區段的結尾， `<head>` 但在任何其他腳本之前插入。 如果您想要從頁面報告任何自訂的 JavaScript 遙測，請將它插入此程式碼片段後面：

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

或者，您也 `FullScript` 可以 `ScriptBody` 從 SDK v 2.14 開始使用。 如果您需要控制 `<script>` 標記來設定內容安全性原則，請使用此設定：

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

稍 `.cshtml` 早參考的檔案名來自預設的 MVC 應用程式範本。 最後，如果您想要適當地為應用程式啟用用戶端監視，JavaScript 程式碼片段必須出現在您 `<head>` 要監視的每個應用程式頁面的區段中。 您可以藉由將 JavaScript 程式碼片段新增至來完成此應用程式範本的目標 `_Layout.cshtml` 。 

如果您的專案不包含 `_Layout.cshtml` ，您仍然可以新增 [用戶端監視](./website-monitoring.md)。 若要這麼做，您可以將 JavaScript 程式碼片段新增至對等的檔案，以控制 `<head>` 應用程式內所有頁面的。 或者，您可以將程式碼片段加入至多個頁面，但此解決方案很難維護，因此我們通常不建議這麼做。

## <a name="configure-the-application-insights-sdk"></a>設定 Application Insights SDK

您可以自訂 Application Insights SDK，以 ASP.NET Core 變更預設設定。 Application Insights ASP.NET SDK 的使用者可能會熟悉使用或修改來變更設定 `ApplicationInsights.config` `TelemetryConfiguration.Active` 。 在 ASP.NET Core 中，幾乎所有的設定變更都是在 `ConfigureServices()` 您類別的方法中完成 `Startup.cs` ，除非您以其他方式進行導向。 下列各節提供詳細資訊。

> [!NOTE]
> 在 ASP.NET Core 應用程式中，不支援透過修改來變更設定 `TelemetryConfiguration.Active` 。

### <a name="using-applicationinsightsserviceoptions"></a>使用 ApplicationInsightsServiceOptions

您可以藉由傳遞到來修改一些一般設定 `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry` ，如此範例所示：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

中的完整設定清單 `ApplicationInsightsServiceOptions`

|設定 | 描述 | 預設
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | 啟用/停用 `PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | 啟用/停用 `RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | 啟用/停用 `EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | 啟用/停用 `DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  啟用/停用 `AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  啟用/停用 `AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | 啟用/停用 LiveMetrics 功能 | true
|EnableAdaptiveSampling | 啟用/停用調適型取樣 | true
|EnableHeartbeat | 啟用/停用會定期 (15 分鐘的預設) 傳送名為 ' HeartbeatState ' 的自訂度量，包含執行時間的相關資訊，例如 .NET 版本、Azure 環境資訊（如果適用的話）等等。 | true
|AddAutoCollectedMetricExtractor | 啟用/停用 AutoCollectedMetrics 解壓縮程式，這是一種 TelemetryProcessor，會在進行取樣之前，傳送要求/相依性預先匯總的計量。 | true
|RequestCollectionOptions.TrackExceptions | 啟用/停用要求收集模組未處理之例外狀況追蹤的報告。 | 在 NETSTANDARD 2.0 (中為 false，因為 ApplicationInsightsLoggerProvider) 會追蹤例外狀況，否則為 true。
|EnableDiagnosticsTelemetryModule | 啟用/停用 `DiagnosticsTelemetryModule` 。 停用此項將會忽略下列設定; `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

如需最新清單，請參閱[中 `ApplicationInsightsServiceOptions` 可設定的設定](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="configuration-recommendation-for-microsoftapplicationinsightsaspnetcore-sdk-2150--above"></a>適用于 ApplicationInsights. AspNetCore SDK 2.15.0 & 的設定建議

從 ApplicationInsights. AspNetCore SDK 版本 [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) ，建議您設定中提供的每項設定 `ApplicationInsightsServiceOptions` ，包括使用應用程式實例的 instrumentationkey `IConfiguration` 。 設定必須在 "ApplicationInsights" 區段底下，如下列範例所示。 appsettings.js的下一節會設定檢測金鑰，也會停用調適型取樣和效能計數器集合。

```json
{
    "ApplicationInsights": {
    "InstrumentationKey": "putinstrumentationkeyhere",
    "EnableAdaptiveSampling": false,
    "EnablePerformanceCounterCollectionModule": false
    }
}
```

如果 `services.AddApplicationInsightsTelemetry(aiOptions)` 使用，則會覆寫中的設定 `Microsoft.Extensions.Configuration.IConfiguration` 。

### <a name="sampling"></a>取樣

適用于 ASP.NET Core 的 Application Insights SDK 同時支援固定速率和調適型取樣。 預設會啟用調適型取樣。

如需詳細資訊，請參閱 [設定 ASP.NET Core 應用程式的](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)調適型取樣。

### <a name="adding-telemetryinitializers"></a>新增 TelemetryInitializers

當您想要使用其他資訊來擴充遙測資料時，請使用 [遙測初始化運算式](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) 。

將任何新 `TelemetryInitializer` 的新增至 `DependencyInjection` 容器，如下列程式碼所示。 SDK 會自動挑選 `TelemetryInitializer` 已新增至容器的任何一個 `DependencyInjection` 。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

> [!NOTE]
> `services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();` 適用于簡單的初始化運算式。 針對其他人，需要下列專案： `services.AddSingleton(new MyCustomTelemetryInitializer() { fieldName = "myfieldName" });`
    
### <a name="removing-telemetryinitializers"></a>移除 TelemetryInitializers

預設會出現遙測初始化運算式。 若要移除所有或特定的遙測初始化運算式，請在呼叫 *後* ，使用下列範例程式碼 `AddApplicationInsightsTelemetry()` 。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // Remove a specific built-in telemetry initializer
    var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                        (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
    if (tiToRemove != null)
    {
        services.Remove(tiToRemove);
    }

    // Remove all initializers
    // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
    services.RemoveAll(typeof(ITelemetryInitializer));
}
```

### <a name="adding-telemetry-processors"></a>新增遙測處理器

您可以 `TelemetryConfiguration` 使用上的擴充方法，將自訂遙測處理器加入至 `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` 。 您會在先進的 [篩選案例](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer)中使用遙測處理器。 使用下列範例。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>設定或移除預設 >telemetrymodules

Application Insights 使用遙測模組自動收集特定工作負載的有用遙測，而不需要使用者手動追蹤。

預設會啟用下列自動收集模組。 這些模組會負責自動收集遙測。 您可以停用或設定它們來改變其預設行為。

* `RequestTrackingTelemetryModule` -從傳入的 web 要求收集 RequestTelemetry。
* `DependencyTrackingTelemetryModule` -從傳出的 HTTP 呼叫和 sql 呼叫收集 [DependencyTelemetry](./asp-net-dependencies.md) 。
* `PerformanceCollectorModule` -收集 Windows PerformanceCounters。
* `QuickPulseTelemetryModule` -收集在即時計量入口網站中顯示的遙測。
* `AppServicesHeartbeatTelemetryModule` -收集以自訂計量的形式傳送的「核心」 (，) ，關於裝載應用程式 Azure App Service 環境。
* `AzureInstanceMetadataTelemetryModule` -收集以自訂計量的形式傳送的核心 (，) ，關於裝載應用程式的 Azure VM 環境。
* `EventCounterCollectionModule` -收集 [EventCounters。](eventcounters.md) 此課程模組是一項新功能，可在 SDK 版本2.8.0 和更高版本中取得。

若要設定任何預設值 `TelemetryModule` ，請在上使用擴充方法 `ConfigureTelemetryModule<T>` `IServiceCollection` ，如下列範例所示。

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

從2.12.2 版本開始， [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) 包含可停用任何預設模組的簡單選項。

### <a name="configuring-a-telemetry-channel"></a>設定遙測通道

預設 [遙測通道](./telemetry-channels.md) 為 `ServerTelemetryChannel` 。 如下列範例所示，您可以覆寫它。

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>動態停用遙測

如果您想要有條件且動態地停用遙測，您可以 `TelemetryConfiguration` 在程式碼中的任何位置使用 ASP.NET Core 相依性插入容器來解析實例，並 `DisableTelemetry` 在其上設定旗標。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

上述不會防止任何自動收集模組收集遙測。 只有將遙測傳送至 Application Insights 會以上述方法停用。 如果不需要特定的自動收集模組，最好先 [移除遙測模組](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-application-insights-support-aspnet-core-3x"></a>Application Insights 支援 ASP.NET Core 3.x 嗎？

是。 針對 ASP.NET Core 2.8.0 版或更高版本 [的 APPLICATION INSIGHTS SDK 進行](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 更新。 較舊版本的 SDK 不支援 ASP.NET Core 3.x。

此外，如果您使用 [此處](#enable-application-insights-server-side-telemetry-visual-studio)Visual Studio 的指示，請更新至最新版本的 Visual Studio 2019 (16.3.0 版) 以進行上架。 舊版的 Visual Studio 不支援 ASP.NET Core 3.x 應用程式的自動上線。

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>如何追蹤不會自動收集的遙測？

使用函式插入來取得的實例 `TelemetryClient` ，並在其上呼叫所需的 `TrackXXX()` 方法。 我們不建議 `TelemetryClient` `TelemetryConfiguration` 在 ASP.NET Core 應用程式中建立新的或實例。 的單一實例 `TelemetryClient` 已在容器中註冊 `DependencyInjection` ，而該容器 `TelemetryConfiguration` 與其他遙測資料共用。 只有在需要與其余遙測不同的設定時，才建議建立新的 `TelemetryClient` 實例。

下列範例顯示如何從控制器追蹤其他遙測。

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

如需有關 Application Insights 中的自訂資料包告的詳細資訊，請參閱 [Application Insights 自訂計量 API 參考](./api-custom-events-metrics.md)。 您可以使用類似的方法，透過 [>GETMETRIC API](./get-metric.md)將自訂計量傳送至 Application Insights。

### <a name="how-do-i-customize-ilogger-logs-collection"></a>如何? 自訂 ILogger 記錄檔收集？

依預設，只會自動捕獲嚴重性和更新版本的記錄 `Warning` 。 若要變更此行為，請明確覆寫提供者的記錄設定， `ApplicationInsights` 如下所示。
下列設定可讓 ApplicationInsights 捕獲嚴重性和更新版本的所有記錄 `Information` 。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
}
```

請務必注意，下列情況並不會導致 ApplicationInsights 提供者捕獲 `Information` 記錄。 這是因為 SDK 會新增預設記錄篩選器，指示 `ApplicationInsights` 只捕獲 `Warning` 和更新版本。 因此，ApplicationInsights 需要明確覆寫。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

閱讀有關 [ILogger](ilogger.md#control-logging-level)設定的詳細資訊。

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>某些 Visual Studio 範本會在 >iwebhostbuilder 上使用 .Useapplicationinsights ( # A1 擴充方法來啟用 Application Insights。 此使用方式是否仍然有效？

雖然 `UseApplicationInsights()` 仍然支援擴充方法，但它在 APPLICATION INSIGHTS SDK 版本2.8.0 中會標示為過時。 它將在下一個主要版本的 SDK 中移除。 啟用 Application Insights 遙測的建議方法是使用， `AddApplicationInsightsTelemetry()` 因為它提供多載來控制某些設定。 此外，在 ASP.NET Core 3.x 應用程式中，也 `services.AddApplicationInsightsTelemetry()` 是啟用 application insights 的唯一方法。

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>我要將 ASP.NET Core 應用程式部署到 Web Apps。 我是否仍要從 Web Apps 啟用 Application Insights 擴充功能？

如本文所示，如果在組建階段安裝 SDK，您就不需要從 App Service 入口網站啟用 [Application Insights 擴充](./azure-web-apps.md) 功能。 即使延伸模組已安裝，當它偵測到 SDK 已新增至應用程式時，它也會關閉。 如果您從擴充功能啟用 Application Insights，則不需要安裝及更新 SDK。 但是，如果您依照本文中的指示啟用 Application Insights，則有更大的彈性，因為：

   * Application Insights 遙測將繼續運作：
       * 所有作業系統，包括 Windows、Linux 和 Mac。
       * 所有發行模式，包括獨立或架構相依。
       * 所有的目標 framework，包括完整的 .NET Framework。
       * 所有裝載選項，包括 Web Apps、Vm、Linux、容器、Azure Kubernetes Service 和非 Azure 裝載。
       * 所有 .NET Core 版本，包括預覽版本。
   * 當您從 Visual Studio 進行調試時，可以在本機查看遙測。
   * 您可以使用 API 來追蹤其他自訂遙測 `TrackXXX()` 。
   * 您可以完整控制設定。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>我可以使用狀態監視器之類的工具來啟用 Application Insights 監視嗎？

不會。 [狀態監視器](./monitor-performance-live-website-now.md) 和 [狀態監視器 v2](./status-monitor-v2-overview.md) 目前只支援 ASP.NET 4.x。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果我在 Linux 中執行我的應用程式，是否支援所有功能？

是。 SDK 的功能支援在所有平臺上都相同，但有下列例外狀況：

* SDK 會收集 Linux 上的 [事件計數器](./eventcounters.md) ，因為只有 Windows 才支援 [效能計數器](./performance-counters.md) 。 大部分的度量都是一樣的。
* 雖然 `ServerTelemetryChannel` 預設會啟用，但如果應用程式是在 Linux 或 macOS 中執行，則通道不會自動建立本機儲存體資料夾，以在發生網路問題時暫時保留遙測。 由於這項限制，當發生暫時性的網路或伺服器問題時，遙測會遺失。 若要解決此問題，請設定通道的本機資料夾：

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

這項限制不適用於 [2.15.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.15.0) 和更新版本。

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>新的 .NET Core 3.x 背景工作服務範本應用程式是否支援此 SDK？

此 SDK 需要 `HttpContext` ，因此無法在任何非 HTTP 應用程式中運作，包括 .Net Core 3.x 背景工作服務應用程式。 請參閱 [這](worker-service.md) 份檔，以使用新發行的 ApplicationInsights. WorkerService SDK 在這類應用程式中啟用 application insights。

## <a name="open-source-sdk"></a>開放原始碼 SDK

* [讀取和參與程式碼](https://github.com/microsoft/ApplicationInsights-dotnet)。

如需最新的更新和錯誤修正， [請參閱版本](./release-notes.md)資訊。

## <a name="next-steps"></a>後續步驟

* [探索使用者流程](./usage-flows.md) ，以瞭解使用者如何流覽您的應用程式。
* [設定快照集集合](./snapshot-debugger.md) ，以查看擲回例外狀況時的原始程式碼和變數狀態。
* [使用 API](./api-custom-events-metrics.md) 來傳送您自己的事件和計量，以深入瞭解您的應用程式效能和使用量。
* 使用[可用性測試](./monitor-web-app-availability.md)持續從世界各地檢查您的應用程式。
* [ASP.NET Core 中的相依性插入](/aspnet/core/fundamentals/dependency-injection)
