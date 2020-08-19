---
title: Azure Application Insights 中的遙測取樣 | Microsoft Docs
description: 如何讓遙測量保持在控制下。
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: bb6793bc1e3d5bb55426c1f344520ae19a22a9f9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549560"
---
# <a name="sampling-in-application-insights"></a>Application Insights 中的取樣

取樣是 [Azure Application Insights](./app-insights-overview.md) 中的一個功能。 建議的方法是減少遙測流量、資料成本和儲存體成本，同時保留統計正確的應用程式資料分析。 取樣也可協助您避免 Application Insights 節流遙測。 取樣篩選器會選取相關的專案，以便您在進行診斷調查時，可以在專案之間流覽。

當計量計數在入口網站中顯示時，會重新標準化，以將取樣納入考慮。 這麼做會將對統計資料的任何影響降到最低。

## <a name="brief-summary"></a>小結

* 有三種不同的取樣類型：調適型取樣、固定速率取樣和內嵌取樣。
* 在 Application Insights ASP.NET 的所有最新版本和 ASP.NET Core 軟體發展工具組 (Sdk) 中，預設會啟用調適型取樣。 [Azure Functions](../../azure-functions/functions-overview.md)也會使用它。
* 最新版本的 Application Insights Sdk 適用于 ASP.NET、ASP.NET Core、JAVA (代理程式和 SDK) 和 Python 都有固定速率取樣。
* 內嵌取樣適用于 Application Insights 服務端點。 只有在沒有其他取樣生效時才適用。 如果 SDK 會將您的遙測資料取樣，則會停用內嵌取樣。
* 針對 web 應用程式，如果您記錄自訂事件，而且需要確保一組事件一起保留或捨棄，則事件必須具有相同的 `OperationId` 值。
* 如果您要撰寫分析查詢，請 [考慮到取樣](../log-query/aggregations.md)。 特別是，您應該使用 `summarize sum(itemCount)`，而非只計算記錄。
* 某些遙測類型（包括效能計量和自訂計量）一律會保留，而不論是否已啟用取樣。

下表摘要說明每個 SDK 和應用程式類型可用的取樣類型：

