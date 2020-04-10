---
title: Application Insights 遙測相互關聯 | Microsoft Docs
description: Application Insights 遙測相互關聯
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 6ceace1ee93fab8c0a46ed4a67850fc87a5cdad2
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991223"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights 中的遙測相互關聯

在微服務的世界裡，每個邏輯作業都需要在服務的各種元件中完成工作。 您可以使用[應用程式見解](../../azure-monitor/app/app-insights-overview.md)分別監視每個元件。 Application Insights 支援分散式遙測相互關聯，這可供您用來偵測造成失敗或效能衰退的元件。

本文說明 Application Insights 所使用的資料模型，以關聯多個元件所傳送的遙測。 它涵蓋內容傳播技巧和通訊協定。 它還涵蓋不同語言和平臺上的相關策略的實施。

## <a name="data-model-for-telemetry-correlation"></a>遙測相互關聯的資料模型

Application Insights 會定義分散遙測相互關聯的[資料模型](../../azure-monitor/app/data-model.md)。 要將遙測與邏輯操作相關聯,每個遙測項都有一個上下文字段,稱為`operation_Id`。 每個遙測項目會在分散式追蹤內共用這個識別碼。 因此,即使您從單個圖層丟失遙測數據,您仍可以關聯其他元件報告的遙測數據。

分散式邏輯操作通常由一組較小的操作組成,這些操作是由其中一個元件處理的請求。 這些作業是由[要求遙測](../../azure-monitor/app/data-model-request-telemetry.md)所定義。 每個請求遙測項都有自己的`id`,用於唯一和全域地標識它。 與請求關聯的所有遙測項(如跟蹤和異常)都應`operation_parentId`設置為`id`請求的值。

每個連出作業 (例如對另一個元件的 HTTP 呼叫) 都會由[相依性遙測](../../azure-monitor/app/data-model-dependency-telemetry.md)代表。 依賴項遙測還定義其全域唯`id`一的自身。 由這個相依性呼叫起始的要求遙測會使用 這個 `id` 作為其 `operation_parentId`。

您可以將 `operation_Id`、`operation_parentId` 和 `request.id` 與 `dependency.id` 搭配使用，來建置分散式邏輯作業的檢視。 這些欄位也會定義遙測呼叫的因果順序。

在微服務環境中，來自元件的追蹤可能會前往不同的儲存體項目。 每個元件在 Application Insights 中都可能有自己的檢測金鑰。 要獲取邏輯操作的遙測數據,應用程式見解會查詢每個存儲項中的數據。 當儲存項的數量很大時,您需要提示下一步要查找的位置。 Application Insights 資料模型會定義兩個欄位來解決這個問題：`request.source` 和 `dependency.target`。 第一個字段標識啟動依賴項請求的元件。 第二個字段標識返回依賴項調用回應的元件。

## <a name="example"></a>範例

讓我們看看以下範例。 名為「股票價格」的應用程式使用名為 Stock 的外部 API 顯示股票的當前市場價格。 股票價格應用程式有一個名為 Stock 頁的頁面,用戶端`GET /Home/Stock`Web 瀏覽器使用 打開該頁面。 應用程式使用 HTTP`GET /api/stock/value`調用 查詢股票 API。

您可以執行查詢來分析產生的遙測︰

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

