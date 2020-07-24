---
title: Azure 應用程式 Insights 覆寫預設 SDK 端點
description: 針對 Azure Government 之類的區域，修改預設 Azure 監視器 Application Insights SDK 端點。
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: references_regions
ms.openlocfilehash: 50a072cd7e509642c36c783e3cc0fd78e4d5adc0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092859"
---
# <a name="application-insights-overriding-default-endpoints"></a>覆寫預設端點 Application Insights

若要從 Application Insights 將資料傳送到特定區域，您必須覆寫預設端點位址。 每個 SDK 都需要稍微不同的修改，這全都會在本文中說明。 這些變更需要調整範例程式碼，並將、和的預留位置值取代為 `QuickPulse_Endpoint_Address` `TelemetryChannel_Endpoint_Address` `Profile_Query_Endpoint_address` 您特定區域的實際端點位址。 本文結尾包含需要此設定之區域的端點位址連結。

> [!NOTE]
> [連接字串](./sdk-connection-string.md?tabs=net)是在 Application Insights 內設定自訂端點的新慣用方法。

---

## <a name="sdk-code-changes"></a>SDK 程式碼變更

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> 每當執行 SDK 升級時，就會自動覆寫 applicationinsights.config 檔案。 執行 SDK 升級之後，請務必重新輸入區域特定的端點值。

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

如下所示修改專案中檔案的 appsettings.js，以調整主要端點：

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

即時計量和設定檔查詢端點的值只能透過程式碼來設定。 若要透過程式碼覆寫所有端點值的預設值，請在檔案的方法中進行下列變更 `ConfigureServices` `Startup.cs` ：

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure Functions](#tab/functions)

針對 Azure Functions，現在建議使用在函式的應用程式設定中設定的[連接字串](./sdk-connection-string.md?tabs=net)。 若要從 [函式] 窗格記憶體取函式的應用程式設定，請選取 [**設定**] [  >  **Configuration**  >  **應用程式設定**] 

名稱： `APPLICATIONINSIGHTS_CONNECTION_STRING` 值：`Connection String Value`

# <a name="java"></a>[Java](#tab/java)

修改 applicationinsights.xml 檔案，以變更預設的端點位址。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

修改檔案 `application.properties` 並新增：

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

您也可以透過環境變數來設定端點：

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

# <a name="javascript"></a>[JavaScript](#tab/js)

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

# <a name="python"></a>[Python](#tab/python)

如需修改 opencensus-python SDK 內嵌端點的指引，請參閱[opencensus-python](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)存放庫。

---

## <a name="regions-that-require-endpoint-modification"></a>需要修改端點的區域

目前只有要求端點修改的區域會[Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights)和[Azure 中國](/azure/china/resources-developer-guide)。

|區域 |  端點名稱 | 值 |
|-----------------|:------------|:-------------|
| Azure 中國 | 遙測通道 | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure 中國 | QuickPulse （即時計量） |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure 中國 | 設定檔查詢 |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | 遙測通道 |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse （即時計量） |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | 設定檔查詢 |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

如果您目前使用的是通常透過 ' api.applicationinsights.io ' 存取的[Application Insights REST API](https://dev.applicationinsights.io/
) ，您將需要使用區域的本機端點：

|區域 |  端點名稱 | 值 |
|-----------------|:------------|:-------------|
| Azure 中國 | REST API | `api.applicationinsights.azure.cn` |
| Azure Government | REST API | `api.applicationinsights.us`|

> [!NOTE]
> 在這些區域中，**目前不支援**無程式碼以代理程式/擴充功能為基礎的 Azure App 服務監視。 一旦推出此功能，將會更新本文。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 Azure Government 的自訂修改，請參閱[Azure 監視和管理](../../azure-government/compare-azure-government-global-azure.md#application-insights)的詳細指導方針。
- 若要深入瞭解 Azure 中國，請參閱[Azure 中國](/azure/china/)腳本。
