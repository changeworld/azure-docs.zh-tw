---
title: Application Insights 遙測相互關聯 | Microsoft Docs
description: Application Insights 遙測相互關聯
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 2e862410e2bf12e09e1a6388bbb6f7105b5b2edf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405258"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights 中的遙測相互關聯

在微服務的世界裡，每個邏輯作業都需要在服務的各種元件中完成工作。 您可以使用[Application Insights](../../azure-monitor/app/app-insights-overview.md)分別監視每個元件。 Application Insights 支援分散式遙測相互關聯，這可供您用來偵測造成失敗或效能衰退的元件。

本文說明 Application Insights 所使用的資料模型，以關聯多個元件所傳送的遙測。 它涵蓋內容傳播技巧和通訊協定。 它也涵蓋不同語言和平臺上的相互關聯策略的執行。

## <a name="data-model-for-telemetry-correlation"></a>遙測相互關聯的資料模型

Application Insights 會定義分散遙測相互關聯的[資料模型](../../azure-monitor/app/data-model.md)。 為了讓遙測與邏輯作業產生關聯，每個遙測專案都有一個`operation_Id`稱為的內容欄位。 每個遙測項目會在分散式追蹤內共用這個識別碼。 因此，即使您遺失單一層的遙測，仍然可以將其他元件所報告的遙測相關聯。

分散式邏輯作業通常包含一組較小的作業，這是由其中一個元件所處理的要求。 這些作業是由[要求遙測](../../azure-monitor/app/data-model-request-telemetry.md)所定義。 每個要求遙測專案都有`id`自己的唯一和全域識別。 與要求相關聯的所有遙測專案（例如追蹤和例外狀況）都應該將設定`operation_parentId`為要求`id`的值。

每個連出作業 (例如對另一個元件的 HTTP 呼叫) 都會由[相依性遙測](../../azure-monitor/app/data-model-dependency-telemetry.md)代表。 相依性遙測也會定義`id`它自己的全域唯一的。 由這個相依性呼叫起始的要求遙測會使用 這個 `id` 作為其 `operation_parentId`。

您可以將 `operation_Id`、`operation_parentId` 和 `request.id` 與 `dependency.id` 搭配使用，來建置分散式邏輯作業的檢視。 這些欄位也會定義遙測呼叫的因果順序。

在微服務環境中，來自元件的追蹤可能會前往不同的儲存體項目。 每個元件在 Application Insights 中都可能有自己的檢測金鑰。 若要取得邏輯作業的遙測，Application Insights 查詢每個儲存專案的資料。 當儲存體專案數很大時，您將需要有關下一個位置的提示。 Application Insights 資料模型會定義兩個欄位來解決這個問題：`request.source` 和 `dependency.target`。 第一個欄位可識別起始相依性要求的元件。 第二個欄位會識別哪個元件傳回相依性呼叫的回應。

## <a name="example"></a>範例

讓我們看看以下範例。 名為「股票價格」的應用程式會使用稱為「庫存」的外部 API 來顯示股票的目前市場價格。 股票價格應用程式有一個名為 [Stock] 頁面的頁面，而用戶端`GET /Home/Stock`網頁瀏覽器會使用它開啟。 應用程式會使用 HTTP 呼叫`GET /api/stock/value`來查詢股票 API。

您可以執行查詢來分析產生的遙測︰

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

在結果中，請注意，所有遙測項目都共用 `operation_Id` 這個根。 從頁面進行 Ajax 呼叫時，會將新的唯一識別碼（`qJSXU`）指派給相依性遙測，並使用 PAGEVIEW 的識別碼作為。 `operation_ParentId` 接著，伺服器要求會使用 Ajax 識別碼作為 `operation_ParentId`。

| itemType   | 名稱                      | 識別碼           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock 頁面                |              | STYz               | STYz         |
| 相依性 | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| 要求    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| 相依性 | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

對外部服務`GET /api/stock/value`進行呼叫時，您需要知道該伺服器的身分識別，才能適當地設定`dependency.target`欄位。 當外部服務不支援監視時，`target` 會設定為服務的主機名稱 (例如 `stock-prices-api.com`)。 但是，如果服務藉由傳回預先定義的 HTTP 標頭`target`來識別自己，則會包含服務識別，讓 Application Insights 藉由查詢來自該服務的遙測，來建立分散式追蹤。

