---
title: Azure 監視器 Application Insights 中的 Get-Metric
description: '瞭解如何有效使用 >getmetric ( # A1 呼叫，以使用 Azure 監視器來捕捉 .NET 和 .NET Core 應用程式的本機預先匯總計量 Application Insights'
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/28/2020
ms.openlocfilehash: 7aacb951d449583c875c71f260957a9d3bc8c663
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86517139"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>.NET 和 .NET Core 中的自訂計量集合

Azure 監視器 Application Insights .NET 和 .NET Core Sdk 有兩種不同的方法來收集自訂計量、 `TrackMetric()` 和 `GetMetric()` 。 這兩種方法之間的主要差異在於本機匯總。 `TrackMetric()` 在預先匯總時缺少預先匯總 `GetMetric()` 。 建議的方法是使用匯總，因此不再是 `TrackMetric()` 收集自訂計量的慣用方法。 本文將逐步引導您使用 >getmetric ( # A1 方法，以及其運作方式背後的一些原理。

## <a name="trackmetric-versus-getmetric"></a>TrackMetric 與 >getmetric

`TrackMetric()` 傳送代表度量的原始遙測。 傳送每個值的單一遙測專案是沒有效率的。 `TrackMetric()` 在效能方面也沒有效率，因為每一次都經過 `TrackMetric(item)` 遙測初始化運算式和處理器的完整 SDK 管線。 不同 `TrackMetric()` `GetMetric()` 于，會為您處理本機預先匯總，然後只會在一分鐘的固定間隔提交匯總的摘要度量。 因此，如果您需要在第二個或甚至毫秒的層級上仔細監視某些自訂計量，您可以這麼做，而只會產生每分鐘只監視的儲存和網路流量成本。 這也可大幅降低發生節流的風險，因為匯總計量所需傳送的遙測專案總數會大幅降低。

在 Application Insights 中，透過和收集的自訂計量 `TrackMetric()` `GetMetric()` 不受 [取樣](./sampling.md)的規範。 對重要計量進行取樣可能會導致案例，其中可能會有以這些計量為基礎的警示，可能會變得不可靠。 藉由永不取樣您的自訂計量，您通常可以確信當違反警示閾值時，就會引發警示。  但由於不會取樣自訂計量，因此有一些潛在的考慮。

如果您需要每秒追蹤計量的趨勢，或以更細微的間隔來追蹤，可能會導致：

- 增加資料儲存體成本。 您傳送給 Azure 監視器的資料量有相關聯的成本。  (的資料越多，就會將整體的監視成本提高 ) 。
- 增加網路流量/效能額外負荷。  (在某些情況下，這可能會產生貨幣和應用程式效能成本。 ) 
- 內嵌節流的風險。  (當您的應用程式在短時間間隔內傳送非常高的遙測速率時，Azure 監視器服務會 ( 「節流」 ) 資料點。 ) 

節流在這種情況下特別重要，例如，節流可能會導致遺漏警示，因為觸發警示的條件可能會在本機發生，然後因為傳送太多資料而在內嵌端點上卸載。 這就是為什麼我們不建議使用 .NET 和 .NET Core， `TrackMetric()` 除非您已執行自己的本機匯總邏輯。 如果您想要追蹤每個實例在指定的時間週期內發生的事件，您可能會發現它 [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) 更適合。 不過請記住，與自訂計量不同的是，自訂事件會受限於取樣。 當然 `TrackMetric()` ，即使沒有撰寫您自己的本機預先匯總，您仍然可以使用，但是如果這樣做，請留意陷阱。

總而言之 `GetMetric()` 是建議的方法，因為它會預先匯總，它會從所有曲目中累積值 ( # A1 呼叫，並每分鐘傳送一次摘要/匯總。 這可以藉由傳送較少的資料點，同時收集所有相關資訊，大幅降低成本和效能負擔。

> [!NOTE]
> 只有 .NET 和 .NET Core Sdk 具有 >getmetric ( # A1 方法。 如果您使用 JAVA，則可以使用 [Micrometer 計量](./micrometer-java.md) 或 `TrackMetric()` 。 針對 Python，您可以使用 [OpenCensus](./opencensus-python.md#metrics) 來傳送自訂計量。 針對 JavaScript 和 Node.js 您仍會使用 `TrackMetric()` ，但請注意上一節中所述的注意事項。

## <a name="getting-started-with-getmetric"></a>開始使用 >getmetric

在我們的範例中，我們將使用基本的 .NET Core 3.1 背景工作服務應用程式。 如果您想要完全複寫與這些範例搭配使用的測試環境，請依照 [監視背景工作服務文章](./worker-service.md#net-core-30-worker-service-application) 中的步驟1-6，將 Application Insights 加入至基本背景工作角色服務專案範本。 這些概念適用于任何可使用 SDK 的一般應用程式，包括 web 應用程式和主控台應用程式。

### <a name="sending-metrics"></a>傳送計量

以下列內容取代您的檔案內容 `worker.cs` ：

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("computersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

如果您執行上述程式碼，並透過 Visual Studio 的輸出視窗或 Telerik 的 Fiddler 之類的工具來監看正在傳送的遙測，您將會看到 while 迴圈重複執行，但不會傳送任何遙測，然後會透過 60-秒的標記來傳送單一遙測專案，如果我們的測試看起來如下:

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"computersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

此單一遙測專案代表41相異計量度量的匯總。 由於我們是以同樣的方式傳送相同的值，因此我們有 *標準差 (stDev) * 0， *最大 (* 最大值) ，以及 *最小 (最 * 小值) 值。 *Value*屬性代表所有匯總之個別值的總和。

如果我們在記錄 (分析) 體驗中檢查 Application Insights 資源，此個別遙測專案看起來會如下所示：

![Log Analytics 查詢檢視](./media/get-metric/log-analytics.png)

> [!NOTE]
> 雖然原始遙測專案在內嵌之後不包含明確的 sum 屬性/欄位，但我們會為您建立一個。 在此情況下 `value` ，和 `valueSum` 屬性都代表相同的東西。

您也可以在入口網站的 [ [_計量_](../platform/metrics-charts.md) ] 區段中存取自訂度量遙測。 同時做為 [記錄型和自訂度量](pre-aggregated-metrics-log-metrics.md)。  (以下的螢幕擷取畫面是以記錄為基礎的範例。 ) ![ 計量瀏覽器視圖](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>高輸送量使用量的快取度量參考

在某些情況下，計量值會非常頻繁地觀察到。 例如，處理每秒500個要求的高輸送量服務，可能會想要針對每個要求發出20個遙測度量。 這表示每秒追蹤10000的值。 在這類高輸送量案例中，使用者可能需要避免某些查閱，以協助 SDK。

例如，在此情況下，上述範例會對度量 "ComputersSold" 的控制碼執行查閱，然後追蹤觀察到的值42。 相反地，可能會快取此控制碼以進行多個追蹤調用：

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

除了快取計量控制碼，上述範例也 `Task.Delay` 會減少到50毫秒，讓迴圈的執行頻率更頻繁，導致 772 `TrackValue()` 調用。

## <a name="multi-dimensional-metrics"></a>多維度計量

上一節中的範例會顯示零維度的度量。 計量也可以是多維度。 目前最多支援10個維度。

 以下是如何建立一維度量的範例：

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

執行此程式碼至少60秒，將會產生三個不同的遙測專案傳送至 Azure，每個專案都代表三個外型規格之一的匯總。 如同之前，您可以在記錄 (分析) 視圖中檢查這些記錄：

![多維度度量的 Log analytics 視圖](./media/get-metric/log-analytics-multi-dimensional.png)

以及計量瀏覽器體驗：

![自訂度量](./media/get-metric/custom-metrics.png)

不過，您會發現您無法以新的自訂維度來分割計量，或使用計量視圖來查看您的自訂維度：

![分割支援](./media/get-metric/splitting-support.png)

依預設，在 Application Insights 資源中，不會開啟計量瀏覽器體驗內的多維度計量。

### <a name="enable-multi-dimensional-metrics"></a>啟用多維度計量

若要啟用 Application Insights 資源的多維度計量，請選取 [**使用量和估計成本**  >  **] 自訂計量**可  >  **在自訂計量維度上啟用警示**  >  ** **。 您可以在 [這裡](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)找到更多有關此的詳細資料。

當您進行變更並傳送新的多維度遙測資料之後，您就能夠套用 **分割**。

> [!NOTE]
> 在入口網站中開啟功能之後，只有新傳送的計量會儲存維度。

![套用分割](./media/get-metric/apply-splitting.png)

並查看每個 _FormFactor_ 維度的度量匯總：

![尺寸規格](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>當有三個以上的維度時，如何使用 MetricIdentifier

目前支援10個維度，但大於三個維度需要使用 `MetricIdentifier` ：

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>自訂度量設定

如果您想要改變計量設定，您需要在計量初始化的位置進行這項作業。

### <a name="special-dimension-names"></a>特殊維度名稱

計量不會使用 `TelemetryClient` 用來存取它們的遙測內容，而類別中的常數可以使用特殊的維度名稱， `MetricDimensionNames` 是此限制的最佳解決方法。

下列「特殊作業要求大小」所傳送的計量匯總，計量 **不** 會將其 `Context.Operation.Name` 設定為「特殊作業」。 但 `TrackMetric()` 或任何其他 TrackXXX ( # A1 會 `OperationName` 正確地設定為「特殊作業」。

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

在這種情況下，請使用類別中列出的特殊維度名稱 `MetricDimensionNames` 來指定 `TelemetryContext` 值。

例如，從下一個語句產生的計量匯總傳送至 Application Insights 雲端端點時，其 `Context.Operation.Name` 資料欄位將會設定為「特殊作業」：

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

這個特殊維度的值會複製到， `TelemetryContext` 而且不會用來做為「一般」維度。 如果您想要同時保留作業維度以進行一般的計量探索，您需要針對該目的建立個別維度：

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>維度和時間序列上限

 若要防止遙測子系統不小心使用您的資源，您可以控制每個計量的資料數列最大數目。 預設限制不超過每個度量的1000總資料數列，而且每個維度不超過100個不同的值。

 在維度和時間序列上限的內容中，我們 `Metric.TrackValue(..)` 會使用來確保觀察到限制。 如果已達到限制， `Metric.TrackValue(..)` 則會傳回 "False"，且不會追蹤值。 否則，它會傳回 "True"。 如果計量的資料來自使用者輸入，這就很有用。

此函式 `MetricConfiguration` 會使用一些選項，以瞭解如何在個別的度量內管理不同的數列，以及針對 `IMetricSeriesConfiguration` 每個個別的度量指定合計列為之類別的物件：

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` 這是度量可包含的資料時間序列數目上限。 一旦達到此限制，就會呼叫 `TrackValue()` 。
* `valuesPerDimensionLimit` 以類似的方式限制每個維度的相異值數目。
* `restrictToUInt32Values` 決定是否應該只追蹤非負整數值。

以下是如何傳送訊息以得知是否超過上限的範例：

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>接下來的步驟

* [深入瞭解 ](./worker-service.md)如何監視背景工作服務應用程式。
* 以取得 [記錄型和預先匯總計量](./pre-aggregated-metrics-log-metrics.md)的進一步詳細資料。
* [計量瀏覽器](../platform/metrics-getting-started.md)
* 如何啟用[ASP.NET Core 應用程式](asp-net-core.md)的 Application Insights
* 如何為[ASP.NET 應用程式](asp-net.md)啟用 Application Insights
