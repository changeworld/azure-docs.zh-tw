---
title: Application Insights 遙測相互關聯 | Microsoft Docs
description: Application Insights 遙測相互關聯
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 20e9ed7e83ff3359651acebc11a939a998f2889d
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607910"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights 中的遙測相互關聯

在微服務的世界裡，每個邏輯作業都需要在服務的各種元件中完成工作。 您可以使用 [Application Insights](../../azure-monitor/app/app-insights-overview.md)分別監視這些元件。 Application Insights 支援分散式遙測相互關聯，這可供您用來偵測造成失敗或效能衰退的元件。

本文說明 Application Insights 所使用的資料模型，以關聯多個元件所傳送的遙測。 它涵蓋內容傳播技巧和通訊協定。 它也涵蓋了在不同語言和平臺上執行相互關聯策略的方式。

## <a name="data-model-for-telemetry-correlation"></a>遙測相互關聯的資料模型

Application Insights 會定義分散遙測相互關聯的[資料模型](../../azure-monitor/app/data-model.md)。 為了將遙測與邏輯作業產生關聯，每個遙測專案都有一個稱為的內容欄位 `operation_Id` 。 每個遙測項目會在分散式追蹤內共用這個識別碼。 因此，即使您遺失單一層的遙測，您仍然可以將其他元件所報告的遙測相關聯。

分散式邏輯作業通常包含一組較小的作業，這些作業是由其中一個元件所處理的要求。 這些作業是由[要求遙測](../../azure-monitor/app/data-model-request-telemetry.md)所定義。 每個要求遙測專案都有自己 `id` 的識別，它是唯一且全域識別的。 和所有遙測專案 (例如與要求相關聯的追蹤和例外狀況) 都應將設定 `operation_parentId` 為要求的值 `id` 。

每個連出作業 (例如對另一個元件的 HTTP 呼叫) 都會由[相依性遙測](../../azure-monitor/app/data-model-dependency-telemetry.md)代表。 相依性遙測也會定義其本身 `id` 是全域唯一的。 由這個相依性呼叫起始的要求遙測會使用 這個 `id` 作為其 `operation_parentId`。

您可以將 `operation_Id`、`operation_parentId` 和 `request.id` 與 `dependency.id` 搭配使用，來建置分散式邏輯作業的檢視。 這些欄位也會定義遙測呼叫的因果順序。

在微服務環境中，來自元件的追蹤可能會前往不同的儲存體項目。 每個元件在 Application Insights 中都可能有自己的檢測金鑰。 若要取得邏輯作業的遙測，Application Insights 查詢每個儲存體專案的資料。 當儲存專案的數目很大時，您將需要一個提示，告訴您接下來要在哪裡查看。 Application Insights 資料模型會定義兩個欄位來解決這個問題：`request.source` 和 `dependency.target`。 第一個欄位會識別起始相依性要求的元件。 第二個欄位會識別哪個元件傳回相依性呼叫的回應。

## <a name="example"></a>範例

以下舉例說明。 稱為股票價格的應用程式會使用稱為股票的外部 API 來顯示股票的目前市場價格。 股票價格應用程式有一個稱為 Stock 頁面的頁面，用戶端網頁瀏覽器會使用這個頁面來開啟 `GET /Home/Stock` 。 應用程式會使用 HTTP 呼叫來查詢股票 API `GET /api/stock/value` 。

您可以執行查詢來分析產生的遙測︰

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

在結果中，請注意，所有遙測項目都共用 `operation_Id` 這個根。 從頁面發出 Ajax 呼叫時，會將新的唯一識別碼 (`qJSXU`) 指派給相依性遙測，而 pageView 的識別碼則會用來做為 `operation_ParentId` 。 接著，伺服器要求會使用 Ajax 識別碼作為 `operation_ParentId`。

| itemType   | NAME                      | 識別碼           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock 頁面                |              | STYz               | STYz         |
| 相依性 | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| 要求    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| 相依性 | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

對 `GET /api/stock/value` 外部服務進行呼叫時，您必須知道該伺服器的身分識別，才能 `dependency.target` 適當地設定欄位。 當外部服務不支援監視時，`target` 會設定為服務的主機名稱 (例如 `stock-prices-api.com`)。 但是，如果服務藉由傳回預先定義的 HTTP 標頭來識別其本身， `target` 就會包含可讓 Application Insights 藉由從該服務查詢遙測資料來建立分散式追蹤的服務識別。

## <a name="correlation-headers-using-w3c-tracecontext"></a>使用 W3C TraceCoNtext 的相互關聯標頭