## <a name="correlation-headers"></a>相互關聯標頭

Application Insights 正在轉換為[W3C 追蹤內容](https://w3c.github.io/trace-context/)，其定義：

- `traceparent`：會攜帶全域唯一的作業識別碼和呼叫的唯一識別碼。
- `tracestate`：攜帶系統特定的追蹤內容。

最新版的 Application Insights SDK 支援追蹤內容通訊協定，但您可能需要加入宣告。 （將會維護 Application Insights SDK 支援的舊版相互關聯通訊協定的回溯相容性）。

相互[關聯 HTTP 通訊協定（也稱為要求識別碼](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)）即將被取代。 此通訊協定會定義兩個標頭：

- `Request-Id`：會攜帶呼叫的全域唯一識別碼。
- `Correlation-Context`：會攜帶分散式追蹤屬性的名稱/值組集合。

Application Insights 也會定義相互關聯 HTTP 通訊協定的[延伸](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md)。 它會使用 `Request-Context` 名稱值組，來傳播立即呼叫端或被呼叫端所使用的屬性集合。 Application Insights SDK 會使用此標頭來設定`dependency.target`和`request.source`欄位。

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>啟用傳統 ASP.NET 應用程式的 W3C 分散式追蹤支援
 
  > [!NOTE]
  >  從`Microsoft.ApplicationInsights.Web`和`Microsoft.ApplicationInsights.DependencyCollector`開始，不需要進行任何設定。

W3C 追蹤內容支援是以後向相容的方式來執行。 相互關聯應使用舊版 SDK 所檢測的應用程式（不含 W3C 支援）。

如果您想要繼續使用舊版`Request-Id`通訊協定，可以使用此設定來停用追蹤內容：

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

如果您執行較舊版本的 SDK，建議您更新它，或套用下列設定以啟用追蹤內容。
從 2.8.0-Beta1 版本開始`Microsoft.ApplicationInsights.Web` ， `Microsoft.ApplicationInsights.DependencyCollector`和套件中都有提供這項功能。
此功能預設為停用。 若要啟用它，請進行下列`ApplicationInsights.config`變更：

- 在`RequestTrackingTelemetryModule`底下，新增`EnableW3CHeadersExtraction`專案，並將其值`true`設定為。
- 在`DependencyTrackingTelemetryModule`底下，新增`EnableW3CHeadersInjection`專案，並將其值`true`設定為。
- 在`W3COperationCorrelationTelemetryInitializer` `TelemetryInitializers`底下新增。 看起來會像這個範例：

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>啟用 ASP.NET Core 應用程式的 W3C 分散式追蹤支援

 > [!NOTE]
  > 從`Microsoft.ApplicationInsights.AspNetCore`版本2.8.0 開始，不需要進行任何設定。
 
W3C 追蹤內容支援是以後向相容的方式來執行。 相互關聯應使用舊版 SDK 所檢測的應用程式（不含 W3C 支援）。

如果您想要繼續使用舊版`Request-Id`通訊協定，可以使用此設定來停用追蹤內容：

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

如果您執行較舊版本的 SDK，建議您更新它，或套用下列設定以啟用追蹤內容。

此功能位於 `Microsoft.ApplicationInsights.AspNetCore` 2.5.0-beta1 版和 `Microsoft.ApplicationInsights.DependencyCollector` 2.8.0-beta1 版中。
此功能預設為停用。 若要啟用它，請將 `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` 設定為 `true`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>啟用 Java 應用程式的 W3C 分散式追蹤支援

#### <a name="java-30-agent"></a>JAVA 3.0 代理程式

  JAVA 3.0 代理程式支援現成的 W3C，不需要額外的設定。 

#### <a name="java-sdk"></a>Java SDK
- **連入設定**

  - 針對 JAVA EE 應用程式，請將下列內容`<TelemetryModules>`新增至 ApplicationInsights 中的標記：

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - 針對春季開機應用程式，新增下列屬性：

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **連出設定**

  將下列程式碼新增至 AI-Agent.xml：

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > 預設會啟用回溯相容性模式，而 `enableW3CBackCompat` 參數則為選擇性。 請只在您想要關閉回溯相容性功能時，才使用它。
  >
  > 理想的情況是，當所有服務都已更新至支援 W3C 通訊協定的新版 SDK 時，您會關閉此功能。 強烈建議您儘快移轉至這些較新版的 SDK。

> [!IMPORTANT]
> 請確定傳入和傳出設定完全相同。

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>啟用 Web 應用程式的 W3C 分散式追蹤支援

這項功能在`Microsoft.ApplicationInsights.JavaScript`中。 此功能預設為停用。 若要啟用它， `distributedTracingMode`請使用 config。AI_AND_W3C 是為了與 Application Insights 所檢測的任何舊版服務回溯相容性而提供。

- **npm 設定（如果使用程式碼片段設定則忽略）**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **程式碼片段設定（如果使用 npm 安裝程式則忽略）**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing 與 Application Insights

[OpenTracing 資料模型規格](https://opentracing.io/)與 Application Insights 資料模型的對應方式如下：

| Application Insights                   | OpenTracing                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `span.kind = server` 的 `Span`                    |
| `Dependency`                           | `span.kind = client` 的 `Span`                    |
| `Request` 和 `Dependency` 的 `Id`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | 類型 `ChildOf` 的 `Reference` (父代範圍)     |

如需詳細資訊，請參閱[Application Insights 遙測資料模型](../../azure-monitor/app/data-model.md)。

如需 OpenTracing 概念的定義，請參閱 OpenTracing[規格](https://github.com/opentracing/specification/blob/master/specification.md)和[語義慣例](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)。

## <a name="telemetry-correlation-in-opencensus-python"></a>OpenCensus Python 中的遙測相互關聯

OpenCensus Python 遵循先前`OpenTracing`所述的資料模型規格。 它也支援[W3C 追蹤內容](https://w3c.github.io/trace-context/)，而不需要任何設定。

### <a name="incoming-request-correlation"></a>連入要求相互關聯

OpenCensus Python 會將 W3C 追蹤內容標頭與來自要求本身所產生之範圍的傳入要求相互關聯。 OpenCensus 會自動使用這些熱門 web 應用程式架構的整合來執行此動作： Flask、Django 和金字塔。 您只需要以[正確的格式](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)填入 W3C 追蹤內容標頭，並將其與要求一起傳送。 以下是範例 Flask 應用程式，示範這種情況：

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

此程式碼會在您的本機電腦上執行範例 Flask 應用程式`8080`，並接聽埠。 若要使追蹤內容相互關聯，您可以將要求傳送至端點。 在此範例中，您可以使用`curl`命令：
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
藉由查看[追蹤內容標頭格式](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)，您可以衍生下列資訊：

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

如果您查看已傳送至 Azure 監視器的要求專案，您可以看到填入追蹤標頭資訊的欄位。 您可以在 Azure 監視器 Application Insights 資源中的 [記錄（分析）] 下找到此資料。

![在記錄中要求遙測（分析）](./media/opencensus-python/0011-correlation.png)

`id`欄位的格式`<trace-id>.<span-id>`為，其中`trace-id`是取自要求中所傳遞的追蹤標頭，而`span-id`是為此範圍產生的8位元組陣列。

`operation_ParentId`欄位的格式`<trace-id>.<parent-id>`為，其中`trace-id`和`parent-id`都取自要求中所傳遞的追蹤標頭。

### <a name="log-correlation"></a>記錄相互關聯

OpenCensus Python 可讓您將追蹤識別碼、span 識別碼和取樣旗標新增至記錄檔記錄，藉以建立記錄的相互關聯。 您可以藉由安裝 OpenCensus[記錄整合](https://pypi.org/project/opencensus-ext-logging/)來新增這些屬性。 下列屬性`LogRecord`會加入至 Python 物件： `traceId`、 `spanId`和。 `traceSampled` 請注意，這只會對在整合後所建立的記錄器生效。

以下是示範這種情況的範例應用程式：

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
當此程式碼執行時，主控台中會列印下列內容：
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
請注意，範圍內`spanId`有記錄訊息的存在。 這與屬於名`spanId` `hello`為的範圍相同。

您可以使用`AzureLogHandler`來匯出記錄檔資料。 如需詳細資訊，請參閱 [本篇文章](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs)。

## <a name="telemetry-correlation-in-net"></a>.NET 中的遙測相互關聯

經過一段時間，.NET 已經定義了數種相互關聯遙測和診斷記錄的方式：

- `System.Diagnostics.CorrelationManager`允許追蹤[LogicalOperationStack 和 ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)。
- `System.Diagnostics.Tracing.EventSource` 與 Windows 事件追蹤 (ETW) 會定義 [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) 方法。
- `ILogger`使用[記錄範圍](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)。
- Windows Communication Foundation (WCF) 與 HTTP 連接起了「目前」的內容傳播。

但這些方法並未啟用自動分散式追蹤支援。 `DiagnosticSource`支援自動跨電腦相互關聯。 .NET 程式庫`DiagnosticSource`支援並允許透過傳輸（例如 HTTP）自動跨電腦傳播相互關聯內容。

中`DiagnosticSource`的[活動使用者指南](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)說明追蹤活動的基本概念。

ASP.NET Core 2.0 支援提取 HTTP 標頭和啟動新的活動。

`System.Net.Http.HttpClient`從版本4.1.0 開始，支援自動插入相互關聯 HTTP 標頭和追蹤 HTTP 呼叫做為活動。

有一個新的 HTTP 模組[microsoft.aspnet.telemetrycorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)，適用于傳統 ASP.NET。 此模組會使用 `DiagnosticSource` 來實作遙測相互關聯。 它會根據連入要求標頭來啟動活動。 它也會將來自不同要求處理階段的遙測相互關聯，即使 Internet Information Services （IIS）處理的每個階段都是在不同的 managed 執行緒上執行也一樣。

Application Insights SDK 從 2.4.0-beta1 版開始，會使用 `DiagnosticSource` 和 `Activity` 來收集遙測，並將它與目前活動建立關聯。

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>JAVA 中的遙測相互關聯

[JAVA 代理程式](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)以及[java SDK](../../azure-monitor/app/java-get-started.md) version 2.0.0 或更新版本都支援遙測的自動相互關聯。 它會自動`operation_id`填入在要求範圍內發出的所有遙測（例如追蹤、例外狀況和自訂事件）。 如果已設定[JAVA SDK 代理程式](../../azure-monitor/app/java-agent.md)，它也會透過 HTTP 傳播服務對服務呼叫的相互關聯標頭（如前文所述）。

> [!NOTE]
> Application Insights JAVA 代理程式會自動收集 JMS、Kafka、Netty/Webflux 等等的要求和相依性。 針對 JAVA SDK，相互關聯功能僅支援透過 Apache HttpClient 所提出的呼叫。 SDK 不支援跨訊息技術（例如 Kafka、RabbitMQ 和 Azure 服務匯流排）的自動內容傳播。 

> [!NOTE]
> 若要收集自訂遙測，您需要使用 JAVA 2.6 SDK 來檢測應用程式。 

### <a name="role-names"></a>角色名稱

您可能想要自訂群組件名稱在[應用程式對應](../../azure-monitor/app/app-map.md)中的顯示方式。 若要這樣做，您可以採取下列`cloud_RoleName`其中一個動作手動設定：

- 針對 Application Insights JAVA 代理程式3.0，請設定雲端角色名稱，如下所示：

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    您也可以使用環境變數`APPLICATIONINSIGHTS_ROLE_NAME`來設定雲端角色名稱。

- 透過 Application Insights JAVA SDK 2.5.0 和更新版本，您可以藉`cloud_RoleName`由將`<RoleName>`新增至 ApplicationInsights，來指定：

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- 如果您搭配使用春季開機與 Application Insights 春季開機 Starter，就只需要在應用程式的 properties 檔案中設定應用程式的自訂名稱：

  `spring.application.name=<name-of-app>`

  彈簧開機 Starter 會自動指派`cloudRoleName`給您為`spring.application.name`屬性輸入的值。

## <a name="next-steps"></a>後續步驟

- 撰寫[自訂遙測](../../azure-monitor/app/api-custom-events-metrics.md)。
- 如需 ASP.NET Core 和 ASP.NET 中的先進相互關聯案例，請參閱[追蹤自訂作業](custom-operations-tracking.md)。
- 深入了解為其他 SDK [設定 cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name)。
- 在 Application Insights 上將微服務的所有元件上線。 查看[支援的平台](../../azure-monitor/app/platforms.md)。
- 參閱[資料模型](../../azure-monitor/app/data-model.md)以了解 Application Insights 類型。
- 了解如何[擴充和篩選遙測](../../azure-monitor/app/api-filtering-sampling.md)。
- 檢閱 [Application Insights 設定參考](configuration-with-applicationinsights-config.md)。
