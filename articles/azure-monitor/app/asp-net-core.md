---
title: ASP.NET核心應用程式的 Azure 應用程式見解 |微軟文件
description: 監視 ASP.NET Core Web 應用程式的可用性、效能和使用方式。
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 1a9a81d76df7f14fb99b8521e7bfa2edff6c9e9e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687379"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET核心應用程式的應用程式見解

本文介紹如何為[ASP.NET核心](https://docs.microsoft.com/aspnet/core)應用程式啟用應用程式見解。 完成本文中的說明後,應用程式見解將從ASP.NET核心應用程式收集請求、依賴項、異常、性能計數器、檢測信號和日誌。

我們將在這裡使用的範例是一`netcoreapp2.2`個的[MVC 應用程式](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app)。 您可以將這些說明應用於所有ASP.NET核心應用程式。

## <a name="supported-scenarios"></a>支援的案例

[ASP.NET核心應用程式見解 SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)可以監視應用程式,無論應用程式在何處或如何運行。 如果應用程式正在運行並且與 Azure 具有網路連接,則可以收集遙測數據。 支援應用程式見解監視無處不在.NET Core。 支援涵蓋:
* **操作系統**:Windows、Linux 或 Mac。
* **託管方法**:在進程或進程外。
* **部署方法**:框架相關或自包含。
* **網路伺服器**:IIS(互聯網資訊伺服器)或凱斯特雷爾。
* **託管平臺**:Azure 應用服務、Azure VM、Docker、Azure 庫伯奈斯服務 (AKS) 等的 Web 應用功能。
* **.NET 核心執行時版本**: 1.XX、 2.XX 或 3.XX
* **IDE**:視覺工作室、VS 代碼或命令列。

> [!NOTE]
> 如果您使用的是ASP.NET酷睿 3.X 以及應用程式見解,請使用[2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0)版本或更高版本。 這是支援ASP.NET酷3.X的唯一版本。

## <a name="prerequisites"></a>Prerequisites

- 一個功能正常的ASP.NET核心應用程式。 如果需要建立 ASP.NET 核心應用程式,請按照此[ASP.NET 核心教程](https://docs.microsoft.com/aspnet/core/getting-started/)。
- 有效的應用程式見解檢測密鑰。 此密鑰是向應用程式見解發送任何遙測數據所必需的。 如果需要建立新的應用程式見解資源來取得偵測金鑰,請參閱[建立應用程式見解資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>開啟應用程式見解伺服器端遙測(視覺化工作室)

1. 在 Visual Studio 中，開啟您的專案。

    > [!TIP]
    > 如果需要,可以為專案設置原始程式碼管理,以便追蹤應用程式見解所做的所有更改。 要啟用原始碼管理,請選擇「**檔案** > **新增到源碼管理**」 。

2. 選擇**專案** > **新增應用程式來解遙測**。

3. 選擇 **「開始」。** 此選擇的文本可能會有所不同,具體取決於您的視覺工作室版本。 某些早期版本使用 **「免費開始」** 按鈕。

4. 選取您的訂用帳戶。 然後選擇**資源** > **註冊**。

5. 將應用程式見解添加到專案後,請檢查以確認您使用的是最新版本的穩定 SDK。 跳到**專案** > **管理 NuGet 套件** > **微軟.應用程式見解.AspNetCore**. 如果需要,請選擇 **"更新**"。

     ![顯示在何處選擇應用程式來解包以更新的螢幕擷取](./media/asp-net-core/update-nuget-package.png)

6. 如果您遵循選擇的提示並將專案加入到原始碼管理,請轉到**檢視** > **團隊資源管理員** > **。** 然後選擇每個檔以查看應用程式見解遙測所做的更改的差異檢視。

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>開啟應用程式見解伺服器端遙測(無視覺化工作室)

1. 安裝[應用程式見解 SDK NuGet 套件 ASP.NET 核心](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)。 我們建議您始終使用最新的穩定版本。 在[開源 GitHub 儲存庫](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)上查找 SDK 的完整發行說明。

    以下代碼示例顯示要添加到專案檔中的`.csproj`更改。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. 新增到`services.AddApplicationInsightsTelemetry();``Startup`類別`ConfigureServices()`的方法,如以下範例所示:

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

3. 設置檢測金鑰。

    儘管可以將檢測密鑰作為 參數提供`AddApplicationInsightsTelemetry`給 ,但我們建議您在配置中指定檢測密鑰。 以下代碼示例演示如何在`appsettings.json`中指定檢測密鑰。 請確保`appsettings.json`在發佈期間複製到應用程式根資料夾。

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

    或者,在以下任一環境變數中指定檢測鍵:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    例如：

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    通常,`APPINSIGHTS_INSTRUMENTATIONKEY`為部署到 Azure Web 應用的應用程式指定檢測密鑰。

    > [!NOTE]
    > 代碼中指定的檢測鍵勝於環境變數`APPINSIGHTS_INSTRUMENTATIONKEY`,它勝於其他選項。

## <a name="run-your-application"></a>執行您的應用程式

運行應用程式並請求它。 遙測數據現在應該流到應用程式見解。 應用程式見解 SDK 會自動收集到應用程式的傳入 Web 請求以及以下遙測數據。

### <a name="live-metrics"></a>即時計量

[即時指標](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)可用於快速驗證應用程式見解監視是否配置正確。 雖然遙測數據可能需要幾分鐘時間才能在門戶和分析中顯示,但即時指標會以近乎即時的方式顯示正在運行的進程的 CPU 使用方式。 它還可以顯示其他遙測數據,如請求、依賴項、跟蹤等。

### <a name="ilogger-logs"></a>ILogger 紀錄

通過`ILogger`嚴重`Warning`性 或更大級別發出的日誌將自動捕獲。 按照[ILogger 文件](ilogger.md#control-logging-level)自定義應用程式見解捕獲的日誌級別。

### <a name="dependencies"></a>相依性

默認情況下啟用依賴項集合。 [本文介紹了](asp-net-dependencies.md#automatically-tracked-dependencies)自動收集的依賴項,並包含執行手動跟蹤的步驟。

### <a name="performance-counters"></a>效能計數器

對ASP.NET核心[中的效能計數器](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)支援是有限的:

* 如果應用程式在 Azure Web 應用 (Windows) 中運行,SDK 版本 2.4.1 和更高版本將收集性能計數器。
* 如果應用程式在 Windows 和目標`NETSTANDARD2.0`中運行 ,SDK 版本 2.7.1 和更高版本將收集性能計數器。
* 對於面向 .NET 框架的應用程式,SDK 的所有版本都支援性能計數器。
* SDK 版本 2.8.0 和更高版本支援 Linux 中的 cpu/記憶體計數器。 Linux 中不支援其他計數器。 Linux(和其他非 Windows 環境)中取得係統計數器的推薦方法是使用[事件計數器](#eventcounter)

### <a name="eventcounter"></a>事件計數器

`EventCounterCollectionModule`默認情況下啟用,它將從 .NET Core 3.X 應用收集一組預設計數器。 [事件計數器](eventcounters.md)教程列出了收集的預設計數器集。 它還具有自定義清單的說明。

## <a name="enable-client-side-telemetry-for-web-applications"></a>為 Web 應用程式啟用客戶端遙測

上述步驟足以幫助您開始收集伺服器端遙測數據。 如果應用程式具有客戶端元件,請按照以下步驟開始收集[使用方式遙測](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)。

1. 在`_ViewImports.cshtml`中,加入入入:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. 在`_Layout.cshtml`中`HtmlHelper`,插入部分`<head>`的末尾,但在任何其他腳本之前插入。 如果要從頁面報告任何自訂 JavaScript 遙測,則在此代碼段後注入它:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
或使用 SDK `FullScript` `ScriptBody` v2.14 的可用 。 如果需要控制標記以設定內容安全原則`<script>`,請使用此選項:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

前面`.cshtml`引用的檔名來自預設 MVC 應用程式樣本。 最終,如果要為應用程式正確啟用用戶端監視,JavaScript 代碼段必須顯示在要監視的應用程式的每個頁面`<head>`的部分中。 通過將 JavaScript 代碼段`_Layout.cshtml`添加到 ,可以實現此應用程式範本的此目標。 

如果專案不包括`_Layout.cshtml`,您仍然可以新增[客戶端監視](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)。 為此,可以將 JavaScript 代碼段添加到控制應用中`<head>`所有頁面的等效檔中。 或者,您可以將代碼段添加到多個頁面,但此解決方案很難維護,我們通常不推薦它。

## <a name="configure-the-application-insights-sdk"></a>設定應用程式見解 SDK

您可以為ASP.NET核心自定義應用程式見解 SDK 以更改預設配置。 應用程式見解ASP.NET SDK 的使用者可能熟悉`ApplicationInsights.config`通過使用`TelemetryConfiguration.Active`或修改 來更改配置。 對於 ASP.NET 核心,您可以更改配置。 將ASP.NET核心 SDK 添加到應用程式中,並使用ASP.NET酷睿內置[依賴項注入進行](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)配置。 在`ConfigureServices()``Startup.cs`類中幾乎對所有配置更改,除非您另有指導。 以下各節提供了更多資訊。

> [!NOTE]
> 在ASP.NET核心應用程式中,不支援通過修改`TelemetryConfiguration.Active`來更改配置。

### <a name="using-applicationinsightsserviceoptions"></a>使用應用程式的語音服務選項

可以通過傳遞`ApplicationInsightsServiceOptions``AddApplicationInsightsTelemetry`到 修改一些常見設置,如本示例中所示:

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

中設定的完整清單`ApplicationInsightsServiceOptions`

|設定 | 描述 | 預設
|---------------|-------|-------
|開啟效能計數器收集模組  | 開啟/停用`PerformanceCounterCollectionModule` | true
|開啟要求追蹤遙測模組   | 開啟/停用`RequestTrackingTelemetryModule` | true
|開啟事件計數器收集模組   | 開啟/停用`EventCounterCollectionModule` | true
|開啟相依追蹤遙測模組   | 開啟/停用`DependencyTrackingTelemetryModule` | true
|啟用應用服務偵測資料模組  |  開啟/停用`AppServicesHeartbeatTelemetryModule` | true
|開啟 Azure 實體資料資料模組   |  開啟/停用`AzureInstanceMetadataTelemetryModule` | true
|開啟快速脈衝測量串流 | 開啟/關閉即時指標功能 | true
|開啟自我調整樣樣 | 開啟/停用自我調整取樣 | true
|啟用偵測訊號 | 啟用/禁用檢測信號功能,該功能定期(預設值 15 分鐘)發送名為"HeartBeatState"的自定義指標,其中包含有關運行時的資訊,如 .NET 版本、Azure 環境資訊(如果適用)。等等。 | true
|新增自動收集公選器 | 啟用/禁用自動收集指標提取器,這是一個遙測處理器,用於在進行採樣之前發送有關請求/依賴項的預聚合指標。 | true
|要求集合選項.追蹤例外 | 啟用/禁用請求集合模組未處理異常跟蹤的報告。 | NETSTANDARD2.0 中的 false(因為異常通過應用程式InsightsLogger提供程式進行跟蹤),否則為 true。

關於最新清單,請參考[的`ApplicationInsightsServiceOptions`可設定設定](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>取樣

ASP.NET核心的應用程式見解 SDK 支援固定利率和自適應採樣。 默認情況下啟用自適應採樣。 

有關詳細資訊,請參閱為[ASP.NET 核心應用程式設定自適應取樣](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)。

### <a name="adding-telemetryinitializers"></a>新增遙測初始化器

如果要定義使用所有遙測傳送的全域屬性,請使用[遙測初始化程式](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer)。

向容器添加`TelemetryInitializer`任何`DependencyInjection`新 元件,如以下代碼所示。 SDK 會自動拾取添加`TelemetryInitializer``DependencyInjection`到 容器中的任何元件。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>刪除遙測初始化器

默認情況下存在遙測初始化器。 要刪除所有或特定的遙測初始化器,請使用以下範例代碼 *,在*呼`AddApplicationInsightsTelemetry()`叫後。

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

可以使用上的擴充`TelemetryConfiguration``AddApplicationInsightsTelemetryProcessor`方法 將自訂遙測處理器加入`IServiceCollection`。 在[高級篩選方案中](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer)使用遙測處理器。 使用以下示例。

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>設定或移除預設遙測模組

應用程式見解使用遙測模組自動收集有關特定工作負載的有用遙測數據,而無需用戶手動跟蹤。

默認情況下啟用以下自動收集模組。 這些模組負責自動收集遙測數據。 您可以關閉或設定它們以更改其預設行為。

* `RequestTrackingTelemetryModule`- 從傳入的 Web 請求收集請求遙測。
* `DependencyTrackingTelemetryModule`- 從傳出 HTTP 調用和 sql 調用收集依賴項遙測。
* `PerformanceCollectorModule`- 收集 Windows 性能計數器。
* `QuickPulseTelemetryModule`- 收集遙測數據以在即時指標門戶中顯示。
* `AppServicesHeartbeatTelemetryModule`- 收集心臟節拍(作為自定義指標發送),有關託管應用程式的 Azure 應用服務環境。
* `AzureInstanceMetadataTelemetryModule`- 收集關於託管應用程式的 Azure VM 環境的心跳(作為自訂指標發送)。
* `EventCounterCollectionModule`- 收集[事件計數器。](eventcounters.md) 此模組是一項新功能,可在 SDK 版本 2.8.0 及更高版本中提供。

要配置任何預設值`TelemetryModule`,請使用`ConfigureTelemetryModule<T>``IServiceCollection`上的擴充方法,如以下範例所示。

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
                module.Counters.Clear();
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

從 2.12.2[`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions)版本 開始,包含禁用任何預設模組的簡單選項。

### <a name="configuring-a-telemetry-channel"></a>設定遙測頻道

預設色版`ServerTelemetryChannel`為 。 您可以重寫它,如以下範例所示。

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

### <a name="disable-telemetry-dynamically"></a>動態禁用遙測

如果要有條件和動態地禁用遙測,則可以在代碼中的任意位置使用ASP.NET`TelemetryConfiguration`核心依賴項注入容器解析實例,並在該容器上`DisableTelemetry`設置 標誌。

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

上述內容不會阻止任何自動收集模組收集遙測數據。 只有通過上述方法禁用向應用程式見解發送遙測數據。 如果不需要特定的自動收集模組,最好[刪除遙測模組](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-application-insights-support-aspnet-core-3x"></a>應用程式見解是否支援ASP.NET核心 3.X?

是。 更新到[應用程式見解 SDK ASP.NET核心](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.8.0 或更高版本。 舊版本的 SDK 不支援 ASP.NET 酷睿 3.X。

此外,如果您[在此處](#enable-application-insights-server-side-telemetry-visual-studio)使用基於 Visual Studio 的說明,請更新至最新版本的 Visual Studio 2019 (16.3.0) 到板載。 早期版本的 Visual Studio 不支援 ASP.NET 酷睿 3.X 應用的自動載入。

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>如何跟蹤未自動收集的遙測數據?

使用建構函數注入`TelemetryClient`獲取的實例,並在其上調用所需`TrackXXX()`的方法。 我們不建議在ASP.NET核心應用程式中創建新`TelemetryClient`實例。 的`TelemetryClient`單個實例`DependencyInjection`已在 容器中註冊,該容器與其餘遙`TelemetryConfiguration`測數據 共用。 僅當新`TelemetryClient`實例需要獨立於遙測的其餘部分的配置時,才建議創建新實例。

下面的範例展示如何追蹤來自控制器的其他遙測數據。

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

有關應用程式見解中的自訂資料報告的詳細資訊,請參閱[應用程式見解自訂指標 API 引用](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)。

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>某些可視化工作室範本使用IWebHostBuilder上的Use應用程式見解() 擴展方法啟用應用程式見解。 此用法是否仍然有效?

雖然擴展方法`UseApplicationInsights()`仍然受支援,但它在應用程式見解 SDK 版本 2.8.0 中標記為過時。 它將在下一個主要版本的 SDK 中刪除。 啟用應用程式見解遙測的建議方法是使用`AddApplicationInsightsTelemetry()`,因為它提供重載來控制某些配置。 此外,在ASP.NET酷睿 3.X 應用`services.AddApplicationInsightsTelemetry()`中, 是啟用應用程式見解的唯一方法。

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>我正在將ASP.NET核心應用程式部署到Web應用。 我是否應該仍然啟用來自 Web 應用的應用程式見解擴展?

如果 SDK 安裝在產生時間(如本文所示)中所示,則無需從應用程式服務門戶啟用[應用程式見解擴展](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps)。 即使安裝了擴展,當它檢測到 SDK 已添加到應用程式時,它也會後退。 如果從擴展啟用應用程式見解,則不必安裝和更新 SDK。 但是,如果您通過遵循本文中的說明啟用應用程式見解,則具有更大的靈活性,因為:

   * 應用程式見解遙測將繼續在以下方面工作:
       * 所有作業系統,包括 Windows、Linux 和 Mac。
       * 所有發佈模式,包括自包含或依賴於框架。
       * 所有目標框架,包括完整的 .NET 框架。
       * 所有託管選項,包括 Web 應用、VM、Linux、容器、Azure 庫伯奈斯服務和非 Azure 託管。
       * 所有 .NET 核心版本,包括預覽版本。
   * 從 Visual Studio 進行調試時,可以在本地看到遙測數據。
   * 您可以使用`TrackXXX()`API 跟蹤其他自定義遙測。
   * 您可以完全控制配置。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>我能否使用狀態監視器等工具啟用應用程式見解監控?

否。 [狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)和[狀態監視器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)目前僅支援 ASP.NET 4.x。

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>是否自動為我ASP.NET Core 2.0 應用程式啟用應用程式見解?

`Microsoft.AspNetCore.All` 2.0 元包包括應用程式見解 SDK(版本 2.1.0)。 如果在 Visual Studio 調試器下運行應用程式,Visual Studio 將啟用應用程式見解,並在 IDE 本身中本地顯示遙測數據。 除非指定了檢測密鑰,否則遙測不會發送到應用程式見解服務。 我們建議按照本文中的說明啟用應用程式見解,即使對於 2.0 應用也是如此。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果我在 Linux 中運行應用程式,是否支援所有功能?

是。 除以下情況外,所有平臺對 SDK 的功能支援都相同:

* SDK 在 Linux 上收集[事件計數器,](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)因為[性能計數器](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)僅在 Windows 中受支援。 大多數指標都是相同的。
* 即使`ServerTelemetryChannel`默認情況下啟用,如果應用程式在 Linux 或 MacOS 中運行,通道不會自動創建本地儲存資料夾,以臨時保留遙測(如果存在網路問題)。 由於此限制,當存在臨時網路或伺服器問題時,遙測將丟失。 要解決此問題,請為通道設定本地資料夾:

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

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>新的 .NET Core 3.X 輔助服務範本應用程式是否支援此 SDK?

此 SDK`HttpContext`需要 ,因此不適用於任何非 HTTP 應用程式,包括 .NET Core 3.X 輔助服務應用程式。 請參閱[本文檔](worker-service.md),以便使用新發布的 Microsoft.Application Insights.WorkerService SDK 在此類應用程式中啟用應用程式見解。

## <a name="open-source-sdk"></a>開放原始碼 SDK

[閱讀並貢獻代碼](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates)。

## <a name="video"></a>影片

- 檢視此外部分步視訊,從頭開始[使用 .NET 核心和視覺化工作室設定應用程式見解](https://www.youtube.com/watch?v=NoS9UhcR4gA&t)。
- 檢視此外部分步視訊,從頭開始[使用 .NET 核心和視覺化工作室代碼設定應用程式見解](https://youtu.be/ygGt84GDync)。

## <a name="next-steps"></a>後續步驟

* [流覽使用者流](../../azure-monitor/app/usage-flows.md)以瞭解使用者如何流覽你的應用。
* [配置快照集合](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)以查看引發異常時原始碼和變數的狀態。
* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md)發送您自己的事件和指標,以便詳細查看應用的性能和使用方式。
* 使用[可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)持續從世界各地檢查您的應用程式。
* [ASP.NET核心中的相依檔](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
