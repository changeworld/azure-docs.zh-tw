---
title: Azure Application Insights 中的遙測取樣 | Microsoft Docs
description: 如何讓遙測量保持在控制下。
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: fc9db23f7733f97ca207e834d4543fbdb1b9db5c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275824"
---
# <a name="sampling-in-application-insights"></a>Application Insights 中的取樣

取樣是 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 中的一個功能。 建議的方法是減少遙測流量、資料成本和儲存體成本，同時保留統計正確的應用程式資料分析。 取樣也可協助您避免 Application Insights 節流遙測。 取樣篩選會選取相關的專案，以便您在進行診斷調查時，可以在專案之間流覽。

在入口網站中呈現計量計數時，會重新標準化它們來將取樣納入考慮。 這麼做可將對統計資料的影響降至最低。

## <a name="brief-summary"></a>簡短摘要

* 有三種不同類型的取樣：彈性取樣、固定速率取樣和內嵌取樣。
* 預設會在所有最新版本的 Application Insights ASP.NET 和 ASP.NET Core 軟體發展工具組（Sdk）中啟用調適型取樣。 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)也會使用它。
* 最新版本的 ASP.NET、ASP.NET Core、JAVA 和 Python 的 Application Insights Sdk 會提供固定速率取樣。
* 內嵌取樣適用于 Application Insights 服務端點。 只有在沒有其他取樣生效時才適用。 如果 SDK 會取樣您的遙測，則會停用內嵌取樣。
* 若是 web 應用程式，如果您記錄自訂事件，而且需要確保一組事件會一起保留或捨棄，事件就必須有相同的 `OperationId` 值。
* 如果您要撰寫分析查詢，請 [考慮到取樣](../../azure-monitor/log-query/aggregations.md)。 特別是，您應該使用 `summarize sum(itemCount)`，而非只計算記錄。
* 某些遙測類型（包括效能計量和自訂計量）一律會保留，不論是否已啟用取樣。

下表摘要說明每個 SDK 和應用程式類型的可用取樣類型：

