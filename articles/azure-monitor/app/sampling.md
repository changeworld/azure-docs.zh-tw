---
title: Azure Application Insights 中的遙測取樣 | Microsoft Docs
description: 如何讓遙測量保持在控制下。
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 5e888e0606b7a9bcd9a7a94c28455d705c5f1bec
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255476"
---
# <a name="sampling-in-application-insights"></a>Application Insights 中的取樣

取樣是 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 中的一個功能。 它是減少遙測流量、數據成本和存儲成本,同時保留對應用程序數據進行統計正確分析的建議方法。 採樣還有助於避免應用程式見解限制遙測。 取樣篩選器選擇相關的項,以便在執行診斷調查時可以在項之間導航。

當指標計數在門戶中顯示時,將重新規範化,以考慮採樣。 這樣做會最大限度地減少對統計資訊的任何影響。

## <a name="brief-summary"></a>小結

* 有三種不同類型的採樣:自適應採樣、固定速率採樣和引入採樣。
* 默認情況下,在所有最新版本的應用程式見解ASP.NET和ASP.NET核心軟體開發工具組 (SDK) 中啟用自適應採樣。 它也被 Azure[函數](https://docs.microsoft.com/azure/azure-functions/functions-overview)使用。
* 最新版本的應用程式見解 SDK 可用於ASP.NET、ASP.NET核心、JAVA(代理和 SDK)和 Python。
* 引入採樣適用於應用程式見解服務終結點。 僅當沒有其他採樣有效時,它才適用。 如果 SDK 對遙測進行採樣,則禁用引入採樣。
* 對於 Web 應用程式,如果記錄自定義事件,並且需要確保一組事件保留或丟棄在一起,則事件必須具有`OperationId`相同的 值。
* 如果您要撰寫分析查詢，請 [考慮到取樣](../../azure-monitor/log-query/aggregations.md)。 特別是，您應該使用 `summarize sum(itemCount)`，而非只計算記錄。
* 無論是否啟用採樣,都會保留某些遙測類型,包括性能指標和自定義指標。

下表總結了每個 SDK 可用的取樣型態和應用程式類型:

| Application Insights SDK | 支援自我調整取樣 | 支援固定速率採樣 | 支援引入取樣樣 |
|-|-|-|-|
| ASP.NET | [是(預設的視窗開啟)](#configuring-adaptive-sampling-for-aspnet-applications) | [是](#configuring-fixed-rate-sampling-for-aspnet-applications) | 僅當沒有其他採樣有效時 |
| ASP.NET Core | [是(預設的視窗開啟)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [是](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | 僅當沒有其他採樣有效時 |
| Azure Functions | [是(預設的視窗開啟)](#configuring-adaptive-sampling-for-azure-functions) | 否 | 僅當沒有其他採樣有效時 |
| Java | 否 | [是](#configuring-fixed-rate-sampling-for-java-applications) | 僅當沒有其他採樣有效時 |
| Python | 否 | [是](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | 僅當沒有其他採樣有效時 |
| All others | 否 | 否 | [是](#ingestion-sampling) |

> [!NOTE]
> 此頁面的大部分資訊適用於應用程式見解 SDK 的當前版本。 關於舊版本的 SDK 的資訊,[請參考以下部分](#older-sdk-versions)。

## <a name="types-of-sampling"></a>取樣類型

有三種不同的採樣方法:

* **自適應取樣**會自動調整從 ASP.NET/ASP.NET核心應用和 Azure 函數中 SDK 發送的遙測量。 這是使用ASP.NET或ASP.NET核心 SDK 時的預設採樣。 自適應取樣目前僅適用於ASP.NET伺服器端遙測和 Azure 函數。

* **固定利率採樣**可減少從ASP.NET或ASP.NET核心或JAVA伺服器以及使用者瀏覽器發送的遙測量。 而比例則由您設定。 用戶端和伺服器會同步處理它們的取樣，讓您可以在 [搜尋] 終於相關的頁面檢視和要求之間瀏覽。

* **引入採樣**發生在應用程式見解服務終結點。 它會根據您設定的取樣比例，捨棄來自您應用程式的一些遙測。 這不會減少從應用程式傳送的遙測流量，但可協助您讓流量不要超過每月配額。 引入採樣的主要優點是,您可以設置採樣率,而無需重新部署應用。 引入採樣適用於所有伺服器和用戶端,但在任何其他類型的採樣運行時,它不適用。

> [!IMPORTANT]
> 如果自適應或固定速率採樣方法在運行中,則禁用引入採樣。

## <a name="adaptive-sampling"></a>調適性取樣

調適性取樣會影響從您的 Web 伺服器應用程式傳送給 Application Insights 服務端點的遙測量。

> [!TIP]
> 默認情況下,當您使用ASP.NET SDK 或 ASP.NET核心 SDK 時啟用自適應採樣,並且默認情況下也為 Azure 函數啟用自適應採樣。

自動調整音量以保持在指定的最大流量速率內,並通過設置`MaxTelemetryItemsPerSecond`進行控制。 如果應用程式生成的遙測量較低(例如調試時或由於使用率低),則只要卷低於`MaxTelemetryItemsPerSecond`,採樣處理器就不會刪除專案。 隨著遙測量的增加,對採樣速率進行調整,以達到目標體積。 調整會定期重新計算，並且根據外寄傳輸速率的移動平均。

為了要讓遙測量達到目標，系統會捨棄部分已產生的遙測。 但就跟其他取樣類型一樣，演算法會保留相關的遙測項目。 舉例來說，當您在 [搜尋] 中檢查遙測時，將能夠尋找與特定例外狀況相關的要求。

度量計量 (例如要求率及例外狀況率) 會受到調整來補償取樣率，讓它們能在計量瀏覽器中顯示大致上正確的值。

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>設定ASP.NET應用程式的自我調整取樣

> [!NOTE]
> 本節適用於ASP.NET應用程式,不適用於ASP.NET核心應用程式。 [瞭解本文件稍後將ASP.NET核心應用程式的自適應採樣配置。](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

在[`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)中,可以調整節點中的`AdaptiveSamplingTelemetryProcessor`多個參數。 顯示的數字是預設值：

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    調整演算法對於 **每部伺服器主機**的目標速率。 如果 Web 應用程式在許多主機上執行，請減少此值，以保持在您的 Application Insights 入口網站的流量目標速率內。

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    重新評估當前遙測速率的時間間隔。 評估是以移動平均來執行。 如果您的遙測會突然暴增，您可能想要縮短此間隔。

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    當採樣百分比值更改時,我們多久才能再次降低採樣百分比以捕獲更少的數據?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    當採樣百分比值更改時,我們多久才能再次增加採樣百分比以捕獲更多數據?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    隨著採樣百分比的不同,允許我們設置的最小值是多少?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    隨著採樣百分比的不同,允許我們設置的最大值是多少?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    在計算移動平均線時,這指定應分配給最新值的權重。 使用等於或小於 1 的值。 較小的值會讓演算法不易受突然的變更影響。

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    應用剛剛開始時要採樣的遙測量。 在調試時不要降低此值。

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    不希望被採樣的類型的分號分隔清單。 識別的類型是: `Dependency` `Event` `Exception` `PageView`、 `Request` `Trace`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 傳輸指定類型的所有遙測數據;將採樣未指定的類型。

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    分號分隔清單,您確實要接受採樣。 識別的類型是: `Dependency` `Event` `Exception` `PageView`、 `Request` `Trace`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 將採樣指定的類型;將始終傳輸其他類型的所有遙測數據。

**要關閉**自適應取樣,`AdaptiveSamplingTelemetryProcessor`請`ApplicationInsights.config`從中刪除節點。

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>替代:在代碼中設定自我調整取樣

您可以以程式設計方式設定這些值,`.config`而不是設定檔中的取樣參數。

1. 從`.config`檔案中`AdaptiveSamplingTelemetryProcessor`刪除 所有節點。
2. 使用以下代碼設定自我調整取樣:

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

您還可以單獨調整每個遙測類型的採樣率,甚至可以排除對特定類型的採樣:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>為ASP.NET核心應用配置自我調整取樣

ASP.NET核心應用程式`ApplicationInsights.config`沒有,因此所有配置都是通過代碼完成的。
所有的 ASP.NET Core 應用程式預設會啟用調適型取樣。 您可以停用或自訂取樣行為。

#### <a name="turning-off-adaptive-sampling"></a>關閉自我調整取樣

在方法`ConfigureServices``ApplicationInsightsServiceOptions`中新增應用程式見解服務時,`Startup.cs`可以在 檔案中關閉預設取樣功能:

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

上述代碼將禁用自適應採樣。 請遵循下列步驟使用更多自訂選項新增取樣。

#### <a name="configure-sampling-settings"></a>設定取樣設定

如下所示，使用 `TelemetryProcessorChainBuilder` 的擴充方法自訂取樣行為。

> [!IMPORTANT]
> 如果使用此方法設定取樣,請確保在呼叫`aiOptions.EnableAdaptiveSampling``false``AddApplicationInsightsTelemetry()`時設定為 。

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

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>為 Azure 函數設定自我調整取樣

按照[此頁面](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)中的說明為在 Azure 函數中運行的應用配置自適應採樣。

## <a name="fixed-rate-sampling"></a>固定利率抽樣

固定利率採樣減少了從 Web 伺服器和 Web 瀏覽器發送的流量。 但它會依照您設定的速率來降低遙測，這與調適性取樣不同。 固定速率採樣可用於ASP.NET、ASP.NET核心、JAVA 和Python 應用程式。

與其他採樣技術一樣,這還會保留相關專案。 它還同步用戶端和伺服器採樣,以便保留相關項 - 例如,當您在「搜尋」中查看頁面檢視時,可以找到其相關的伺服器請求。 

在計量瀏覽器中，速率 (例如要求及例外狀況數) 會乘以某個係數來補償取樣率，讓它們能大致上正確。

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>為ASP.NET應用程式設定固定速率採樣

1. **禁用自適應採樣**:[`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)在 中,刪除或`AdaptiveSamplingTelemetryProcessor`註釋 出節點。

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **啟用固定取樣率模組。** 將此代碼段[`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)新增到 :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      或,您可以以編程設定以下值,`ApplicationInsights.config`而不是設定檔中的取樣參數:

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

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>為ASP.NET核心應用程式設定固定速率採樣

1. **關閉自我調整取樣 :** 可以使用`ConfigureServices``ApplicationInsightsServiceOptions`

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

2. **啟用固定取樣率模組。** 可以在方法中進行變更,`Configure`如下面的代碼段所示:

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

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>為 Java 應用程式設定固定速率取樣

默認情況下,JAVA 代理和 SDK 中未啟用採樣。 目前它僅支援固定速率採樣。 Java 不支援自適應採樣。

#### <a name="configuring-java-agent"></a>設定 Java 代理程式

1. 下載[應用程式見解-代理-3.0.0-PREVIEW.2.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.2/applicationinsights-agent-3.0.0-PREVIEW.2.jar)

1. 要開啟取樣,請加入以下`ApplicationInsights.json`內容:

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

#### <a name="configuring-java-sdk"></a>設定 Java SDK

1. 使用最新的[應用程式見解 Java SDK](../../azure-monitor/app/java-get-started.md)下載和設定您的 Web 應用程式。

2. 以將以下代碼段新增到`ApplicationInsights.xml`檔案,**啟用固定利率取樣模組**:

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

3. 您可以使用`Processor`標籤中的以下標記從取樣中包括或排除特定類型的遙測`FixedRateSamplingTelemetryProcessor`資料:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

可以包含或從取樣中排除的遙測`Dependency`型態是`Event``Exception``PageView``Request`:`Trace`、、、、、、和 。

> [!NOTE]
> 針對取樣百分比，選擇接近 100/N 的百分比，其中 N 是整數。  目前取樣並不支援其他值。

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>為 OpenCensus Python 應用程式設定固定速率取樣

使用最新的[OpenCensus Azure 監視器匯出器](../../azure-monitor/app/opencensus-python.md)檢測應用程式。

> [!NOTE]
> 指標導出器不提供固定利率抽樣。 這意味著自定義指標是唯一無法配置採樣的遙測類型。 指標匯出器將發送它跟蹤的所有遙測數據。

#### <a name="fixed-rate-sampling-for-tracing"></a>追蹤的固定速率取樣 ####
您可以指定 `sampler` 作為 `Tracer` 設定的一部分。 如果未提供顯示式取樣器,則預設情況下會使用`ProbabilitySampler`。 默認情況下`ProbabilitySampler`,將使用 1/10000 的速率,這意味著每 10000 個請求中,有一個將發送到應用程式見解。 如果您想要指定取樣率，請參閱下方內容。

要指定採樣率,請確保`Tracer`指定採樣率介於 0.0 和 1.0(包括)之間的採樣器。 採樣率為 1.0 表示 100%,這意味著所有請求都將作為遙測發送到應用程式見解。

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>紀錄的固定速率取樣 ####
您可以通過`AzureLogHandler``logging_sampling_rate`修改 可選參數來配置固定速率採樣。 如果未提供參數,將使用採樣率為 1.0。 採樣率為 1.0 表示 100%,這意味著所有請求都將作為遙測發送到應用程式見解。

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>使用 JavaScript 設定網頁的固定速率取樣

基於 JavaScript 的網頁可以配置為使用應用程式見解。 遙測是從在使用者的瀏覽器中運行的用戶端應用程式發送的,並且頁面可以從任何伺服器託管。

當您[為應用程式見解配置基於 JavaScript 的網頁](javascript.md)時,請修改從應用程式見解門戶獲取的 JavaScript 代碼段。

> [!TIP]
> 在包含 JavaScript ASP.NET 應用中,程式碼`_Layout.cshtml`段通常會進入 。

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

#### <a name="coordinating-server-side-and-client-side-sampling"></a>協調伺服器端和客戶端取樣

用戶端 JavaScript SDK 與伺服器端 SDK 一起參與固定利率採樣。 檢測的頁面將僅從伺服器端 SDK 決定包括在採樣中的同一使用者發送用戶端遙測數據。 此邏輯旨在維護用戶端和伺服器端應用程式的使用者會話的完整性。 因此,從應用程式見解中的任何特定遙測項中,您可以找到此使用者或工作階段的所有其他遙測項,在「搜尋」中,您可以在相關頁面檢視和請求之間導航。

如果客戶端與伺服器端遙測不顯示協調的範例:

* 驗證是否在伺服器和用戶端上啟用了採樣。
* 請檢查您的用戶端和伺服器中設定相同的取樣百分比。
* 確定 SDK 版本為 2.0 或更新版本。

## <a name="ingestion-sampling"></a>擷取取樣

引入採樣在 Web 伺服器、瀏覽器和設備的遙測到達應用程式見解服務終結點時運行。 雖然它不會減少來自您應用程式的遙測流量，但會減少 Application Insights 所處理及保留 (並收費) 的遙測量。

如果您的應用程式通常會超過每月配額，且您無法選擇使用任何一種 SDK 式的取樣類型，請使用這種類型的取樣。 

在 [使用量和估計成本] 頁面中設定取樣率：

![從應用程式的「概述」邊欄選項卡中,按一下「設置」、「配額」、「示例」,然後選擇採樣率,然後按一下「更新」。](./media/sampling/data-sampling.png)

就跟其他取樣類型一樣，演算法會保留相關的遙測項目。 舉例來說，當您在 [搜尋] 中檢查遙測時，將能夠尋找與特定例外狀況相關的要求。 度量計量 (例如要求率及例外狀況率) 會正確地保留。

遭到取樣捨棄的資料點將無法在任何 Application Insights 功能中使用，例如 [連續匯出](../../azure-monitor/app/export-telemetry.md)。

在自適應或固定速率採樣運行時,引入採樣無法運行。 默認情況下,當使用 ASP.NET SDK 或 ASP.NET核心 SDK 時,或者在[Azure 應用服務](azure-web-apps.md)中啟用應用程式見解或使用狀態監視器啟用應用程式見解時,將啟用自我適應取樣。 當應用程式見解服務終結點接收遙測時,它會檢查遙測數據,如果採樣率報告小於 100%(指示正在採樣遙測),則忽略您設置的引入採樣率。

> [!WARNING]
> 門戶磁貼上顯示的值指示為引入採樣設置的值。 如果任何類型的 SDK 採樣(自適應或固定速率採樣)正在運行,它不表示實際採樣率。

## <a name="when-to-use-sampling"></a>何時使用取樣

通常,對於大多數中小型應用程式,您不需要採樣。 最有用的診斷資訊和最準確的統計資料會是透過收集所有使用者活動的資料取得。 

取樣的主要優點如下：

* 當應用在短時間內發送非常高的遙測速率時,應用程式見解服務會丟棄("限制")數據點。 採樣降低了應用程序出現限制的可能性。
* 保持在定價層的資料點 [配額](pricing.md) 內。 
* 若要從收集的遙測降低網路流量。 

### <a name="which-type-of-sampling-should-i-use"></a>我應該使用哪種類型的取樣？

**如果是下列情形，請使用擷取取樣：**

* 您經常使用每月的遙測配額。
* 從使用者的 Web 瀏覽器中獲取的遙測數據過多。
* 您使用不支援取樣的 SDK 版本，例如比 ASP.NET 版本 2 早的版本。

**如果是下列情形，則使用固定取樣率：**

* 您希望用戶端和伺服器之間同步採樣,以便在[搜索](../../azure-monitor/app/diagnostic-search.md)中調查事件時,可以在用戶端和伺服器上的相關事件(如頁面視圖和 HTTP 請求)之間導航。
* 您對於您的應用程式的適當取樣百分比有信心。 應該夠高以取得精確的度量，但是低於超過價格配額和節流限制的取樣率。

**使用調適性取樣：**

如果欲使用其他形式取樣但條件不適用，建議使用適應型取樣。 默認情況下,此設置在ASP.NET/ASP.NET核心SDK中啟用。 在達到某個最低速率之前,它將不會減少流量,因此,低使用網站可能不會被採樣。

## <a name="knowing-whether-sampling-is-in-operation"></a>瞭解取樣是否正在執行

若要找出實際的取樣率 (不論是否已套用)，請使用如下所示的 [分析查詢](../../azure-monitor/app/analytics.md) ︰

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

如果看到`RetainedPercentage`任何類型小於 100,則對該類型的遙測進行採樣。

> [!IMPORTANT]
> 應用程式見解不會在任何採樣技術中對會話、指標(包括自定義指標)或性能計數器遙測類型進行採樣。 這些類型始終被排除在採樣之外,因為對於這些遙測類型來說,精度的降低可能非常不可取。

## <a name="how-sampling-works"></a>採樣的工作原理

採樣演算法決定要刪除的遙測項以及保留哪些遙測項。 無論是 SDK 還是應用程式見解服務完成採樣,都是如此。 取樣決策會根據數個規則，目標是要保留相關的資料點不變，在 Application Insights 中保有可採取動作而且即使有縮減資料集仍可靠的診斷經驗。 例如,如果應用在示例中包含失敗的請求,則將保留為此請求記錄的其他遙測項(如異常和跟蹤)。 採樣要麼保持或將它們放在一起。 如此一來，當您在 Application Insights 中查看要求詳細資料時，您一律可以看到要求和其相關聯的遙測項目。

採樣決策基於請求的操作 ID,這意味著屬於特定操作的所有遙測項都將保留或丟棄。 對於沒有操作 ID 集的遙測項(例如從沒有 HTTP 上下文的非同步線程報告的遙測項),採樣只需捕獲每種類型的遙測項的百分比。

呈現遙測回來給您時，Application Insights 服務會以收集時使用的相同取樣百分比調整度量，來彌補遺漏的資料點。 因此，在 Application Insights 中查看遙測時，使用者會看到統計正確也非常接近實際數的近似值。

近似值的精確度絕大部分取決於設定的取樣百分比。 此外，對於處理大量使用者的通常類似要求的應用程式，其精確度會增加。 相反地，對於不處理大量負載的應用程式，就不需要取樣，因為這些應用程式通常可以傳送遙測同時保持在配額內，而不會因節流造成資料遺失。 

## <a name="frequently-asked-questions"></a>常見問題集

*ASP.NET和 ASP.NET核心 SDK 中的預設採樣行為是什麼?*

* 如果使用上述 SDK 的最新版本之一,默認情況下啟用自適應採樣,每秒使用五個遙測項。
  默認情況下添加兩`AdaptiveSamplingTelemetryProcessor`個節點,一個節點包括採樣`Event`類型,另一個節點從採樣中`Event`排除 類型。 此配置意味著 SDK 將嘗試將遙測項限制`Event`為五 個類型的遙測項,以及合併所有其他類型的五個遙測項`Events`,從而確保 與其他遙測類型分開採樣。 事件通常用於業務遙測,並且很可能不應受診斷遙測卷的影響。
  
  下面顯示了生成的預設`ApplicationInsights.config`檔。 在ASP.NET核心中,代碼中啟用了相同的默認行為。 使用[本頁前面部分中的範例](#configuring-adaptive-sampling-for-aspnet-core-applications)更改此預設行為。

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

*遙測可以多次採樣嗎?*

* 否。 如果已採樣物料,採樣遙測處理器將忽略採樣注意事項中的項。 引入採樣也是如此,因為引入採樣不會將採樣應用於 SDK 本身中已採樣的專案。

*為什麼不取樣簡單的「收集每個遙測類型百分之 X」？*

* 雖然這種採樣方法將提供高水平的指標近似精度,但它將破壞關聯每個使用者、會話和請求的診斷數據的能力,這對於診斷至關重要。 因此,採樣適用於"為 X% 的應用使用者收集所有遙測項" 或" 為 X% 的應用請求收集所有遙測" 等策略。 對於未與請求關聯的遙測項(如後台異步處理),回退是「收集每種遙測類型所有項的 X%。 

*取樣百分比會隨著時間變更嗎？*

* 是的，調適性取樣會根據目前觀察到的遙測量，逐漸變更取樣百分比。

*如果我使用固定取樣率，如何知道哪個取樣百分比最適合我的應用程式？*

* 開始使用調適性取樣的其中一個方法，就是找出它選擇的取樣率 (請參閱上一個問題)，然後再切換為使用該取樣率的固定取樣率。 
  
    否則，您就必須猜測。 分析 Application Insights 中您目前的遙測使用量、觀察目前的節流，並估計所收集之遙測的量。 這三項輸入與所選定價層，可對您可能想要減少收集的遙測量提出建議。 不過，使用者數目的增加或遙測量的其他某些變化可能會讓您的評估失效。

*如果我將採樣百分比配置為過低,會發生什麼情況?*

* 採樣百分比過低會導致過度主動採樣,並在應用程式見解嘗試補償數據可視化以減少數據量時降低近似值的準確性。 此外,您的診斷體驗可能會產生負面影響,因為可能會採樣出一些不常失敗或緩慢的請求。

*如果我將採樣百分比配置為過高,會發生什麼情況?*

* 配置過高的採樣百分比(不夠積極)會導致收集的遙測量減少不足。 您可能仍會遇到與節流相關的遙測資料遺失，而使用 Application Insights 的成本由於超額費可能高於您的計劃。

*我可以在何種平台上使用取樣？*

* 如果 SDK 未執行取樣，則擷取取樣會在任何遙測超過特定數量時自動運作。 例如,如果您使用的是舊版本的ASP.NET SDK 或 JAVA SDK,則此配置將起作用。
* 如果使用當前ASP.NET或ASP.NET核心 SDK(託管在 Azure 或您自己的伺服器上),則默認情況下可以獲取自適應採樣,但可以切換到如上所述的固定速率。 使用固定取樣率，瀏覽器 SDK 會自動同步至取樣相關的事件。 
* 如果使用目前 JAVA 代理,則`ApplicationInsights.json`可以配置 (對於 JAva`ApplicationInsights.xml`SDK,配置 )以打開固定利率採樣。 根據預設取樣功能為關閉。 通過固定速率採樣,瀏覽器 SDK 和伺服器會自動同步到示例相關事件。

*我一律想要看見特定罕見的事件。我要如何讓它們通過取樣模組？*

* 實現此目的的最佳方法是編寫一個自定義[遙測初始化器,該分明器](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)在`SamplingPercentage`要保留的遙測項上將 設定為 100,如下所示。 由於初始化程序保證在遙測處理器(包括採樣)之前運行,這可確保所有採樣技術都忽略此項,從任何採樣注意事項。 自定義遙測初始化程式在ASP.NET SDK、ASP.NET核心 SDK、JAVAScript SDK 和 Java SDK 中可用。 例如,可以使用ASP.NET SDK 設定遙測初始化程式:

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

適用於適用於ASP.NET v2.0.0-beta3 和更高版本的應用程式見解 SDK,適用於 Microsoft.應用程式見解.AspNetCore SDK v2.2.0-beta1 及更高版本,默認情況下處於啟用狀態。

固定速率採樣是 SDK 在 ASP.NET 版本中的一項功能,從 2.0.0 和 JAVA SDK 版本 2.0.1 及以後。

在ASP.NET SDK 的 v2.5.0-beta2 和 ASP.NET 酷 SDK 的 v2.2.0-Beta3 之前,採樣決策基於定義「使用者」(即最典型的 Web 應用程式)的應用程式的使用者 ID 哈希值。 對於未定義使用者(如 Web 服務)的應用程式類型,採樣決策基於請求的操作 ID。 ASP.NET和 ASP.NET核心 SDK 的最新版本使用操作 ID 進行採樣決策。

## <a name="next-steps"></a>後續步驟

* [篩選](../../azure-monitor/app/api-filtering-sampling.md) 可以對您的 SDK 所傳送的內容，提供更嚴格的控制。
* 閱讀開發人員網路文章[,使用應用程式見解最佳化遙測](https://msdn.microsoft.com/magazine/mt808502.aspx)。
