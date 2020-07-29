---
title: ApplicationInsights.config 參考 - Azure | Microsoft Docs
description: 啟用或停用資料收集模組，以及加入效能計數器和其他參數。
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: ec446190cd589eb511a7a905faeb5f29f31e7d69
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87310475"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>使用 ApplicationInsights.config 或 .xml 設定 Application Insights SDK
Application Insights .NET SDK 是由數個 NuGet 封裝所組成。 [核心封裝](https://www.nuget.org/packages/Microsoft.ApplicationInsights) 提供 API，用於傳送遙測至 Application Insights。 [其他套件](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights)提供遙測*模組*和*初始設定式*，用於自動從您的應用程式和其內容追蹤遙測。 藉由調整設定檔案，您可以啟用或停用遙測模組和初始化運算式，並為其中一部分設定參數。

組態檔的名稱為 `ApplicationInsights.config` 或 `ApplicationInsights.xml`，端視您的應用程式類型而定。 當您[安裝大部分版本的 SDK][start] 時，系統會自動將組態檔加入至您的專案。 根據預設，當使用支援**新增 > Application Insights 遙測**之 Visual Studio 範本專案的自動化體驗時，會在專案根資料夾中建立 ApplicationInsights.config 檔案，並在編譯時將其複製到 bin 資料夾。 它也會藉由[在 IIS 伺服器上狀態監視器][redfield]來新增至 web 應用程式。 如果使用 azure[網站的延伸](azure-web-apps.md)模組或[azure VM 和虛擬機器擴展集的延伸](azure-vm-vmss-apps.md)模組，則會忽略設定檔。

沒有同等的檔案可以控制[網頁中的 SDK][client]。

本文件說明您在組態檔中看到的內容、控制 SDK 元件的方式，以及哪些 NuGet 封裝載入這些元件。

> [!NOTE]
> ApplicationInsights.config 和 .xml 指示不適用 .NET Core SDK。 如需設定 .NET Core 應用程式，請遵循[本](./asp-net-core.md)指南。

## <a name="telemetry-modules-aspnet"></a>遙測模組 (ASP.NET)
每個遙測模組會收集特定類型的資料，並使用核心 API 來傳送資料。 模組由不同的 NuGet 封裝安裝，也會將必要的行加入 .config 檔案。

組態檔中的每個模組都有一個節點。 若要停用模組，請刪除節點或將其註解化。

### <a name="dependency-tracking"></a>相依性追蹤
[相依性追蹤](./asp-net-dependencies.md) 會收集有關您的 app 對資料庫和外部服務和資料庫呼叫的遙測。 若要允許此模組用於 IIS 伺服器，您必須[安裝狀態監視器][redfield]。

您也可以使用 [TrackDependency API](./api-custom-events-metrics.md#trackdependency)撰寫您自己的相依性追蹤程式碼。

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet 封裝。

您可以使用代理程式型（無程式碼） attach，自動收集相依性，而不需要修改程式碼。 若要在 Azure web apps 中使用它，請啟用[Application Insights 延伸](azure-web-apps.md)模組。 若要在 Azure VM 或 Azure 虛擬機器擴展集中使用它，請啟用[VM 和虛擬機器擴展集的應用程式監視延伸](azure-vm-vmss-apps.md)模組。

### <a name="performance-collector"></a>效能收集器
[收集系統效能計數器](./performance-counters.md)，例如 CPU、記憶體和網路負載（從 IIS 安裝）。 您可以指定要收集哪些計數器，包括您自己所設定的效能計數器。

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet 封裝。

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights 診斷遙測
`DiagnosticsTelemetryModule` 報告 Application Insights 檢測程式碼本身中的錯誤。 例如，如果程式碼無法存取效能計數器，或 `ITelemetryInitializer` 擲回例外狀況。 此模組所追蹤的追蹤遙測會出現在[診斷搜尋][diagnostic]中。

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>開發人員模式
`DeveloperModeWithDebuggerAttachedTelemetryModule` 會強制 Application Insights `TelemetryChannel` 立即傳送資料，在偵錯工具附加至應用程式程序時一次傳送一個遙測項目。 這會減少您的應用程式追蹤遙測時，與當遙測出現在 Application Insights 入口網站時之間的時間量。 但是會造成 CPU 和網路頻寬明顯的負擔。

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet 封裝

### <a name="web-request-tracking"></a>Web 要求追蹤
報告 HTTP 要求的 [回應時間和結果碼](../../azure-monitor/app/asp-net.md) 。

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 封裝。

### <a name="exception-tracking"></a>例外狀況追蹤
`ExceptionTrackingTelemetryModule` 追蹤 Web 應用程式中未處理的例外狀況。 請參閱[失敗和例外狀況][exceptions]。

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 封裝。
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-追蹤未觀察到工作例外狀況
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - 追蹤背景工作角色、Windows 服務和主控台應用程式的未處理例外狀況。
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)NuGet 套件。

### <a name="eventsource-tracking"></a>EventSource 追蹤
`EventSourceTelemetryModule` 可讓您設定要傳送至 Application Insights 作為追蹤的 EventSource 事件。 如需追蹤 EventSource 事件的資訊，請參閱[使用 EventSource 事件](./asp-net-trace-logs.md#use-eventsource-events)。

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW 事件追蹤
`EtwCollectorTelemetryModule` 可讓您設定要傳送至 Application Insights 作為追蹤的 ETW 提供者事件。 如需追蹤 ETW 事件的資訊，請參閱[使用 ETW 事件](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events)。

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Microsoft.ApplicationInsights 封裝提供 SDK 的 [核心 API](/dotnet/api/microsoft.applicationinsights?view=azure-dotnet) 。 其他遙測模組使用此，而且您也可以[使用它來定義您自己的遙測](./api-custom-events-metrics.md)。

* ApplicationInsights.config 中沒有項目。
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet 封裝。 如果您只安裝此 NuGet，不會產生任何 .config 檔案。

## <a name="telemetry-channel"></a>遙測通道
[遙測通道](telemetry-channels.md)會管理將遙測緩衝處理和傳輸到 Application Insights 服務。

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`是 web 應用程式的預設通道。 它會緩衝記憶體中的資料，並採用重試機制和本機磁片儲存體，以提供更可靠的遙測傳遞。
* `Microsoft.ApplicationInsights.InMemoryChannel`是輕量遙測通道，如果未設定任何其他通道，則會使用此通道。 

## <a name="telemetry-initializers-aspnet"></a>遙測初始設定式 (ASP.NET)
遙測初始化運算式會設定與每個遙測專案一起傳送的內容屬性。

您可以 [撰寫您自己的初始設定式](./api-filtering-sampling.md#add-properties) 設定內容屬性。

標準的初始設定式已由 Web 或 WindowsServer NuGet 封裝設定：

* `AccountIdTelemetryInitializer` 設定 AccountId 屬性。
* `AuthenticatedUserIdTelemetryInitializer` 如 JavaScript SDK 設定般設定 AuthenticatedUserId 屬性。
* 針對具有從 Azure 執行階段環境擷取之資訊的所有遙測項目，`AzureRoleEnvironmentTelemetryInitializer` 會更新 `Device` 內容的 `RoleName` 和 `RoleInstance` 屬性。
* 針對具有從 MS 組建所產生之 `BuildInfo.config` 檔案擷取值的所有遙測項目，`BuildInfoConfigComponentVersionTelemetryInitializer` 會更新 `Component` 內容的 `Version` 屬性。
* `ClientIpHeaderTelemetryInitializer` 會根據要求的 `X-Forwarded-For` HTTP 標頭來更新所有遙測項目之 `Location` 內容的 `Ip` 屬性。
* `DeviceTelemetryInitializer` 會更新所有遙測項目 `Device` 內容的下列屬性。
  * `Type` 設定為 "PC"
  * `Id` 設定為 Web 應用程式執行所在電腦的網域名稱。
  * `OemName` 設定為使用 WMI 從 `Win32_ComputerSystem.Manufacturer` 欄位擷取的值。
  * `Model` 設定為使用 WMI 從 `Win32_ComputerSystem.Model` 欄位擷取的值。
  * `NetworkType` 設定為從 `NetworkInterface` 擷取的值。
  * `Language` 設定為 `CurrentCulture` 的名稱。
* 針對具有 Web 應用程式執行所在電腦之網域名稱的所有遙測項目，`DomainNameRoleInstanceTelemetryInitializer` 會更新 `Device` 內容的 `RoleInstance` 屬性。
* `OperationNameTelemetryInitializer` 會根據 HTTP 方法，以及 ASP.NET MVC 控制器的名稱和叫用來處理要求的動作，更新所有遙測項目 `RequestTelemetry` 之 `Name` 屬性和 `Operation` 內容的 `Name` 屬性。
* `OperationIdTelemetryInitializer` 或 `OperationCorrelationTelemetryInitializer` 在處理具有自動產生的 `RequestTelemetry.Id` 的要求時，會更新追蹤的所有遙測項目的 `Operation.Id` 內容屬性。
* 針對具有從使用者瀏覽器中執行的 Application Insights JavaScript 檢測程式碼所產生之 `ai_session` Cookie 擷取值的所有遙測項目，`SessionTelemetryInitializer` 會更新 `Session` 內容的 `Id` 屬性。
* `SyntheticTelemetryInitializer` 或 `SyntheticUserAgentTelemetryInitializer` 在處理來自綜合來源 (例如可用性測試或搜尋引擎 Bot) 的要求時，會更新追蹤的所有遙測項目的 `User`、`Session` 和 `Operation` 內容屬性。 根據預設， [計量瀏覽器](../platform/metrics-charts.md) 不會顯示綜合的遙測。

    `<Filters>` 會設定要求的識別屬性。
* 針對具有從使用者瀏覽器中執行之 Application Insights JavaScript 檢測程式碼所產生的 `ai_user` Cookie 擷取值的所有遙測項目，`UserTelemetryInitializer` 會更新 `User` 內容的 `Id` 和 `AcquisitionDate` 屬性。
* `WebTestTelemetryInitializer`針對來自[可用性測試](./monitor-web-app-availability.md)的 HTTP 要求，設定使用者識別碼、會話識別碼和綜合來源屬性。
  `<Filters>` 會設定要求的識別屬性。

針對 Service Fabric 中執行的 .NET 應用程式，您可以包含 `Microsoft.ApplicationInsights.ServiceFabric` NuGet 套件。 此套件包含的 `FabricTelemetryInitializer` 會將 Service Fabric 屬性新增至遙測項目。 如需詳細資訊，請參閱 [GitHub 頁面](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md)了解這個 NuGet 套件所新增之屬性的相關資訊。

## <a name="telemetry-processors-aspnet"></a>遙測處理器 (ASP.NET)
遙測處理器可以在從 SDK 傳送至入口網站之前，篩選和修改每個遙測專案。

您可以[撰寫自己的遙測處理器](./api-filtering-sampling.md#filtering)。

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>彈性取樣遙測處理器（來自 2.0.0-Beta3）
此選項預設為啟用狀態。 如果您的應用程式傳送許多遙測，此處理器會移除其中一些遙測。

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

參數會提供演算法嘗試要達成的目標。 每個 SDK 執行個體都獨立運作，因此如果您的伺服器是數個機器的叢集，遙測的實際數量會隨之加乘。

[深入瞭解取樣](./sampling.md)。

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>固定速率取樣遙測處理器（來自 2.0.0-Beta1）
另外還有標準[取樣遙測處理器](./api-filtering-sampling.md)（來自2.0.1）：

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>InstrumentationKey
這會決定顯示您資料的 Application Insights 資源。 通常您會針對每個應用程式，用個別的金鑰建立個別資源。

如果想要以動態方式設定金鑰 (例如，想要將應用程式的結果傳送到不同的資源)，您可以在組態檔中省略金鑰，並將金鑰設定在程式碼中。

設定 TelemetryClient 的所有實例的索引鍵，包括標準遙測模組。 請在初始化方法中這麼做，例如 ASP.NET 服務中的 global.aspx.cs：

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

如果您只想將特定的一組事件傳送至不同的資源，可以針對特定的 TelemetryClient 設定金鑰：

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

若要取得新的金鑰，請[在 Application Insights 入口網站中建立新的資源][new]。



## <a name="applicationid-provider"></a>ApplicationId 提供者

_從 v2.6.0 開始可供使用_

提供者的用途是要查閱以檢測金鑰為基礎的應用程式識別碼。 應用程式識別碼包含在 RequestTelemetry 和 DependencyTelemetry 中，並且用來判斷入口網站中的相互關聯。

可藉由在程式碼或組態中設定 `TelemetryConfiguration.ApplicationIdProvider` 來使用此功能。

### <a name="interface-iapplicationidprovider"></a>介面：IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


我們在 [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) SDK 中提供兩個實作：`ApplicationInsightsApplicationIdProvider` 和 `DictionaryApplicationIdProvider`。

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

這是我們設定檔 API 的包裝函式。 它會對要求進行節流處理並快取結果。

當您安裝 [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) 或 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 時，此提供者會新增至組態檔

此類別具有選擇性屬性 `ProfileQueryEndpoint`。
依預設，這會設定為 `https://dc.services.visualstudio.com/api/profiles/{0}/appId`。
如果您需要為此組態設定 Proxy，我們建議對基底位址 (Base Address) 使用 Proxy 並包括 "/api/profiles/{0}/appId"。 請注意，在每個要求的執行階段上，'{0}' 會替代為檢測金鑰。

#### <a name="example-configuration-via-applicationinsightsconfig"></a>透過 ApplicationInsights.config 的範例組態：
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>透過程式碼的範例組態：
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

這是靜態提供者，將依賴您設定的檢測金鑰/應用程式識別碼配對。

此類別具有 `Defined` 屬性，也就是檢測金鑰與應用程式識別碼配對的「字典<string,string>」。

此類別具有選擇性的 `Next` 屬性，可在您組態中不存在要求的檢測金鑰時，用來設定其他提供者。

#### <a name="example-configuration-via-applicationinsightsconfig"></a>透過 ApplicationInsights.config 的範例組態：
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>透過程式碼的範例組態：
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>後續步驟
[深入了解 API][api]。

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: ./asp-net-exceptions.md
[netlogs]: ./asp-net-trace-logs.md
[new]: ./create-new-resource.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