| Application Insights SDK | 支援調適型取樣 | 支援的固定速率取樣 | 支援的內嵌取樣 |
|-|-|-|-|
| ASP.NET | [是（預設為開啟）](#configuring-adaptive-sampling-for-aspnet-applications) | [是](#configuring-fixed-rate-sampling-for-aspnet-applications) | 只有在沒有其他取樣生效時 |
| ASP.NET Core | [是（預設為開啟）](#configuring-adaptive-sampling-for-aspnet-core-applications) | [是](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | 只有在沒有其他取樣生效時 |
| Azure Functions | [是（預設為開啟）](#configuring-adaptive-sampling-for-azure-functions) | 否 | 只有在沒有其他取樣生效時 |
| Java | 否 | [是](#configuring-fixed-rate-sampling-for-java-applications) | 只有在沒有其他取樣生效時 |
| Python | 否 | [是](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | 只有在沒有其他取樣生效時 |
| All others | 否 | 否 | [是](#ingestion-sampling) |

> [!NOTE]
> 本頁面大部分的資訊適用于目前版本的 Application Insights Sdk。 如需舊版 Sdk 的詳細資訊，[請參閱下面的一節](#older-sdk-versions)。

## <a name="types-of-sampling"></a>取樣類型

有三種不同的取樣方法：

* 調適型**取樣**會自動調整您 ASP.NET/ASP.NET Core 應用程式中的 SDK 所傳送的遙測資料量，以及 Azure Functions。 當您使用 ASP.NET 或 ASP.NET Core SDK 時，這是預設的取樣。 調適型取樣目前僅適用于 ASP.NET 伺服器端遙測和 Azure Functions。

* **固定速率取樣**可減少從您的 ASP.NET 或 ASP.NET Core 或 JAVA 伺服器，以及從使用者的瀏覽器傳送的遙測量。 而比例則由您設定。 用戶端和伺服器會同步處理它們的取樣，讓您可以在 [搜尋] 終於相關的頁面檢視和要求之間瀏覽。

* 內嵌**取樣**會發生在 Application Insights 服務端點。 它會根據您設定的取樣比例，捨棄來自您應用程式的一些遙測。 這不會減少從應用程式傳送的遙測流量，但可協助您讓流量不要超過每月配額。 內嵌取樣的主要優點是您可以設定取樣率，而不需要重新部署您的應用程式。 內嵌取樣適用于所有伺服器和用戶端，但在作業中有任何其他類型的取樣時則不適用。

> [!IMPORTANT]
> 如果作業中有彈性或固定速率取樣方法，則會停用內嵌取樣。

## <a name="adaptive-sampling"></a>調適性取樣

調適性取樣會影響從您的 Web 伺服器應用程式傳送給 Application Insights 服務端點的遙測量。

> [!TIP]
> 當您使用 ASP.NET SDK 或 ASP.NET Core SDK 時，預設會啟用調適型取樣，而且預設也會針對 Azure Functions 啟用。

磁片區會自動調整以保留指定的最大流量速率，並透過 `MaxTelemetryItemsPerSecond`設定來控制。 如果應用程式產生較少的遙測資料（例如，在進行調試時或因為低使用量而造成），取樣處理器就不會卸載專案，只要磁片區低於 `MaxTelemetryItemsPerSecond`。 隨著遙測量的增加，取樣率會調整成達到目標磁片區。 調整會定期重新計算，並且根據外寄傳輸速率的移動平均。

為了要讓遙測量達到目標，系統會捨棄部分已產生的遙測。 但就跟其他取樣類型一樣，演算法會保留相關的遙測項目。 舉例來說，當您在 [搜尋] 中檢查遙測時，將能夠尋找與特定例外狀況相關的要求。

度量計量 (例如要求率及例外狀況率) 會受到調整來補償取樣率，讓它們能在計量瀏覽器中顯示大致上正確的值。

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>設定 ASP.NET 應用程式的調適型取樣

> [!NOTE]
> 本節適用于 ASP.NET 應用程式，而不是 ASP.NET Core 應用程式。 [瞭解如何在本檔稍後設定 ASP.NET Core 應用程式的調適型取樣。](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

在[`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)中，您可以調整 [`AdaptiveSamplingTelemetryProcessor`] 節點中的數個參數。 顯示的數字是預設值：

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    調整演算法對於 **每部伺服器主機**的目標速率。 如果 Web 應用程式在許多主機上執行，請減少此值，以保持在您的 Application Insights 入口網站的流量目標速率內。

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    重新評估目前遙測速率的間隔。 評估是以移動平均來執行。 如果您的遙測會突然暴增，您可能想要縮短此間隔。

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    當取樣百分比值變更時，我們可以在多久之後，再次降低取樣百分比來捕獲較少的資料？

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    當取樣百分比值變更時，我們可以在多久之後，再次增加取樣百分比來捕捉更多資料？

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    當取樣百分比不同時，我們可以設定的最小值為何？

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    當取樣百分比不同時，我們可以設定的最大值為何？

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    在 [移動平均] 的計算中，這會指定應該指派給最新值的權數。 使用等於或小於 1 的值。 較小的值會讓演算法不易受突然的變更影響。

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    應用程式剛開始時要取樣的遙測數量。 當您正在進行調試時，請勿降低此值。

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    以分號分隔的類型清單，您不想要進行取樣。 可辨識的類型為： `Dependency`、`Event`、`Exception`、`PageView`、`Request`、`Trace`。 會傳輸指定類型的所有遙測;將會取樣未指定的類型。

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    您想要進行取樣的類型清單（以分號分隔）。 可辨識的類型為： `Dependency`、`Event`、`Exception`、`PageView`、`Request`、`Trace`。 將會取樣指定的類型。其他類型的所有遙測一律會進行傳輸。

**若要關閉**調適型取樣，請從 `ApplicationInsights.config`移除 `AdaptiveSamplingTelemetryProcessor` 節點。

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>替代方式：在程式碼中設定調適型取樣

您可以透過程式設計方式設定這些值，而不是在 `.config` 檔中設定取樣參數。

1. 從 `.config` 檔案移除所有的 `AdaptiveSamplingTelemetryProcessor` 節點。
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

    ([深入了解遙測處理器](../../azure-monitor/app/api-filtering-sampling.md#filtering))。

您也可以個別調整每個遙測類型的取樣率，或甚至可以排除某些類型，使其完全不被取樣：

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>設定 ASP.NET Core 應用程式的調適型取樣

ASP.NET Core 應用程式沒有 `ApplicationInsights.config`，因此所有的設定都是透過程式碼來完成。
所有的 ASP.NET Core 應用程式預設會啟用調適型取樣。 您可以停用或自訂取樣行為。

#### <a name="turning-off-adaptive-sampling"></a>關閉調適型取樣

使用 `Startup.cs` 檔案中的 `ApplicationInsightsServiceOptions`，在方法 `ConfigureServices`中新增 Application Insights 服務時，可以停用預設的取樣功能：

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

上述程式碼會停用調適型取樣。 請遵循下列步驟使用更多自訂選項新增取樣。

#### <a name="configure-sampling-settings"></a>設定取樣設定

如下所示，使用 `TelemetryProcessorChainBuilder` 的擴充方法自訂取樣行為。

> [!IMPORTANT]
> 如果您使用這個方法來設定取樣，請務必在呼叫 `AddApplicationInsightsTelemetry()`時，將 `aiOptions.EnableAdaptiveSampling` 屬性設為 `false`。

```csharp
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

遵循[此頁面](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)中的指示，為在 Azure Functions 中執行的應用程式設定調適型取樣。

## <a name="fixed-rate-sampling"></a>固定速率取樣

固定速率取樣可減少從您的 web 伺服器和網頁瀏覽器傳送的流量。 但它會依照您設定的速率來降低遙測，這與調適性取樣不同。 固定速率取樣適用于 ASP.NET、ASP.NET Core、JAVA 和 Python 應用程式。

就像其他的取樣技術一樣，這也會保留相關的專案。 它也會同步處理用戶端和伺服器取樣，以便保留相關的專案，例如，當您在 [搜尋] 中查看頁面流覽時，您可以找到其相關的伺服器要求。 

在計量瀏覽器中，速率 (例如要求及例外狀況數) 會乘以某個係數來補償取樣率，讓它們能大致上正確。

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>設定 ASP.NET 應用程式的固定速率取樣

1. **停**用調適型取樣：在[`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)中，移除或批註 `AdaptiveSamplingTelemetryProcessor` 節點。

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **啟用固定取樣率模組。** 將此程式碼片段新增至[`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)：
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      或者，您可以透過程式設計方式設定這些值，而不是在 `ApplicationInsights.config` 檔案中設定取樣參數：

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

    ([深入了解遙測處理器](../../azure-monitor/app/api-filtering-sampling.md#filtering))。

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>設定 ASP.NET Core 應用程式的固定速率取樣

1. **停**用彈性取樣：可以使用 `ApplicationInsightsServiceOptions`，在 `ConfigureServices` 方法中進行變更：

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

2. **啟用固定取樣率模組。** 您可以在 `Configure` 方法中進行變更，如下列程式碼片段所示：

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

根據預設，JAVA SDK 中不會啟用任何取樣。 目前只支援固定速率取樣。 JAVA SDK 不支援調適型取樣。

1. 使用最新的[Application Insights JAVA SDK](../../azure-monitor/app/java-get-started.md)下載並設定您的 web 應用程式。

2. 藉由將下列程式碼片段新增至 `ApplicationInsights.xml` 檔案，**啟用固定速率取樣模組**：

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

3. 您可以使用 `Processor` 標籤的 `FixedRateSamplingTelemetryProcessor`內的下列標記，在取樣中包含或排除特定類型的遙測：
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

可以在取樣中包含或排除的遙測類型為： `Dependency`、`Event`、`Exception`、`PageView`、`Request`和 `Trace`。

> [!NOTE]
> 針對取樣百分比，選擇接近 100/N 的百分比，其中 N 是整數。  目前取樣並不支援其他值。

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>設定 OpenCensus Python 應用程式的固定速率取樣

使用最新的[OpenCensus Azure 監視器匯出工具](../../azure-monitor/app/opencensus-python.md)檢測您的應用程式。

> [!NOTE]
> 計量匯出工具無法使用固定速率取樣。 這表示自訂計量是唯一無法設定取樣的遙測類型。 計量匯出程式會傳送它所追蹤的所有遙測。

#### <a name="fixed-rate-sampling-for-tracing"></a>追蹤的固定速率取樣 ####
您可以指定 `sampler` 作為 `Tracer` 設定的一部分。 如果未提供明確的取樣器，預設會使用 `ProbabilitySampler`。 根據預設，`ProbabilitySampler` 會使用1/10000 的速率，這表示每個10000要求的其中一個會傳送至 Application Insights。 如果您想要指定取樣率，請參閱下方內容。

若要指定取樣率，請確定您的 `Tracer` 會指定取樣率介於0.0 和1.0 （含）之間的取樣器。 取樣率1.0 代表100%，這表示您的所有要求都會以遙測傳送至 Application Insights。

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>記錄的固定速率取樣 ####
您可以藉由修改 `logging_sampling_rate` 選擇性引數，來設定 `AzureLogHandler` 的固定速率取樣。 如果未提供任何引數，則會使用取樣率1.0。 取樣率1.0 代表100%，這表示您的所有要求都會以遙測傳送至 Application Insights。

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>使用 JavaScript 設定網頁的固定速率取樣

以 JavaScript 為基礎的 web 網頁可以設定為使用 Application Insights。 遙測會從在使用者的瀏覽器中執行的用戶端應用程式傳送，而且可以從任何伺服器主控這些頁面。

當您[針對 Application Insights 設定以 javascript 為基礎的網頁](javascript.md)時，請修改您從 Application Insights 入口網站取得的 javascript 程式碼片段。

> [!TIP]
> 在包含 JavaScript 的 ASP.NET 應用程式中，程式碼片段通常會進入 `_Layout.cshtml`。

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

用戶端 JavaScript SDK 會與伺服器端 SDK 一起參與固定速率取樣。 已檢測的頁面只會從伺服器端 SDK 決定要包含在取樣中的相同使用者，傳送用戶端遙測。 這個邏輯的設計目的是要維護用戶端和伺服器端應用程式之間的使用者會話完整性。 因此，從 Application Insights 中的任何特定遙測專案，您都可以找到此使用者或會話的所有其他遙測專案，並在搜尋中，您可以在相關的頁面流覽和要求之間導覽。

如果您的用戶端和伺服器端遙測不會顯示協調的範例：

* 確認您已在伺服器和用戶端上啟用取樣。
* 請檢查您的用戶端和伺服器中設定相同的取樣百分比。
* 確定 SDK 版本為 2.0 或更新版本。

## <a name="ingestion-sampling"></a>擷取取樣

內嵌取樣會在來自 web 伺服器、瀏覽器和裝置的遙測到達 Application Insights 服務端點的位置運作。 雖然它不會減少來自您應用程式的遙測流量，但會減少 Application Insights 所處理及保留 (並收費) 的遙測量。

如果您的應用程式通常會超過每月配額，且您無法選擇使用任何一種 SDK 式的取樣類型，請使用這種類型的取樣。 

在 [使用量和估計成本] 頁面中設定取樣率：

![從應用程式的 [總覽] 分頁，按一下 [設定]、[配額]、[範例]，然後選取取樣率，再按一下 [更新]。](./media/sampling/data-sampling.png)

就跟其他取樣類型一樣，演算法會保留相關的遙測項目。 舉例來說，當您在 [搜尋] 中檢查遙測時，將能夠尋找與特定例外狀況相關的要求。 度量計量 (例如要求率及例外狀況率) 會正確地保留。

遭到取樣捨棄的資料點將無法在任何 Application Insights 功能中使用，例如 [連續匯出](../../azure-monitor/app/export-telemetry.md)。

當調適型或固定速率取樣正在運作時，內嵌取樣不會運作。 當使用 ASP.NET SDK 或 ASP.NET Core SDK，或在[Azure App Service](azure-web-apps.md)或使用狀態監視器啟用 Application Insights 時，預設會啟用調適型取樣。 當 Application Insights 服務端點收到遙測時，它會檢查遙測資料，如果取樣率報告為小於100% （表示正在取樣遙測），則會忽略您設定的內嵌取樣率。

> [!WARNING]
> [入口網站] 磚上顯示的值會指出您為 [內嵌取樣] 所設定的值。 如果有任何類型的 SDK 取樣（彈性或固定速率取樣）正在進行中，則不代表實際的取樣率。

## <a name="when-to-use-sampling"></a>使用取樣的時機

一般來說，對於大部分的小型和中型應用程式，您不需要取樣。 最有用的診斷資訊和最準確的統計資料會是透過收集所有使用者活動的資料取得。 

取樣的主要優點如下：

* 當您的應用程式在短時間內傳送非常高的遙測速率時，Application Insights 服務會卸載（「節流」）資料點。 取樣可減少應用程式看到節流的可能性。
* 保持在定價層的資料點 [配額](pricing.md) 內。 
* 若要從收集的遙測降低網路流量。 

### <a name="which-type-of-sampling-should-i-use"></a>我應該使用哪種類型的取樣？

**如果是下列情形，請使用擷取取樣：**

* 您通常會使用每月的遙測配額。
* 您從使用者的網頁瀏覽器取得了太多遙測。
* 您使用不支援取樣的 SDK 版本，例如比 ASP.NET 版本 2 早的版本。

**如果是下列情形，則使用固定取樣率：**

* 您想要同步處理用戶端與伺服器之間的取樣，因此，當您在[搜尋](../../azure-monitor/app/diagnostic-search.md)中調查事件時，您可以在用戶端和伺服器上的相關事件之間流覽，例如頁面流覽和 HTTP 要求。
* 您對於您的應用程式的適當取樣百分比有信心。 應該夠高以取得精確的度量，但是低於超過價格配額和節流限制的取樣率。

**使用調適性取樣：**

如果欲使用其他形式取樣但條件不適用，建議使用適應型取樣。 在 ASP.NET/ASP.NET Core SDK 中，預設會啟用此設定。 在達到特定最小速率之前，它不會降低流量，因此可能完全不會取樣低用途的網站。

## <a name="knowing-whether-sampling-is-in-operation"></a>瞭解取樣是否正在運作

若要找出實際的取樣率 (不論是否已套用)，請使用如下所示的 [分析查詢](../../azure-monitor/app/analytics.md) ︰

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

如果您看到任何類型的 `RetainedPercentage` 小於100，則會取樣該類型的遙測。

> [!IMPORTANT]
> Application Insights 不會取樣會話、計量（包括自訂計量），或任何取樣技術中的效能計數器遙測類型。 這些類型一律會從取樣中排除，因為這些遙測類型的精確度不會太高。

## <a name="how-sampling-works"></a>取樣的運作方式

取樣演算法會決定要卸載哪些遙測專案，以及要保留哪些。 無論是由 SDK 還是在 Application Insights 服務中進行取樣，都是如此。 取樣決策會根據數個規則，目標是要保留相關的資料點不變，在 Application Insights 中保有可採取動作而且即使有縮減資料集仍可靠的診斷經驗。 例如，如果您的應用程式包含在範例中的失敗要求，則會保留額外的遙測專案（例如針對此要求所記錄的例外狀況和追蹤）。 取樣會同時保留或卸載它們。 如此一來，當您在 Application Insights 中查看要求詳細資料時，您一律可以看到要求和其相關聯的遙測項目。

取樣決策是以要求的作業識別碼為基礎，這表示屬於特定作業的所有遙測專案都會保留或卸載。 針對沒有設定作業識別碼的遙測專案（例如，從非同步執行緒報告但沒有 HTTP 內容的遙測專案），取樣只會取得每個類型之遙測專案的百分比。

呈現遙測回來給您時，Application Insights 服務會以收集時使用的相同取樣百分比調整度量，來彌補遺漏的資料點。 因此，在 Application Insights 中查看遙測時，使用者會看到統計正確也非常接近實際數的近似值。

近似值的精確度絕大部分取決於設定的取樣百分比。 此外，對於處理大量使用者的通常類似要求的應用程式，其精確度會增加。 相反地，對於不處理大量負載的應用程式，就不需要取樣，因為這些應用程式通常可以傳送遙測同時保持在配額內，而不會因節流造成資料遺失。 

## <a name="frequently-asked-questions"></a>常見問題集

*ASP.NET 和 ASP.NET Core Sdk 中的預設取樣行為為何？*

* 如果您使用上述 SDK 的其中一個最新版本，則預設會啟用調適型取樣，每秒有五個遙測專案。
  預設會加入兩個 `AdaptiveSamplingTelemetryProcessor` 節點，其中一個會包含取樣中的 `Event` 類型，而另一個則會排除取樣中的 `Event` 類型。 此設定表示 SDK 會嘗試將遙測專案限制為 `Event` 類型的五個遙測專案，以及結合所有其他類型的五個遙測專案，藉此確保 `Events` 會與其他遙測類型分開進行取樣。 事件通常用於商務遙測，而且很可能不會受到診斷遙測磁片區的影響。
  
  以下顯示產生的預設 `ApplicationInsights.config` 檔案。 在 ASP.NET Core 中，會在程式碼中啟用相同的預設行為。 使用[此頁面先前章節中的範例](#configuring-adaptive-sampling-for-aspnet-core-applications)來變更此預設行為。

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

*可以多次取樣遙測嗎？*

* 否。 如果已取樣專案，SamplingTelemetryProcessors 會忽略取樣考慮的專案。 同時也適用于內嵌取樣，這不會對已在 SDK 本身取樣的專案套用取樣。

*為什麼不取樣簡單的「收集每個遙測類型百分之 X」？*

* 雖然此取樣方法會提供較高等級的計量近似值，但它會破壞每個使用者、會話和要求的診斷資料相互關聯的能力，這對於診斷很重要。 因此，取樣會更適合「收集應用程式使用者的所有遙測專案」之類的原則，或「收集應用程式要求 X% 的所有遙測」。 對於未與要求相關聯的遙測專案（例如背景非同步處理），回溯是「收集每個遙測類型的 X% 的所有專案」。 

*取樣百分比會隨著時間變更嗎？*

* 是的，調適性取樣會根據目前觀察到的遙測量，逐漸變更取樣百分比。

*如果我使用固定取樣率，如何知道哪個取樣百分比最適合我的應用程式？*

* 開始使用調適性取樣的其中一個方法，就是找出它選擇的取樣率 (請參閱上一個問題)，然後再切換為使用該取樣率的固定取樣率。 
  
    否則，您就必須猜測。 分析 Application Insights 中您目前的遙測使用量、觀察目前的節流，並估計所收集之遙測的量。 這三項輸入與所選定價層，可對您可能想要減少收集的遙測量提出建議。 不過，使用者數目的增加或遙測量的其他某些變化可能會讓您的評估失效。

*如果我將取樣百分比設定得太低，會發生什麼事？*

* 過度低取樣百分比會造成過度積極的取樣，並在 Application Insights 嘗試補償資料的視覺效果以縮減資料量時，降低近似值的精確度。 此外，您的診斷體驗可能會受到負面影響，因為可能會取樣一些不常失敗或緩慢的要求。

*如果我將取樣百分比設定得太高，會發生什麼事？*

* 設定太高的取樣百分比（不夠積極），會導致收集的遙測磁片區中的縮減量不足。 您可能仍會遇到與節流相關的遙測資料遺失，而使用 Application Insights 的成本由於超額費可能高於您的計劃。

*我可以在何種平台上使用取樣？*

* 如果 SDK 未執行取樣，則擷取取樣會在任何遙測超過特定數量時自動運作。 例如，如果您使用較舊版本的 ASP.NET SDK 或 JAVA SDK，這項設定就會正常執行。
* 如果您使用目前的 ASP.NET 或 ASP.NET Core Sdk （裝載于 Azure 或您自己的伺服器上），則預設會取得彈性取樣，但是您可以切換到固定速率，如上所述。 使用固定取樣率，瀏覽器 SDK 會自動同步至取樣相關的事件。 
* 如果您使用的是目前的 JAVA SDK，您可以設定 `ApplicationInsights.xml` 來開啟固定速率取樣。 根據預設取樣功能為關閉。 使用固定速率取樣時，瀏覽器 SDK 和伺服器會自動同步處理至範例相關事件。

*我一律想要看見特定罕見的事件。我要如何讓它們通過取樣模組？*

* 達成此目標的最佳方式是撰寫自訂[TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)，將您要保留的遙測專案上的 `SamplingPercentage` 設為100，如下所示。 因為初始化運算式保證會在遙測處理器（包括取樣）之前執行，所以這可確保所有取樣技術都會忽略任何取樣考慮的這個專案。 自訂遙測初始化運算式可在 ASP.NET SDK、ASP.NET Core SDK、JavaScript SDK 和 JAVA SDK 中取得。 例如，您可以使用 ASP.NET SDK 來設定遙測初始化運算式：

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

彈性取樣適用于 ASP.NET v 2.0.0 的 Application Insights SDK-Beta3 和更新版本，Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-Beta1 和更新版本，預設會啟用。

固定速率取樣是2.0.0 和 JAVA SDK 2.0.1 版和更新版本的 ASP.NET 版本中的一項 SDK 功能。

在2.5.0 之前的 ASP.NET SDK Beta2 和 v 2.2.0-Beta3 的 ASP.NET Core SDK 中，取樣決策是以定義「使用者」（也就是最常見的 web 應用程式）之應用程式的使用者識別碼雜湊為基礎。 針對未定義使用者的應用程式類型（例如 web 服務），取樣決策是根據要求的作業識別碼。 最新版本的 ASP.NET 和 ASP.NET Core Sdk 會使用作業識別碼進行取樣決策。

## <a name="next-steps"></a>後續步驟

* [篩選](../../azure-monitor/app/api-filtering-sampling.md) 可以對您的 SDK 所傳送的內容，提供更嚴格的控制。
* 閱讀開發人員網路文章[使用 Application Insights 優化遙測](https://msdn.microsoft.com/magazine/mt808502.aspx)。