在結果中，請注意，所有遙測項目都共用 `operation_Id` 這個根。 從頁面進行 Ajax 呼叫時,將為其相依項`qJSXU`遙測分配一個新的唯一 ID ( ,`operation_ParentId`pageView 的 ID 用作 。 接著，伺服器要求會使用 Ajax 識別碼作為 `operation_ParentId`。

| itemType   | NAME                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock 頁面                |              | STYz               | STYz         |
| 相依性 | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| 要求    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| 相依性 | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

對外部服務`GET /api/stock/value`進行呼叫時,您需要知道該伺服器的標識,以便可以適當地`dependency.target`設置 該欄位。 當外部服務不支援監視時，`target` 會設定為服務的主機名稱 (例如 `stock-prices-api.com`)。 但是,如果服務通過返回預定義的 HTTP 標頭來`target`標識自身 ,則包含允許應用程式見解通過查詢該服務的遙測來構建分散式跟蹤的服務標識。

## <a name="correlation-headers"></a>相互關聯標頭

應用程式見解正在過渡到[W3C 追蹤上下文](https://w3c.github.io/trace-context/),它定義:

- `traceparent`:攜帶全域唯一的操作 ID 和調用的唯一標識符。
- `tracestate`:攜帶特定於系統的跟蹤上下文。

最新版本的應用程式見解 SDK 支援追蹤上下文協定,但您可能需要選擇加入該協定。 (將保持與應用程式見解 SDK 支援的先前相關協定的向後相容性。

[相關的 HTTP 協定(也稱為請求-ID)](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)正在被棄用。 此協定定義兩個標頭:

- `Request-Id`:攜帶呼叫的全球唯一 ID。
- `Correlation-Context`:攜帶分散式跟蹤屬性的名稱值對集合。

應用程式見解還定義了相關 HTTP 協定的[延伸](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md)。 它會使用 `Request-Context` 名稱值組，來傳播立即呼叫端或被呼叫端所使用的屬性集合。 應用程式見解 SDK 使用此標頭`dependency.target`來`request.source`設置 和 欄位。

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>啟用傳統 ASP.NET 應用程式的 W3C 分散式追蹤支援
 
  > [!NOTE]
  >  從`Microsoft.ApplicationInsights.Web``Microsoft.ApplicationInsights.DependencyCollector`和開始不需要配置。

W3C 跟蹤上下文支援以向後相容的方式實現。 預計關聯版適用於使用早期版本的 SDK(不支援 W3C)進行檢測的應用程式。

如果要繼續使用舊協定`Request-Id`,可以使用以下配置禁用追蹤上下文:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

如果執行舊版本的 SDK,我們建議您更新它或應用以下設定以啟用追蹤上下文。
此功能在`Microsoft.ApplicationInsights.Web``Microsoft.ApplicationInsights.DependencyCollector`和 包中可用,從版本 2.8.0-Beta1 開始。
此功能預設為停用。 要開啟它,要對`ApplicationInsights.config`:

- 在`RequestTrackingTelemetryModule`下,`EnableW3CHeadersExtraction`新增元素並將值值設定`true`為 。
- 在`DependencyTrackingTelemetryModule`下,`EnableW3CHeadersInjection`新增元素並將值值設定`true`為 。
- 在`W3COperationCorrelationTelemetryInitializer``TelemetryInitializers`下新增 。 看起來與此範例類似:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>啟用 ASP.NET Core 應用程式的 W3C 分散式追蹤支援

 > [!NOTE]
  > 從`Microsoft.ApplicationInsights.AspNetCore`版本 2.8.0 開始,無需配置。
 
W3C 跟蹤上下文支援以向後相容的方式實現。 預計關聯版適用於使用早期版本的 SDK(不支援 W3C)進行檢測的應用程式。

如果要繼續使用舊協定`Request-Id`,可以使用以下配置禁用追蹤上下文:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

如果執行舊版本的 SDK,我們建議您更新它或應用以下設定以啟用追蹤上下文。

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

#### <a name="java-30-agent"></a>Java 3.0 代理

  Java 3.0 代理支援 W3C 開箱即用,無需其他配置。 

#### <a name="java-sdk"></a>Java SDK
- **連入設定**

  - 對於 Java EE 應用程式`<TelemetryModules>`,請向應用程式 Insights.xml 中的標記新增以下內容:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - 對於 Spring Boot 應用,新增以下屬性:

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
> 確保傳入和傳出配置完全相同。

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>為 Web 應用啟用 W3C 分散式追蹤支援

此功能在中`Microsoft.ApplicationInsights.JavaScript`。 此功能預設為停用。 要啟用它,請使用`distributedTracingMode`配置。提供AI_AND_W3C與應用程式見解檢測的任何舊服務向後相容。

- **npm 設定(如果使用程式碼設定,請忽略)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **程式碼設定(如果使用 npm 設定,則忽略)**

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

有關詳細資訊,請參閱[應用程式見解遙測資料模型](../../azure-monitor/app/data-model.md)。

有關 OpenTracing 概念的定義,請參閱 OpenTracing[規範與](https://github.com/opentracing/specification/blob/master/specification.md)[語義約定](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)。

## <a name="telemetry-correlation-in-opencensus-python"></a>開放Census Python 中的遙測相關性

OpenCensus Python`OpenTracing`遵循前面概述的數據模型規範。 它還支援[W3C 跟蹤上下文](https://w3c.github.io/trace-context/),無需任何配置。

### <a name="incoming-request-correlation"></a>傳入要求關聯

OpenCensus Python 將傳入請求的 W3C 追蹤上下文標頭與從請求本身生成的範圍相關聯。 OpenCensus 將自動透過這些流行的 Web 應用程式架構的整合來執行此操作:法蘭克、Django 和金字塔。 您只需使用[正確的格式](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)填充 W3C 追蹤上下文標頭,然後隨請求一起發送它們。 下面是一個範例 Flask 應用程式,演示了此:

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

此代碼在本地電腦上執行範例 Flask 應用程式,偵`8080`聽連接埠 。 要關聯跟蹤上下文,請向終結點發送請求。 這個範例中, 可以使用`curl`指令 :
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
通過查看[追蹤上下文標頭格式](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format),可以派生以下資訊:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

如果查看發送到 Azure 監視器的請求條目,可以看到填充跟蹤標頭資訊的欄位。 您可以在 Azure 監視器應用程式見解資源中的日誌(分析)下找到此資料。

![要求遙測(分析)](./media/opencensus-python/0011-correlation.png)

該`id`欄位採用`<trace-id>.<span-id>`格式`trace-id`,其中 從請求中傳遞的追蹤標`span-id`頭獲取 , 並且 是此範圍生成的 8 位元組陣列。

這個`operation_ParentId`欄位以指定的`<trace-id>.<parent-id>`格式為`trace-id`,`parent-id`其中與從請求中傳遞的追蹤標頭中取得與 。

### <a name="log-correlation"></a>記錄相互關聯

OpenCensus Python 使您能夠透過將追蹤 ID、範圍 ID 和取樣標誌添加到日誌記錄來關聯日誌。 您可以通過安裝 OpenCensus[記錄記錄整合](https://pypi.org/project/opencensus-ext-logging/)來新增這些屬性。 以下屬性將`LogRecord`新增到 Python`traceId``spanId``traceSampled`物件: 與 。 請注意,這僅適用於集成後創建的記錄器。

下面是一個範例應用程式,演示了此功能:

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
執行此代碼時,控制台中列印以下內容:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
請注意,存在在範圍內的`spanId`日誌消息的顯示。 這是屬於名為`hello``spanId`的 span 的相同。

可以使用 匯出紀錄資料`AzureLogHandler`。 如需詳細資訊，請參閱 [本篇文章](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs)。

## <a name="telemetry-correlation-in-net"></a>.NET 中的遙測相互關聯

隨著時間的推移,.NET 定義了幾種關聯遙測和診斷日誌的方法:

- `System.Diagnostics.CorrelationManager`允許追蹤[邏輯操作堆疊與活動代碼](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)。
- `System.Diagnostics.Tracing.EventSource` 與 Windows 事件追蹤 (ETW) 會定義 [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) 方法。
- `ILogger`使用[Log 作用網域](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)。
- Windows Communication Foundation (WCF) 與 HTTP 連接起了「目前」的內容傳播。

但是這些方法沒有啟用自動分散式跟蹤支援。 `DiagnosticSource`支援自動跨機關聯。 .NET 函`DiagnosticSource`式庫支援並允許透過傳輸(如 HTTP)自動跨電腦傳播相關上下文。

`DiagnosticSource`中的[「活動使用者指南」](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)解釋了追蹤活動的基礎知識。

ASP.NET Core 2.0 支援提取 HTTP 標頭並啟動新活動。

`System.Net.Http.HttpClient`,從版本 4.1.0 開始,支援自動注入相關 HTTP 標頭,並將 HTTP 調用作為活動進行跟蹤。

有一個新的HTTP模組,[微軟.AspNet.遙測相關](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/),經典ASP.NET。 此模組會使用 `DiagnosticSource` 來實作遙測相互關聯。 它會根據連入要求標頭來啟動活動。 它還關聯請求處理不同階段的遙測數據,即使 Internet 資訊服務 (IIS) 處理的每個階段都運行在不同的託管線程上也是如此。

Application Insights SDK 從 2.4.0-beta1 版開始，會使用 `DiagnosticSource` 和 `Activity` 來收集遙測，並將它與目前活動建立關聯。

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Java 中的遙測相關性

[Java 代理](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)以及[JAVA SDK](../../azure-monitor/app/java-get-started.md)版本 2.0.0 或更高版本支援自動關聯遙測。 它會自動填充`operation_id`在請求範圍內發出的所有遙測數據(如跟蹤、異常和自定義事件)。 如果[配置了 Java SDK 代理](../../azure-monitor/app/java-agent.md),它還通過 HTTP 傳播服務到服務調用的相關標頭(前面所述)。

> [!NOTE]
> 應用程式見解 Java 代理自動收集 JMS、卡夫卡、Netty/Webflux 等的請求和依賴項。 對於 Java SDK,僅支援透過 Apache HttpClient 進行的呼叫,用於相關功能。 SDK 不支援跨消息傳遞技術(如 Kafka、RabbitMQ 和 Azure 服務總線)進行自動上下文傳播。 

> [!NOTE]
> 要收集自定義遙測數據,您需要使用 JAVA 2.6 SDK 對應用程式進行檢測。 

### <a name="role-names"></a>角色名稱

您可能希望自訂元件名稱在[應用程式映射](../../azure-monitor/app/app-map.md)中的顯示方式。 此,您可以透過以下操作之一手動設定`cloud_RoleName`:

- 對於應用程式見解 JAVA 代理 3.0,請設置雲角色名稱,如下所示:

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    您還可以使用環境變數`APPLICATIONINSIGHTS_ROLE_NAME`設置雲角色名稱。

- 使用應用程式的 Java SDK 2.5.0`cloud_RoleName`與更高版本`<RoleName>`, 您可以透過新增到應用程式 Insights.xml 檔來指定 :

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- 如果將 Spring Boot 與應用程式見解彈簧啟動啟動器一起使用,則只需在應用程式中為應用程式設置自定義名稱。

  `spring.application.name=<name-of-app>`

  彈簧啟動器會自動分配給`cloudRoleName`您`spring.application.name`為 屬性輸入的值。

## <a name="next-steps"></a>後續步驟

- 編寫[自訂遙測](../../azure-monitor/app/api-custom-events-metrics.md)。
- 有關 ASP.NET 核心和 ASP.NET 的進階關聯方案,請參閱[追蹤自訂操作](custom-operations-tracking.md)。
- 深入了解為其他 SDK [設定 cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name)。
- 在 Application Insights 上將微服務的所有元件上線。 查看[支援的平台](../../azure-monitor/app/platforms.md)。
- 參閱[資料模型](../../azure-monitor/app/data-model.md)以了解 Application Insights 類型。
- 了解如何[擴充和篩選遙測](../../azure-monitor/app/api-filtering-sampling.md)。
- 檢閱 [Application Insights 設定參考](configuration-with-applicationinsights-config.md)。
