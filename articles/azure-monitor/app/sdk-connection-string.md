---
title: Azure 應用程式 Insights 中的連接字串 |Microsoft Docs
description: 如何使用連接字串。
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: f4227c28329233c7f414c6c45e4a3c1420bf47be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335155"
---
# <a name="connection-strings"></a>連接字串

## <a name="overview"></a>概觀

連接字串可讓應用程式深入解析使用者進行單一設定，而不需要多個 proxy 設定。 非常適用于想要將資料傳送至監視服務的內部網路 web 伺服器、主權或混合式雲端環境。

金鑰值組提供簡單的方法，讓使用者為每個 Application Insights (AI) 服務/產品定義前置詞尾碼組合。

> [!IMPORTANT]
> 我們不建議同時設定連接字串和檢測金鑰。 如果使用者同時設定了這兩個專案，就會優先採用最後一個設定。 


## <a name="scenario-overview"></a>案例概觀 

我們會以最大的影響來視覺化這種情況的客戶案例：

- 防火牆例外或 proxy 重新導向 

    在需要監視內部網路 web 伺服器的情況下，我們先前的解決方案會要求客戶將個別的服務端點新增至您的設定。 如需詳細資訊，請參閱[這裡](../faq.md#can-i-monitor-an-intranet-web-server)。 
    連接字串藉由將這項工作縮減為單一設定，提供更好的替代方案。 簡單的前置詞（尾碼修訂）可將所有端點的自動填入和重新導向至正確的服務。 

- 主權或混合式雲端環境

    使用者可以將資料傳送至已定義的 [Azure Government 區域](../../azure-government/compare-azure-government-global-azure.md#application-insights)。
    連接字串可讓您定義內部網路伺服器或混合式雲端設定的端點設定。 

## <a name="getting-started"></a>開始使用

### <a name="finding-my-connection-string"></a>尋找我的連接字串？

您的連接字串會顯示在 Application Insights 資源的總覽分頁中。

![總覽分頁上的連接字串](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>結構描述

#### <a name="max-length"></a>最大長度

連接的最大支援長度為4096個字元。

#### <a name="key-value-pairs"></a>機碼值組

連接字串是由以分號分隔的索引鍵/值組所表示的設定清單所組成： `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>語法

- `InstrumentationKey` (例如： 00000000-0000-0000-0000-000000000000) 連接字串是 **必要** 欄位。
- `Authorization` (例如： ikey)  (這項設定是選擇性的，因為今天我們只支援 ikey 授權。 ) 
- `EndpointSuffix` (範例： applicationinsights.azure.cn) 設定端點尾碼將會指示要連接的 Azure 雲端 SDK。 SDK 會組合個別服務的其餘端點。
- 明確的端點。
  任何服務都可以在連接字串中明確覆寫。
   - `IngestionEndpoint` (例如： `https://dc.applicationinsights.azure.com`) 
   - `LiveEndpoint` (例如： `https://live.applicationinsights.azure.com`) 
   - `ProfilerEndpoint` (例如： `https://profiler.applicationinsights.azure.com`) 
   - `SnapshotEndpoint` (例如： `https://snapshot.applicationinsights.azure.com`) 

#### <a name="endpoint-schema"></a>端點架構

`<prefix>.<suffix>`
- Prefix：定義服務。 
- 尾碼：定義通用功能變數名稱。

##### <a name="valid-suffixes"></a>有效的尾碼

以下是有效尾碼的清單 
- applicationinsights.azure.cn
- applicationinsights.us


另請參閱：https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>有效的首碼

- [遙測](./app-insights-overview.md)內嵌： `dc`
- [即時計量](./live-stream.md)： `live`
- [Profiler](./profiler-overview.md)： `profiler`
- [快照](./snapshot-debugger.md)集： `snapshot`



## <a name="connection-string-examples"></a>連接字串範例


### <a name="minimal-valid-connection-string"></a>基本有效的連接字串

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

在此範例中，只會設定檢測金鑰。

- 授權配置預設為 "ikey" 
- 檢測金鑰：00000000-0000-0000-0000-000000000000
- 區域服務 Uri 是以 [SDK 預設值](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) 為基礎，並且會連接到公用全域 Azure：
   - 攝入： `https://dc.services.visualstudio.com/`
   - 即時計量： `https://rt.services.visualstudio.com/`
   - 分析器： `https://agent.azureserviceprofiler.net/`
   - 調試： `https://agent.azureserviceprofiler.net/`



### <a name="connection-string-with-endpoint-suffix"></a>具有端點尾碼的連接字串

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

在此範例中，此連接字串會指定端點尾碼，而 SDK 將會建立服務端點。

- 授權配置預設為 "ikey" 
- 檢測金鑰：00000000-0000-0000-0000-000000000000
- 區域服務 Uri 是以提供的端點尾碼為基礎： 
   - 攝入： `https://dc.ai.contoso.com`
   - 即時計量： `https://live.ai.contoso.com`
   - 分析器： `https://profiler.ai.contoso.com`
   - 調試： `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>具有明確端點覆寫的連接字串 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

在此範例中，這個連接字串會指定每個服務的明確覆寫。 SDK 將會使用提供的正確端點，而不需要修改。

- 授權配置預設為 "ikey" 
- 檢測金鑰：00000000-0000-0000-0000-000000000000
- 區域服務 Uri 以明確覆寫值為基礎： 
   - 攝入： `https://custom.com:111/`
   - 即時計量： `https://custom.com:222/`
   - 分析器： `https://custom.com:333/`
   - 調試： `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>如何設定連接字串

下列 SDK 版本支援連接字串：
- .NET 和 .NET Core v 2.12。0
- JAVA 2.5.1 和 JAVA 3。0
- JavaScript v 2.3。0
- NodeJS，1.5.0 版
- Python 1.0.0 版

您可以在程式碼、環境變數或設定檔中設定連接字串。



### <a name="environment-variable"></a>環境變數

- 連接字串： `APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

TelemetryConfiguration ConnectionString： https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.NET 明確設定：
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET 設定檔：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore 明確設定：
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.js： 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


JAVA (2.5. x) 明確設定：
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

重要事項： JavaScript 不支援使用環境變數。

使用程式碼片段：

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


手動設定：
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

我們建議使用者設定環境變數。

若要明確設定連接字串：

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>後續步驟

在執行階段開始使用︰

* [由 Azure VM 和 Azure 虛擬機器擴展集 IIS 所裝載的應用程式](./azure-vm-vmss-apps.md)
* [IIS 伺服器](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

在開發階段開始使用︰

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

