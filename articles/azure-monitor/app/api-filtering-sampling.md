---
title: 在 Application Insights SDK 中篩選及前置處理 | Microsoft Docs
description: 撰寫 SDK 的遙測處理器和遙測初始設定式來篩選屬性或將屬性新增至資料，再將遙測傳送至 Application Insights 入口網站。
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 8b81849726ad546a24ce1bb56a139b384eb54c42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405368"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>在 Application Insights SDK 中篩選及前置處理遙測

您可以撰寫和設定 Application Insights SDK 的外掛程式，以自訂如何擴充和處理遙測，然後再將它傳送至 Application Insights 服務。

* [取樣](sampling.md) 可減少遙測的量而不會影響統計資料。 它可將相關資料點寶持放在一起，因此您診斷問題時，能夠在資料點之間瀏覽。 在入口網站中將乘以總計數，以補償取樣。
* 使用遙測處理器進行篩選，可讓您在 SDK 傳送至伺服器之前，先篩選出其中的遙測。 例如，您可以從傀儡程式中排除要求來減少遙測量。 篩選是比取樣更能減少流量的基本方法。 它可讓您更充分掌握傳輸內容，但是您必須注意，它會影響統計資料 (例如，若您要篩選所有成功的要求)。
* 遙測初始化運算式會在從您的應用程式傳送的任何遙測中[新增或修改屬性](#add-properties)，包括來自標準模組的遙測。 例如，您可以新增計算好的值，或是用來在入口網站中篩選資料的版本號碼。
* [SDK API](../../azure-monitor/app/api-custom-events-metrics.md) 可用來傳送自訂事件和計量。

開始之前：

* 為您的應用程式安裝適當的 SDK： [ASP.NET](asp-net.md)、 [ASP.NET Core](asp-net-core.md)、[適用于 .Net/.net Core 的非 HTTP/背景工作](worker-service.md)或[JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtering

這項技術可讓您直接控制遙測串流中包含或排除的內容。 篩選可用來卸載要傳送至 Application Insights 的遙測專案。 您可以將它與取樣搭配使用或分開使用。

若要篩選遙測，請撰寫遙測處理器，並向註冊它`TelemetryConfiguration`。 所有遙測會通過您的處理器，您可以選擇將它從串流中卸載，或將它提供給鏈中的下一個處理器。 這包括來自標準模組的遙測，例如 HTTP 要求收集器和相依性收集器，以及您自行追蹤的遙測。 比方說，您可以篩選出有關來自傀儡程式要求或成功的相依性呼叫的遙測。

> [!WARNING]
> 篩選傳送自使用處理器的 SDK 的遙測可能會曲解您在入口網站中看到的統計資料，並且難以追蹤相關的項目。
>
> 請考慮改用 [取樣](../../azure-monitor/app/sampling.md)。
>
>

### <a name="create-a-telemetry-processor-c"></a>建立遙測處理器 (C#)

1. 若要建立篩選準則， `ITelemetryProcessor`請執行。

    請注意，遙測處理器建構一連串的處理。 當您具現化遙測處理器時，您會獲得鏈中下一個處理器的參考。 當遙測資料點傳遞至處理方法時，它會執行其工作，然後呼叫（或不會呼叫）鏈中的下一個遙測處理器。

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. 新增您的處理器。

**ASP.NET 應用程式**在 ApplicationInsights 中插入此程式碼片段：

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

您可以在類別中提供公開具名屬性，以從 .config 檔案傳遞字串值。

> [!WARNING]
> 仔細地將 .config 檔案中的類型名稱和任何屬性名稱與程式碼中的類別和屬性名稱做比對。 如果 .config 檔案參考不存在的類型或屬性，SDK 可能無法傳送任何遙測，而且不會產生任何訊息。
>

或者，**** 您也可以在程式碼中初始化篩選。 在適當的初始化類別中（例如 AppStart） `Global.asax.cs` ，將您的處理器插入鏈中：

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

在這個點之後建立的 TelemetryClients 會使用您的處理器。

**ASP.NET Core/背景工作服務應用程式**

> [!NOTE]
> 使用`ApplicationInsights.config`或使用`TelemetryConfiguration.Active`來新增處理器對 ASP.NET Core 應用程式而言是不正確，或者，如果您使用 ApplicationInsights. WorkerService SDK。

針對使用[ASP.NET Core](asp-net-core.md#adding-telemetry-processors)或[WorkerService](worker-service.md#adding-telemetry-processors)所撰寫的應用程式， `TelemetryProcessor`使用`AddApplicationInsightsTelemetryProcessor`上`IServiceCollection`的擴充方法來加入新的，如下所示。 這個方法是在`ConfigureServices` `Startup.cs`類別的方法中呼叫。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>範例篩選器

#### <a name="synthetic-requests"></a>綜合要求

篩選出 bot 和 Web 測試。 雖然計量瀏覽器可讓您選擇篩選出綜合來源，但此選項會藉由在 SDK 本身進行篩選來減少流量和內嵌大小。

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>驗證失敗

篩選出具有 "401" 回應的要求。

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>篩選出快速遠端相依性呼叫

如果您只想要診斷速度很慢的呼叫，請篩選出快的項目。

> [!NOTE]
> 這樣會曲解您在入口網站上看到的統計資料。
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>診斷相依性問題

[這篇部落格文章](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) 描述可自動傳送定期的 Ping 給相依項目，藉以診斷相依性問題的專案。

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript Web 應用程式

**使用 ITelemetryInitializer 進行篩選**

1. 建立遙測初始化運算式回呼函數。 回呼函式會`ITelemetryItem`採用做為參數，這是正在處理的事件。 `false`從此回呼傳回會導致要篩選掉的遙測專案。  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. 新增遙測初始化運算式回呼：

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>新增/修改屬性： ITelemetryInitializer


使用遙測初始化運算式，以額外資訊和/或覆寫標準遙測模組所設定的遙測屬性來充實遙測。

例如，Web 套件的 Application Insights 會收集關於 HTTP 要求的遙測。 根據預設，它會將所有含 >= 400 回應碼的要求標記為失敗。 但如果您想將 400 視為成功，您可以提供設定 Success 屬性的遙測初始設定式。

如果您提供遙測初始設定式，則會在呼叫任何的 Track*() 方法時呼叫它。 這包括`Track()`標準遙測模組呼叫的方法。 依照慣例，這些模組不會設定任何已由初始設定式設定的屬性。 在呼叫遙測處理器之前，會呼叫遙測初始化運算式。 因此，對處理器而言，初始化運算式所完成的任何擴充都是可見的。

**定義您的初始設定式**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**ASP.NET apps：載入您的初始化運算式**

在 ApplicationInsights.config 中：

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*或者* ，您也可以在程式碼 (如 Global.aspx.cs) 中具現化初始設定式：

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[詳細查看此範例。](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core/背景工作服務應用程式：載入您的初始化運算式**

> [!NOTE]
> 使用`ApplicationInsights.config`或使用`TelemetryConfiguration.Active`來加入初始化運算式對 ASP.NET Core 應用程式而言是不正確，或如果您使用 ApplicationInsights. WorkerService SDK。

針對使用[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers)或[WorkerService](worker-service.md#adding-telemetryinitializers)所撰寫的應用程式， `TelemetryInitializer`加入新的是藉由將它新增至相依性插入容器來完成，如下所示。 這是在方法`Startup.ConfigureServices`中完成。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>JavaScript 遙測初始設定式
*JavaScript*

在您從入口網站取得的初始化程式碼之後立即插入遙測初始設定式：

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

如需 telemetryItem 上可用的非自訂屬性摘要，請參閱 [Application Insights 匯出資料模型](../../azure-monitor/app/export-data-model.md)。

您可以視需要新增多個初始化運算式，並依加入的順序來呼叫它們。

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python 遙測處理器

OpenCensus Python 中的遙測處理器只是呼叫的回呼函式，可在匯出之前處理遙測。 回呼函數必須接受[信封](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86)資料類型做為其參數。 若要篩選掉要匯出的遙測資料，請確定回呼函數`False`傳回。 您可以在[這裡](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)的信封中看到 Azure 監視器資料類型的架構。

> [!NOTE]
> 您可以藉`cloud_RoleName`由變更`ai.cloud.role` `tags`欄位中的屬性來修改。

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name.py'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
您可以視需要新增多個處理器，並依其新增順序來呼叫它們。 如果一個處理器應該擲回例外狀況，則不會影響下列處理器。

### <a name="example-telemetryinitializers"></a>範例 TelemetryInitializers

#### <a name="add-custom-property"></a>新增自訂屬性

下列範例初始化運算式會將自訂屬性新增至每個追蹤的遙測。

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>新增雲端角色名稱

下列範例初始化運算式會將雲端角色名稱設定為每個追蹤的遙測。

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>從 HttpCoNtext 新增資訊

下列範例初始化運算式會從讀取資料[`HttpContext`](https://docs.microsoft.com/aspnet/core/fundamentals/http-context?view=aspnetcore-3.1) ，並將其附加`RequestTelemetry`至實例。 `IHttpContextAccessor`會透過函式相依性插入自動提供。

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor 和 ITelemetryInitializer

遙測處理器與遙測初始設定式之間有何差異？

* 您可以對它們執行的動作有一些重迭：兩者都可以用來新增或修改遙測的屬性，但建議針對該目的使用初始化運算式。
* TelemetryInitializers 一律會在 TelemetryProcessors 之前執行。
* 可能會呼叫 TelemetryInitializers 一次以上。 依照慣例，它們不會設定任何已設定的屬性。
* TelemetryProcessors 可讓您完全取代或捨棄遙測項目。
* 保證會針對每個遙測專案呼叫所有已註冊的 TelemetryInitializers。 對於遙測處理器，SDK 會保證呼叫第一部遙測處理器。 是否呼叫其餘的處理器，是由先前的遙測處理器決定。
* 使用 TelemetryInitializers 以其他屬性擴充遙測，或覆寫現有的內容。 使用 TelemetryProcessor 來篩選出遙測。

## <a name="troubleshooting-applicationinsightsconfig"></a>為 ApplicationInsights.config 疑難排解

* 確認完整格式的類型名稱和組件名稱均正確。
* 確認 applicationinsights.config 檔案在您的輸出目錄中，並且包含任何最近的變更。

## <a name="reference-docs"></a>參考文件

* [API 總覽](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET 參考](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK 程式碼

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>後續步驟
* [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)
* [取樣](../../azure-monitor/app/sampling.md)
* [疑難排解](../../azure-monitor/app/troubleshoot-faq.md)
