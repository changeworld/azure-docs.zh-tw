---
title: Azure 應用程式見解中的連接字串 |微軟文檔
description: 如何使用連接字串。
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 7b049c04913d3415074f46b9d90ec34be874a2da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136701"
---
# <a name="connection-strings"></a>連接字串

## <a name="overview"></a>總覽

連接字串為應用程式洞察使用者提供單個配置設置，無需多個代理設置。 對於希望將資料發送到監視服務的 Intranet Web 服務器、主權或混合雲環境非常有用。

鍵值對為使用者提供了一種為每個應用程式見解 （AI） 服務/產品定義首碼尾碼組合的簡單方法。

> [!IMPORTANT]
> 我們不建議同時設置連接字串和檢測金鑰。 如果使用者同時設置兩者，則以上次設置者為准。 


## <a name="scenario-overview"></a>案例概觀 

我們視覺化的為客戶情景具有最大影響：

- 防火牆異常或代理重定向 

    如果需要對 Intranet Web 服務器進行監視，我們較早的解決方案要求客戶將單個服務終結點添加到您的配置中。 如需詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server)。 
    通過將此工作量減少到單個設置，連接字串提供了更好的替代方法。 一個簡單的首碼，尾碼修正允許自動填滿和重定向所有終結點到正確的服務。 

- 主權雲或混合雲環境

    使用者可以將資料發送到定義的 Azure[政府區域](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)。
    連接字串允許您為 Intranet 伺服器或混合雲設置定義終結點設置。 

## <a name="getting-started"></a>開始使用

### <a name="finding-my-connection-string"></a>正在查找連接字串？

連接字串顯示在應用程式見解資源的"概述"邊欄選項卡上。

![概述邊欄選項卡上的連接字串](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>結構描述

#### <a name="max-length"></a>最大長度

連接的最大支援長度為 4096 個字元。

#### <a name="key-value-pairs"></a>索引鍵/值組

連接字串由表示為按分號分隔的鍵值對的設置清單組成：`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>語法

- `InstrumentationKey`（如：00000-000-000-000-00000000000000） 連接字串是**必填**欄位。
- `Authorization`（例如：伊基）（此設置是可選的，因為今天我們僅支援 ikey 授權。
- `EndpointSuffix`（例如：applicationinsights.azure.cn）設置終結點尾碼將指示要連接到哪個 Azure 雲的 SDK。 SDK 將組裝各個服務的終結點的其餘部分。
- 顯式終結點。
  可以在連接字串中顯式重寫任何服務。
   - `IngestionEndpoint`（例如：https://dc.applicationinsights.azure.com)
   - `LiveEndpoint`（例如：https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint`（例如：https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint`（例如：https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>端點架構

`<prefix>.<suffix>`
- 首碼：定義服務。 
- 尾碼：定義公共功能變數名稱。

##### <a name="valid-suffixes"></a>有效的尾碼

下面是有效的尾碼清單 
- applicationinsights.azure.cn
- applicationinsights.us


另請參閱：https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>有效的首碼

- [遙測引入](./app-insights-overview.md)：`dc`
- [即時指標](./live-stream.md)：`live`
- [探測器](./profiler-overview.md)：`profiler`
- [快照](./snapshot-debugger.md)：`snapshot`



## <a name="connection-string-examples"></a>連接字串示例


### <a name="minimal-valid-connection-string"></a>最小有效連接字串

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

在此示例中，僅設置了檢測金鑰。

- 授權方案預設為"ikey" 
- 儀器檢測金鑰：0000000-0000-0000-000000000000000000
- 區域服務 URI 基於 SDK[預設值](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6)，並將連接到公共全域 Azure：
   - 攝入：https://dc.services.visualstudio.com/
   - 即時指標：https://rt.services.visualstudio.com/
   - 分析器：https://agent.azureserviceprofiler.net/
   - 調試：https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>具有終結點尾碼的連接字串

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

在此示例中，此連接字串指定終結點尾碼，SDK 將構造服務終結點。

- 授權方案預設為"ikey" 
- 儀器檢測金鑰：0000000-0000-0000-000000000000000000
- 區域服務 URI 基於提供的終結點尾碼： 
   - 攝入：https://dc.ai.contoso.com
   - 即時指標：https://live.ai.contoso.com
   - 分析器：https://profiler.ai.contoso.com 
   - 調試：https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>具有顯式終結點覆蓋的連接字串 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

在此示例中，此連接字串為每個服務指定顯式覆蓋。 SDK 將使用未經修改提供的確切終結點。

- 授權方案預設為"ikey" 
- 儀器檢測金鑰：0000000-0000-0000-000000000000000000
- 區域服務 URI 基於顯式覆蓋值： 
   - 攝入： HTTPs：\//custom.com:111/
   - 即時指標： HTTPs：\//custom.com:222/
   - 探測器： HTTPs：\//custom.com:333/ 
   - 調試器： HTTPs：\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>如何設置連接字串

以下 SDK 版本支援連接字串：
- .NET 和 .NET 核心 v2.12.0
- JAVA v2.5.1
- JAVAscript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

連接字串可以通過代碼、環境變數或設定檔進行設置。



### <a name="environment-variable"></a>環境變數

- 連接字串： `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="net-sdk-example"></a>.網路 SDK 示例

遙測配置.連接字串：https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net 顯式設置：
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.淨設定檔：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore 配置.json： 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


### <a name="java-sdk-example"></a>JAVA SDK 示例


JAVA 顯式設置：
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

### <a name="javascript-sdk-example"></a>JAVAscript SDK 示例

重要提示：JAVAscript 不支援使用環境變數。

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


手動設置：
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="node-sdk-example"></a>節點 SDK 示例

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

### <a name="python-sdk-example"></a>Python SDK 示例

我們建議使用者設置環境變數。

要顯式設置連接字串：

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>後續步驟

在執行階段開始使用︰

* [由 Azure VM 和 Azure 虛擬機器擴展集 IIS 所裝載的應用程式](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS 伺服器](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps ](../../azure-monitor/app/azure-web-apps.md)

在開發階段開始使用︰

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [JAVA](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