| Application Insights SDK | 支援調適型取樣 | 支援的固定速率取樣 | 支援內嵌取樣 |
|-|-|-|-|
| ASP.NET | [是 (預設為開啟) ](#configuring-adaptive-sampling-for-aspnet-applications) | [是](#configuring-fixed-rate-sampling-for-aspnet-applications) | 只有在沒有其他取樣生效時 |
| ASP.NET Core | [是 (預設為開啟) ](#configuring-adaptive-sampling-for-aspnet-core-applications) | [是](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | 只有在沒有其他取樣生效時 |
| Azure Functions | [是 (預設為開啟) ](#configuring-adaptive-sampling-for-azure-functions) | No | 只有在沒有其他取樣生效時 |
| Java | 否 | [是](#configuring-fixed-rate-sampling-for-java-applications) | 只有在沒有其他取樣生效時 |
| Node.JS | 否 | [是](./nodejs.md#sampling) | 只有在沒有其他取樣生效時
| Python | 否 | [是](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | 只有在沒有其他取樣生效時 |
| All others | 否 | 否 | [是](#ingestion-sampling) |

> [!NOTE]
> 本頁面大部分的資訊都適用于目前的 Application Insights Sdk 版本。 如需舊版 Sdk 的詳細資訊， [請參閱下一節](#older-sdk-versions)。

## <a name="types-of-sampling"></a>取樣類型

有三種不同的取樣方法：

* 調適型**取樣**會自動調整從 ASP.NET/ASP.NET Core 應用程式中的 SDK 傳送的遙測資料量，以及 Azure Functions。 當您使用 ASP.NET 或 ASP.NET Core SDK 時，這是預設的取樣。 調適型取樣目前僅適用于 ASP.NET 伺服器端遙測及 Azure Functions。

* **固定速率取樣** 可減少從您的 ASP.NET 或 ASP.NET Core、JAVA 伺服器和使用者的瀏覽器傳送的遙測資料量。 而比例則由您設定。 用戶端和伺服器會同步處理它們的取樣，讓您可以在 [搜尋] 終於相關的頁面檢視和要求之間瀏覽。

* 內嵌**取樣**會在 Application Insights 服務端點進行。 它會根據您設定的取樣比例，捨棄來自您應用程式的一些遙測。 這不會減少從應用程式傳送的遙測流量，但可協助您讓流量不要超過每月配額。 內嵌取樣的主要優點是您可以設定取樣率，而不需要重新部署應用程式。 內嵌取樣適用于所有伺服器和用戶端，但在作業中任何其他類型的取樣時都不適用。

> [!IMPORTANT]
> 如果自調適型或固定速率取樣方法正在運作中，則會停用內嵌取樣。

## <a name="adaptive-sampling"></a>調適性取樣

調適性取樣會影響從您的 Web 伺服器應用程式傳送給 Application Insights 服務端點的遙測量。

> [!TIP]
> 當您使用 ASP.NET SDK 或 ASP.NET Core SDK 時，預設會啟用調適型取樣，而且 Azure Functions 預設也會啟用。

磁片區會自動調整以保持在指定的最大流量速率內，並透過設定來控制 `MaxTelemetryItemsPerSecond` 。 如果應用程式產生較少的遙測資料（例如，當您在偵錯工具或因為低使用量時），取樣處理器將不會卸載專案，只要磁片區低於此值即可 `MaxTelemetryItemsPerSecond` 。 當遙測量增加時，會調整取樣率，以達成目標磁片區。 調整會定期重新計算，並且根據外寄傳輸速率的移動平均。

為了要讓遙測量達到目標，系統會捨棄部分已產生的遙測。 但就跟其他取樣類型一樣，演算法會保留相關的遙測項目。 舉例來說，當您在 [搜尋] 中檢查遙測時，將能夠尋找與特定例外狀況相關的要求。

度量計量 (例如要求率及例外狀況率) 會受到調整來補償取樣率，讓它們能在計量瀏覽器中顯示大致上正確的值。

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>設定 ASP.NET 應用程式的調適型取樣

> [!NOTE]
> 本節適用于 ASP.NET 應用程式，而不是 ASP.NET Core 應用程式。 [深入瞭解如何為本檔稍後的 ASP.NET Core 應用程式設定適應性取樣。](#configuring-adaptive-sampling-for-aspnet-core-applications)

在中 [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) ，您可以調整節點中的數個參數 `AdaptiveSamplingTelemetryProcessor` 。 顯示的數字是預設值：

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    彈性演算法在每部**伺服器主機上**所要收集的[邏輯運算](./correlation.md#data-model-for-telemetry-correlation)目標速率。 如果 Web 應用程式在許多主機上執行，請減少此值，以保持在您的 Application Insights 入口網站的流量目標速率內。

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    重新評估目前的遙測速率的間隔。 評估是以移動平均來執行。 如果您的遙測會突然暴增，您可能想要縮短此間隔。

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    當取樣百分比值變更時，我們多久之後才會再次降低取樣百分比以抓取較少的資料？

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    當取樣百分比值變更時，我們多久之後才會再增加取樣百分比來捕捉更多資料？

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    當取樣百分比改變時，我們可以設定的最小值是多少？

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    當取樣百分比改變時，我們可以設定的最大值為何？

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    在移動平均的計算中，這會指定應指派給最新值的加權。 使用等於或小於 1 的值。 較小的值會讓演算法不易受突然的變更影響。

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    應用程式剛剛啟動時所要取樣的遙測量。 當您正在進行調試時，請勿減少此值。

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    您不想要進行取樣的類型清單（以分號分隔）。 可辨識的類型為： `Dependency` 、 `Event` 、 `Exception` 、 `PageView` 、 `Request` 、 `Trace` 。 會傳輸指定類型的所有遙測;將會針對未指定的類型進行取樣。

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    以分號分隔的型別清單，您想要進行取樣。 可辨識的類型為： `Dependency` 、 `Event` 、 `Exception` 、 `PageView` 、 `Request` 、 `Trace` 。 將會取樣指定的類型;所有其他類型的遙測一律會傳送。

**若要關閉** 自調適型取樣，請 `AdaptiveSamplingTelemetryProcessor` 從) 移除節點 (`ApplicationInsights.config` 。

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>替代方法：在程式碼中設定調適型取樣

`.config`您可以用程式設計的方式設定這些值，而不是在檔案中設定取樣參數。

1. 從檔案中移除所有的 `AdaptiveSamplingTelemetryProcessor` 節點 () `.config` 。
2. 使用下列程式碼片段來設定調適型取樣：

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([深入了解遙測處理器](./api-filtering-sampling.md#filtering))。

您也可以個別調整每個遙測類型的取樣率，或甚至可以排除某些類型，使其完全無法進行取樣：

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>設定 ASP.NET Core 應用程式的調適型取樣

`ApplicationInsights.config`ASP.NET Core 的應用程式都沒有，所以所有設定都是透過程式碼來完成。
所有的 ASP.NET Core 應用程式預設會啟用調適型取樣。 您可以停用或自訂取樣行為。

#### <a name="turning-off-adaptive-sampling"></a>關閉適應性取樣

在使用檔案內的方法中新增 Application Insights 服務時，可以停用預設的取樣功能 `ConfigureServices` `ApplicationInsightsServiceOptions` `Startup.cs` ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

上述程式碼將會停用調適型取樣。 請遵循下列步驟使用更多自訂選項新增取樣。

#### <a name="configure-sampling-settings"></a>設定取樣設定

如下所示，使用 `TelemetryProcessorChainBuilder` 的擴充方法自訂取樣行為。

> [!IMPORTANT]
> 如果您使用這個方法來設定取樣，請務必 `aiOptions.EnableAdaptiveSampling` 在呼叫時將屬性設定為 `false` `AddApplicationInsightsTelemetry()` 。

```csharp
using Microsoft.ApplicationInsights.Extensibility

public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>設定 Azure Functions 的調適型取樣

依照 [此頁面](../../azure-functions/functions-monitoring.md#configure-sampling) 中的指示，為在 Azure Functions 中執行的應用程式設定適應性取樣。

## <a name="fixed-rate-sampling"></a>固定速率取樣

固定速率取樣可減少從 web 伺服器和網頁瀏覽器傳送的流量。 但它會依照您設定的速率來降低遙測，這與調適性取樣不同。 固定速率取樣適用于 ASP.NET、ASP.NET Core、JAVA 及 Python 應用程式。

就像其他的取樣技巧一樣，這也會保留相關的專案。 它也會同步處理用戶端和伺服器取樣，以便保留相關的專案-例如，當您在 [搜尋] 中查看網頁檢視時，您可以找到其相關的伺服器要求。 

在計量瀏覽器中，速率 (例如要求及例外狀況數) 會乘以某個係數來補償取樣率，讓它們能大致上正確。

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>設定 ASP.NET 應用程式的固定速率取樣

1. **停**用調適型取樣：在中 [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) ，移除或批註 `AdaptiveSamplingTelemetryProcessor` 節點。

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **啟用固定取樣率模組。** 將此程式碼片段新增至 [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) ：
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      或者，您可以透過程式設計的 `ApplicationInsights.config` 方式設定這些值，而不是在檔案中設定取樣參數：

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([深入了解遙測處理器](./api-filtering-sampling.md#filtering))。

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>設定 ASP.NET Core 應用程式的固定速率取樣

1. **停**用調適型取樣：您可以 `ConfigureServices` 使用下列方法，在方法中進行變更： `ApplicationInsightsServiceOptions`

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **啟用固定取樣率模組。** 您可以在方法中進行變更 `Configure` ，如下列程式碼片段所示：

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>設定 JAVA 應用程式的固定速率取樣

依預設，不會在 JAVA 代理程式和 SDK 中啟用取樣。 目前只支援固定速率取樣。 JAVA 不支援調適型取樣。

#### <a name="configuring-java-agent"></a>設定 JAVA 代理程式

1. 下載 [applicationinsights-agent-3.0.0 >>.0.9.0-preview.nupkg. 5. .jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. 若要啟用取樣，請將下列內容新增至您的檔案 `ApplicationInsights.json` ：

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>設定 JAVA SDK

1. 下載並設定您的 web 應用程式，並使用最新的 [Application Insights JAVA SDK](./java-get-started.md)。

2. 將下列程式碼片段新增至檔案，以**啟用固定速率取樣模組** `ApplicationInsights.xml` ：

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. 您可以使用標記內的下列標記，在取樣中包含或排除特定類型的遙測 `Processor` `FixedRateSamplingTelemetryProcessor` ：
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

可以在取樣中包括或排除的遙測類型為： `Dependency` 、 `Event` 、 `Exception` 、 `PageView` 、 `Request` 和 `Trace` 。

> [!NOTE]
> 針對取樣百分比，選擇接近 100/N 的百分比，其中 N 是整數。  目前取樣並不支援其他值。

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>設定 OpenCensus Python 應用程式的固定速率取樣

使用最新的 [OpenCensus Azure 監視器匯出工具](./opencensus-python.md)檢測您的應用程式。

> [!NOTE]
> 計量匯出工具無法使用固定速率取樣。 這表示自訂計量是唯一不能設定取樣的遙測類型。 計量匯出工具會傳送它所追蹤的所有遙測。

#### <a name="fixed-rate-sampling-for-tracing"></a>追蹤的固定速率取樣 ####
您可以指定 `sampler` 作為 `Tracer` 設定的一部分。 如果未提供明確的取樣器， `ProbabilitySampler` 預設會使用。 `ProbabilitySampler`預設會使用1/10000 的速率，這表示每個10000要求的其中一個會傳送至 Application Insights。 如果您想要指定取樣率，請參閱下方內容。

若要指定取樣率，請確定您 `Tracer` 指定的取樣率介於0.0 到1.0 （含）之間。 取樣率1.0 表示100%，表示您所有的要求都會以遙測的形式傳送至 Application Insights。

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>記錄的固定速率取樣 ####
您可以藉 `AzureLogHandler` 由修改選擇性引數來設定的固定速率取樣 `logging_sampling_rate` 。 如果未提供任何引數，則會使用取樣率1.0。 取樣率1.0 表示100%，表示您所有的要求都會以遙測的形式傳送至 Application Insights。

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>使用 JavaScript 設定網頁的固定速率取樣

以 JavaScript 為基礎的網頁可以設定為使用 Application Insights。 遙測是從使用者的瀏覽器中執行的用戶端應用程式傳送，而且可以從任何伺服器託管這些頁面。

當您 [針對 Application Insights 設定以 javascript 為基礎的網頁](javascript.md)時，請修改您從 Application Insights 入口網站取得的 javascript 程式碼片段。

> [!TIP]
> 在包含 JavaScript 的 ASP.NET 應用程式中，程式碼片段通常會進入 `_Layout.cshtml` 。

在檢測金鑰之前插入類似 `samplingPercentage: 10,` 的一行：

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

針對取樣百分比，選擇接近 100/N 的百分比，其中 N 是整數。 目前取樣並不支援其他值。

#### <a name="coordinating-server-side-and-client-side-sampling"></a>協調伺服器端和用戶端的取樣

用戶端 JavaScript SDK 會與伺服器端 SDK 一起參與固定速率的取樣。 經過檢測的頁面只會從伺服器端 SDK 決定要包含在取樣中的相同使用者傳送用戶端遙測。 此邏輯的設計目的是在用戶端和伺服器端的應用程式之間維護使用者會話的完整性。 如此一來，您可以從 Application Insights 中的任何特定遙測專案，找到此使用者或會話的所有其他遙測專案，而且在搜尋中，您可以在相關網頁檢視與要求之間流覽。

如果您的用戶端和伺服器端遙測未顯示協調範例：

* 確認您已在伺服器和用戶端上啟用取樣。
* 請檢查您的用戶端和伺服器中設定相同的取樣百分比。
* 確定 SDK 版本為 2.0 或更新版本。

## <a name="ingestion-sampling"></a>擷取取樣

內嵌取樣會在來自您 web 伺服器、瀏覽器和裝置的遙測抵達 Application Insights 服務端點的位置運作。 雖然它不會減少來自您應用程式的遙測流量，但會減少 Application Insights 所處理及保留 (並收費) 的遙測量。

如果您的應用程式通常會超過每月配額，且您無法選擇使用任何一種 SDK 式的取樣類型，請使用這種類型的取樣。 

在 [使用量和估計成本] 頁面中設定取樣率：

![從應用程式的總覽分頁，按一下 [設定]、[配額]、[範例]，然後選取取樣率，然後按一下 [更新]。](./media/sampling/data-sampling.png)

就跟其他取樣類型一樣，演算法會保留相關的遙測項目。 舉例來說，當您在 [搜尋] 中檢查遙測時，將能夠尋找與特定例外狀況相關的要求。 度量計量 (例如要求率及例外狀況率) 會正確地保留。

遭到取樣捨棄的資料點將無法在任何 Application Insights 功能中使用，例如 [連續匯出](./export-telemetry.md)。

當自我調整或固定速率取樣正在運作時，內嵌取樣無法運作。 當使用 ASP.NET SDK 或 ASP.NET Core SDK 時，或是在 [Azure App Service ](azure-web-apps.md) 中啟用 Application Insights 或使用狀態監視器時，預設會啟用調適型取樣。 當 Application Insights 服務端點收到遙測時，它會檢查遙測資料，如果取樣率回報為小於 100% (指出正在取樣遙測) 則會忽略您設定的內嵌取樣率。

> [!WARNING]
> 顯示在入口網站磚上的值表示您為內嵌取樣設定的值。 如果任何類型的 SDK 取樣 (彈性或固定速率取樣) 處於作業中，則不會代表實際的取樣率。

## <a name="when-to-use-sampling"></a>使用取樣的時機

一般來說，對於大部分的小型和中型應用程式而言，您不需要取樣。 最有用的診斷資訊和最準確的統計資料會是透過收集所有使用者活動的資料取得。 

取樣的主要優點如下：

* 當您的應用程式在短時間間隔內傳送非常高的遙測速率時，Application Insights 服務會將 ( 「節流」 ) 資料點。 取樣可減少應用程式可能會看到節流的可能性。
* 保持在定價層的資料點 [配額](pricing.md) 內。 
* 若要從收集的遙測降低網路流量。 

### <a name="which-type-of-sampling-should-i-use"></a>我應該使用哪種類型的取樣？

**如果是下列情形，請使用擷取取樣：**

* 您通常會使用每月的遙測配額。
* 您正在從使用者的網頁瀏覽器取得太多遙測。
* 您使用不支援取樣的 SDK 版本，例如比 ASP.NET 版本 2 早的版本。

**如果是下列情形，則使用固定取樣率：**

* 您想要在用戶端與伺服器之間進行同步處理，如此一來，當您調查 [搜尋](./diagnostic-search.md)中的事件時，您可以在用戶端和伺服器上的相關事件之間流覽，例如頁面流覽和 HTTP 要求。
* 您對於您的應用程式的適當取樣百分比有信心。 應該夠高以取得精確的度量，但是低於超過價格配額和節流限制的取樣率。

**使用調適性取樣：**

如果欲使用其他形式取樣但條件不適用，建議使用適應型取樣。 在 ASP.NET/ASP.NET Core SDK 中，預設會啟用此設定。 它不會減少流量，直到達到特定的最小速率為止，因此可能完全不會取樣低用途的網站。

## <a name="knowing-whether-sampling-is-in-operation"></a>瞭解取樣是否正在運作

若要找出實際的取樣率 (不論是否已套用)，請使用如下所示的 [分析查詢](../log-query/log-query-overview.md) ︰

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

如果您看到 `RetainedPercentage` 任何類型的小於100，則會取樣該類型的遙測。

> [!IMPORTANT]
> Application Insights 不會取樣會話、計量 (包括自訂計量) ，或任何取樣技術中的效能計數器遙測類型。 這些類型一律會從取樣中排除，因為這些遙測類型的精確度減少可能非常不需要。

## <a name="how-sampling-works"></a>取樣的運作方式

取樣演算法會決定要捨棄哪些遙測專案，以及要保留哪些遙測專案。 無論取樣是由 SDK 或 Application Insights 服務執行，都是如此。 取樣決策會根據數個規則，目標是要保留相關的資料點不變，在 Application Insights 中保有可採取動作而且即使有縮減資料集仍可靠的診斷經驗。 例如，如果您的應用程式在範例中包含失敗的要求，則會保留額外的遙測專案 (例如例外狀況，以及針對此要求所記錄的追蹤) 。 取樣會將它們全部保持在一起。 如此一來，當您在 Application Insights 中查看要求詳細資料時，您一律可以看到要求和其相關聯的遙測項目。

取樣決策是根據要求的作業識別碼，這表示屬於特定作業的所有遙測專案都會保留或卸載。 針對沒有設定作業識別碼的遙測專案 (例如，從非同步執行緒報告且沒有 HTTP 內容的遙測專案) 取樣只會捕獲每個類型之遙測專案的百分比。

呈現遙測回來給您時，Application Insights 服務會以收集時使用的相同取樣百分比調整度量，來彌補遺漏的資料點。 因此，在 Application Insights 中查看遙測時，使用者會看到統計正確也非常接近實際數的近似值。

近似值的精確度絕大部分取決於設定的取樣百分比。 此外，對於處理大量使用者的通常類似要求的應用程式，其精確度會增加。 相反地，對於不處理大量負載的應用程式，就不需要取樣，因為這些應用程式通常可以傳送遙測同時保持在配額內，而不會因節流造成資料遺失。 

## <a name="frequently-asked-questions"></a>常見問題集

*ASP.NET 和 ASP.NET Core Sdk 中的預設取樣行為為何？*

* 如果您使用上述 SDK 的其中一個最新版本，則預設會啟用彈性取樣，每秒五個遙測專案。
  預設會新增兩個 `AdaptiveSamplingTelemetryProcessor` 節點，其中一個會 `Event` 在取樣中包含類型，而另一個則會將 `Event` 類型從取樣中排除。 這項設定表示 SDK 會嘗試將遙測專案限制為類型的五個遙測專案 `Event` ，以及所有其他類型的五個遙測專案，藉此確保與 `Events` 其他遙測類型分開取樣。 事件通常用於商務遙測，最可能不會受到診斷遙測磁片區的影響。
  
  以下顯示產生的預設檔案 `ApplicationInsights.config` 。 在 ASP.NET Core 中，會在程式碼中啟用相同的預設行為。 使用 [此頁面先前章節中的範例](#configuring-adaptive-sampling-for-aspnet-core-applications) 來變更此預設行為。

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*遙測是否可以進行一次以上的取樣？*

* 否。 如果已取樣專案，SamplingTelemetryProcessors 會忽略取樣考慮的專案。 內嵌取樣也是如此，這也不會將取樣套用至已在 SDK 本身取樣的專案。

*為什麼不取樣簡單的「收集每個遙測類型百分之 X」？*

* 雖然此取樣方法會在計量近似值中提供高等級的精確度，但它會中斷將診斷資料與每個使用者、會話和要求相互關聯的能力，這對診斷而言很重要。 因此，取樣的效能較佳，例如「收集應用程式使用者的 X% 的所有遙測專案」或「收集所有遙測資料的應用程式要求的 X%」。 針對未與要求相關聯的遙測專案 (例如背景非同步處理) ，此回復為「收集每個遙測類型的所有專案的 X%」。 

*取樣百分比會隨著時間變更嗎？*

* 是的，調適性取樣會根據目前觀察到的遙測量，逐漸變更取樣百分比。

*如果我使用固定取樣率，如何知道哪個取樣百分比最適合我的應用程式？*

* 開始使用調適性取樣的其中一個方法，就是找出它選擇的取樣率 (請參閱上一個問題)，然後再切換為使用該取樣率的固定取樣率。 
  
    否則，您就必須猜測。 分析 Application Insights 中您目前的遙測使用量、觀察目前的節流，並估計所收集之遙測的量。 這三項輸入與所選定價層，可對您可能想要減少收集的遙測量提出建議。 不過，使用者數目的增加或遙測量的其他某些變化可能會讓您的評估失效。

*如果將取樣百分比設定為太低時，會發生什麼事？*

* 過低的取樣百分比會導致過度過度取樣，當 Application Insights 嘗試補償資料量縮減的視覺效果時，會降低近似值的精確度。 此外，您的診斷體驗可能會受到負面影響，因為某些不常失敗或緩慢的要求可能會被取樣。

*如果將取樣百分比設定為太高時，會發生什麼事？*

* 設定太高的取樣百分比 (沒有足夠的) 會導致收集到的遙測量沒有足夠的縮減量。 您可能仍會遇到與節流相關的遙測資料遺失，而使用 Application Insights 的成本由於超額費可能高於您的計劃。

*我可以在何種平台上使用取樣？*

* 如果 SDK 未執行取樣，則擷取取樣會在任何遙測超過特定數量時自動運作。 例如，如果您使用較舊版本的 ASP.NET SDK 或 JAVA SDK，此設定將會運作。
* 如果您使用目前的 ASP.NET 或 ASP.NET Core Sdk (裝載于 Azure 或您自己的伺服器) 上，則預設會取得適應性取樣，但您可以依照上述步驟切換為固定速率。 使用固定取樣率，瀏覽器 SDK 會自動同步至取樣相關的事件。 
* 如果您使用目前的 JAVA 代理程式，您可以設定 `ApplicationInsights.json` JAVA SDK 的 (、設定 `ApplicationInsights.xml`) 來開啟固定速率取樣。 根據預設取樣功能為關閉。 使用固定速率取樣時，瀏覽器 SDK 和伺服器會自動同步處理至範例相關事件。

*我一律想要看見特定罕見的事件。我要如何讓它們通過取樣模組？*

* 達成此目標的最佳方式是撰寫自訂 [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)，將 `SamplingPercentage` 您想要保留的遙測專案上的設為100，如下所示。 當初始化運算式可保證在遙測處理器 (包括取樣) 之前執行，這可確保所有的取樣技術都會忽略此專案的任何取樣考慮。 自訂遙測初始化運算式可在 ASP.NET SDK、ASP.NET Core SDK、JavaScript SDK 和 JAVA SDK 中取得。 例如，您可以使用 ASP.NET SDK 設定遙測初始化運算式：

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>較舊的 SDK 版本

彈性取樣適用于適用于 ASP.NET v 2.0.0 的 Application Insights SDK-Beta3 和更新版本、ApplicationInsights. AspNetCore SDK v 2.2.0-Beta1 和更新版本，且預設為啟用。

固定速率取樣是2.0.0 和 JAVA SDK 2.0.1 版和更新版本的 ASP.NET 版本中的 SDK 功能。

在2.5.0 之前的 ASP.NET SDK Beta2，以及 ASP.NET Core SDK 的 v 2.2.0-Beta3 之前，取樣決策是根據定義 "user" (的應用程式使用者識別碼的雜湊，也就是最常見的 web 應用程式) 。 針對未定義使用者 (例如 web 服務的應用程式類型) 取樣決策是根據要求的作業識別碼。 ASP.NET 和 ASP.NET Core Sdk 的最新版本會使用運算識別碼進行取樣決策。

## <a name="next-steps"></a>接下來的步驟

* [篩選](./api-filtering-sampling.md) 可以對您的 SDK 所傳送的內容，提供更嚴格的控制。
* 閱讀開發人員網路文章， [利用 Application Insights 將遙測優化](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights)。