Application Insights 正在轉換成 [W3C 追蹤內容](https://w3c.github.io/trace-context/)，它會定義：

- `traceparent`：攜帶全域唯一的作業識別碼和呼叫的唯一識別碼。
- `tracestate`：攜帶系統特定的追蹤內容。

Application Insights SDK 的最新版本支援 Trace-Context 的通訊協定，但您可能需要加入宣告。  (與先前的相互關聯通訊協定（Application Insights SDK 所支援的回溯相容性）維持不變。 ) 

相互 [關聯 HTTP 通訊協定（也稱為要求識別碼](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)）即將被取代。 此通訊協定會定義兩個標頭：

- `Request-Id`：攜帶呼叫的全域唯一識別碼。
- `Correlation-Context`：攜帶分散式追蹤屬性的名稱/值組集合。

Application Insights 也會定義相互關聯 HTTP 通訊協定的 [延伸](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) 。 它會使用 `Request-Context` 名稱值組，來傳播立即呼叫端或被呼叫端所使用的屬性集合。 Application Insights SDK 會使用此標頭來設定 `dependency.target` 和 `request.source` 欄位。

[W3C 追蹤內容](https://w3c.github.io/trace-context/)和 Application Insights 資料模型的對應方式如下：

| Application Insights                   | W3C TraceCoNtext                                      |
|------------------------------------    |-------------------------------------------------|
| `Request` 和 `Dependency` 的 `Id`     | [父系識別碼](https://w3c.github.io/trace-context/#parent-id)                                     |
| `Operation_Id`                         | [追蹤識別碼](https://w3c.github.io/trace-context/#trace-id)                                           |
| `Operation_ParentId`                   | 此範圍父範圍的[父系識別碼](https://w3c.github.io/trace-context/#parent-id)。 如果這是根範圍，則此欄位必須是空的。     |

如需詳細資訊，請參閱 [Application Insights 遙測資料模型](../../azure-monitor/app/data-model.md)。

### <a name="enable-w3c-distributed-tracing-support-for-net-apps"></a>啟用 .NET 應用程式的 W3C 分散式追蹤支援

根據預設，所有最近的 .NET Framework/.NET Core Sdk 都會啟用 W3C TraceCoNtext 型分散式追蹤，以及與舊版 Request-Id 通訊協定的回溯相容性。

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>啟用 Java 應用程式的 W3C 分散式追蹤支援

#### <a name="java-30-agent"></a>JAVA 3.0 代理程式

  JAVA 3.0 代理程式支援 W3C 現成的功能，不需要進行其他設定。 

#### <a name="java-sdk"></a>Java SDK
- **連入設定**

  - 針對 JAVA EE 應用程式，請將下列內容新增至 `<TelemetryModules>` ApplicationInsights.xml 中的標記：

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```

  - 針對春季 Boot 應用程式，請新增下列屬性：

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
> 請確定連入和連出設定都完全相同。

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>啟用 Web 應用程式的 W3C 分散式追蹤支援

這項功能已在中 `Microsoft.ApplicationInsights.JavaScript` 。 此功能預設為停用。 若要啟用它，請使用 `distributedTracingMode` config。AI_AND_W3C 是為了與 Application Insights 所檢測之任何舊版服務的回溯相容性而提供。

- **[以 npm 為基礎的安裝程式](./javascript.md#npm-based-setup)**

新增下列設定：
  ```JavaScript
    distributedTracingMode: DistributedTracingModes.W3C
  ```

- **[以程式碼片段為基礎的設定](./javascript.md#snippet-based-setup)**

新增下列設定：
  ```
      distributedTracingMode: 2 // DistributedTracingModes.W3C
  ```
> [!IMPORTANT] 
> 若要查看啟用相互關聯所需的所有設定，請參閱 [JavaScript 相互關聯檔](./javascript.md#enable-correlation)。

## <a name="telemetry-correlation-in-opencensus-python"></a>OpenCensus Python 中的遙測相互關聯

OpenCensus Python 支援 [W3C 追蹤內容](https://w3c.github.io/trace-context/) ，而不需要額外的設定。

您可以在 [這裡](https://github.com/census-instrumentation/opencensus-specs/tree/master/trace)找到 OpenCensus 資料模型作為參考。

### <a name="incoming-request-correlation"></a>傳入要求相互關聯

OpenCensus Python 會將 W3C Trace-Context 標頭從要求本身產生的範圍，相互關聯至範圍。 OpenCensus 將會自動執行這些熱門 web 應用程式架構的整合： Flask、Django 和金字塔。 您只需要以 [正確的格式](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) 填入 W3C Trace-Context 標頭，並使用要求傳送這些標頭。 以下是示範此範例的 Flask 應用程式範例：

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

此程式碼會在本機電腦上執行 Flask 應用程式範例，以接聽埠 `8080` 。 若要使追蹤內容相互關聯，您必須將要求傳送至端點。 在此範例中，您可以使用 `curl` 命令：
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
藉由查看 [追蹤內容標頭格式](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)，您可以衍生下列資訊：

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

如果您查看傳送給 Azure 監視器的要求專案，可以看到填入追蹤標頭資訊的欄位。 您可以在 Azure 監視器 Application Insights 資源的記錄 (分析) 中找到此資料。

![ (Analytics 中的記錄要求遙測) ](./media/opencensus-python/0011-correlation.png)

此 `id` 欄位的格式為 `<trace-id>.<span-id>` ，其中取自在 `trace-id` 要求中傳遞的追蹤標頭，且 `span-id` 為此範圍產生的8位元組陣列。

此 `operation_ParentId` 欄位的格式為 `<trace-id>.<parent-id>` ，其中 `trace-id` 和 `parent-id` 都取自要求中傳遞的追蹤標頭。

### <a name="log-correlation"></a>記錄相互關聯

OpenCensus Python 可讓您將追蹤識別碼、span 識別碼和取樣旗標新增至記錄，以將記錄相互關聯。 您可以藉由安裝 OpenCensus [記錄整合](https://pypi.org/project/opencensus-ext-logging/)來新增這些屬性。 下列屬性會新增至 Python `LogRecord` 物件： `traceId` 、 `spanId` 和 `traceSampled` 。 請注意，這只適用于整合之後所建立的記錄器。

以下是示範此範例的範例應用程式：

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
當此程式碼執行時，會在主控台中列印下列內容：
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
請注意，在 `spanId` 範圍內的記錄訊息會有一個存在。 這等同 `spanId` 于名為的範圍 `hello` 。

您可以使用匯出記錄檔資料 `AzureLogHandler` 。 如需詳細資訊，請參閱[這篇文章](./opencensus-python.md#logs)。

## <a name="telemetry-correlation-in-net"></a>.NET 中的遙測相互關聯

.NET 執行時間支援使用[活動](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)和[DiagnosticSource](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)的說明來散發

Application Insights .NET SDK 會使用 `DiagnosticSource` 和 `Activity` 來收集遙測資料，並將其相互關聯。

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>JAVA 中的遙測相互關聯

[JAVA 代理程式](./java-in-process-agent.md) 和 [java SDK](../../azure-monitor/app/java-get-started.md) 版本2.0.0 或更新版本都支援遙測的自動相互關聯。 它會自動填入 `operation_id` 所有遙測 (例如追蹤、例外狀況，以及在要求範圍內) 發出的自訂事件。 如果已設定 [JAVA SDK 代理程式](../../azure-monitor/app/java-agent.md) ，它也會傳播相互關聯標頭 (稍早透過 HTTP 所述的服務對服務呼叫) 。

> [!NOTE]
> Application Insights JAVA 代理程式會自動收集 JMS、Kafka、Netty/Webflux 等的要求和相依性。 針對 JAVA SDK，相互關聯功能僅支援透過 Apache HttpClient 發出的呼叫。 SDK 不支援跨訊息技術的自動內容傳播 (例如 Kafka、RabbitMQ 和 Azure 服務匯流排) 。 

> [!NOTE]
> 若要收集自訂遙測，您需要使用 JAVA 2.6 SDK 來檢測應用程式。 

### <a name="role-names"></a>角色名稱

您可能會想要自訂群組件名稱在 [應用程式對應](../../azure-monitor/app/app-map.md)中的顯示方式。 若要這樣做，您可以 `cloud_RoleName` 採取下列其中一個動作，手動設定：

- 針對 Application Insights JAVA 代理程式3.0，設定雲端角色名稱，如下所示：

    ```json
    {
      "role": {
        "name": "my cloud role name"
      }
    }
    ```
    您也可以使用環境變數來設定雲端角色名稱 `APPLICATIONINSIGHTS_ROLE_NAME` 。

- 使用 Application Insights JAVA SDK 2.5.0 和更新版本，您可以藉 `cloud_RoleName` 由新增 `<RoleName>` 至 ApplicationInsights.xml 檔案來指定：

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- 如果您在 Application Insights 春季 Boot Starter 中使用 [春季開機]，則只需要在應用程式的 [屬性] 檔案中設定應用程式的自訂名稱：

  `spring.application.name=<name-of-app>`

  彈簧開機 Starter 會自動指派 `cloudRoleName` 給您為屬性輸入的值 `spring.application.name` 。

## <a name="next-steps"></a>後續步驟

- 撰寫 [自訂遙測](../../azure-monitor/app/api-custom-events-metrics.md)。
- 如需 ASP.NET Core 和 ASP.NET 中的先進相互關聯案例，請參閱 [追蹤自訂作業](custom-operations-tracking.md)。
- 深入了解為其他 SDK [設定 cloud_RoleName](./app-map.md#set-or-override-cloud-role-name)。
- 在 Application Insights 上將微服務的所有元件上線。 查看[支援的平台](./platforms.md)。
- 參閱[資料模型](./data-model.md)以了解 Application Insights 類型。
- 了解如何[擴充和篩選遙測](./api-filtering-sampling.md)。
- 檢閱 [Application Insights 設定參考](configuration-with-applicationinsights-config.md)。