---
title: Azure 監視器 Application Insights 中的取得標準
description: 瞭解如何有效率地使用 GetMetric （）呼叫，以使用 Azure 監視器來捕獲適用于 .NET 和 .NET Core 應用程式的本機預先匯總計量 Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/28/2020
ms.openlocfilehash: 309e467f5831961b6bc5a94ad2ce05fd3b991794
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629264"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>.NET 和 .NET Core 中的自訂計量集合

.NET 和 .NET Core Sdk Application Insights 的 Azure 監視器有兩種不同的方法來收集自`TrackMetric()`定義計量`GetMetric()`：和。 這兩種方法之間的主要差異在於本機匯總。 `TrackMetric()`在預先匯總時`GetMetric()`缺少預先匯總。 建議的方法是使用匯總，因此不再`TrackMetric()`是收集自訂計量的慣用方法。 本文將逐步引導您使用 GetMetric （）方法，以及其運作方式的一些基本概念。

## <a name="trackmetric-versus-getmetric"></a>TrackMetric 與 GetMetric

`TrackMetric()`傳送代表度量的原始遙測。 傳送每個值的單一遙測專案是沒有效率的。 `TrackMetric()`傳送代表度量的原始遙測。 傳送每個值的單一遙測專案是沒有效率的。 `TrackMetric()`在效能方面也沒有效率，因為每`TrackMetric(item)`個都經過遙測初始化運算式和處理器的完整 SDK 管線。 不同`TrackMetric()`于`GetMetric()` ，會為您處理本機預先匯總，然後只以一分鐘的固定間隔提交匯總的摘要度量。 因此，如果您需要在第二個或甚至毫秒層級密切監視某個自訂計量，您可以這麼做，同時只會產生每分鐘監視的儲存體和網路流量成本。 這也能大幅降低發生節流的風險，因為需要為匯總計量傳送的遙測專案總數大幅降低。

在 Application Insights 中，透過`TrackMetric()`和`GetMetric()`收集的自訂計量不受[取樣](https://docs.microsoft.com/azure/azure-monitor/app/sampling)的規範。 取樣重要計量可能會導致可能以這些計量為基礎建立警示的案例可能會變得不可靠。 藉由永不取樣您的自訂計量，您通常可以確信當違反警示閾值時，將會引發警示。  但是因為自訂計量並未取樣，所以有一些潛在的顧慮。

如果您需要每秒追蹤計量中的趨勢，或更細微的間隔時間，這可能會導致：

- 增加資料儲存成本。 您傳送給 Azure 監視器的資料量有相關聯的成本。 （您傳送的資料愈多，整體的監視成本就愈高）。
- 增加網路流量/效能額外負荷。 （在某些情況下，這可能會有貨幣和應用程式的效能成本）。
- 內嵌節流的風險。 （當您的應用程式在短時間內傳送非常高的遙測率時，Azure 監視器服務會卸載（「節流」）資料點）。

節流是特別考慮，如同取樣，節流可能會導致遺漏警示，因為觸發警示的條件可能會在本機發生，然後在內嵌端點上進行，因為傳送的資料太多。 這就是為什麼不建議使用`TrackMetric()` .NET 和 .net Core，除非您已經實作為自己的本機匯總邏輯。 如果您想要追蹤每個實例在指定的時間週期內發生事件，您可能會發現[`TrackEvent()`](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics#trackevent)這是較佳的調整。 但請記住，與自訂計量不同的是，自訂事件會受到取樣。 即使沒有撰寫自己的本機`TrackMetric()`預先匯總，您仍然可以使用，但如果您這樣做，請注意這些陷阱。

在 [ `GetMetric()`摘要] 中是建議的方法，因為它會進行預先匯總，它會從所有追蹤（）呼叫累積值，並每隔一分鐘傳送一次摘要/匯總。 藉由傳送較少的資料點，同時仍然收集所有相關資訊，這可以大幅降低成本和效能負擔。

> [!NOTE]
> 只有 .NET 和 .NET Core Sdk 具有 GetMetric （）方法。 如果您使用 JAVA，您可以使用[Micrometer 計量](https://docs.microsoft.com/azure/azure-monitor/app/micrometer-java)或`TrackMetric()`。 針對 Python，您可以使用[OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#metrics)來傳送自訂計量。 針對 JavaScript 和 node.js，您仍然可以使用`TrackMetric()`，但請記住上一節中所述的注意事項。

## <a name="getting-started-with-getmetric"></a>開始使用 GetMetric

在我們的範例中，我們將使用基本的 .NET Core 3.1 背景工作角色服務應用程式。 如果您想要完全複寫與這些範例搭配使用的測試環境，請遵循[監視背景工作服務文章](https://docs.microsoft.com/azure/azure-monitor/app/worker-service#net-core-30-worker-service-application)的步驟1-6，將 Application Insights 新增至基本背景工作服務專案範本。 這些概念適用于任何可使用 SDK 的一般應用程式，包括 web 應用程式和主控台應用程式。

### <a name="sending-metrics"></a>傳送計量

將檔案的內容取代`worker.cs`為下列內容：

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

如果您執行上述程式碼，並透過 Visual Studio 的輸出視窗或 Telerik 的 Fiddler 之類的工具來監看傳送的遙測，您會看到 while 迴圈在未傳送遙測的情況下重複執行，然後再以 60-秒標記傳送單一遙測專案，在測試的情況下會顯示如下:

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

這個單一遙測專案代表41相異計量測量的匯總。 因為我們會再次傳送相同的值，所以我們的*標準差（stDev）* 為0，具有相同的*最大值（max）* 和*最小值（分鐘）* 。 *Value*屬性代表所有已匯總之個別值的總和。

如果我們檢查記錄（分析）體驗中的 Application Insights 資源，則此個別的遙測專案看起來會像這樣：

![Log Analytics 查詢檢視](./media/get-metric/log-analytics.png)

> [!NOTE]
> 雖然原始遙測專案在內嵌之後不會包含明確的 sum 屬性/欄位，但我們會為您建立一個。 在此情況下`value` ，和`valueSum`屬性都代表相同的東西。

您也可以在入口網站的 [[_計量_](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)] 區段中，存取您的自訂度量遙測。 同時做為[記錄式和自訂度量](pre-aggregated-metrics-log-metrics.md)。 （下列螢幕擷取畫面是以記錄為基礎的範例）。![計量瀏覽器視圖](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>高輸送量使用量的快取度量參考

在某些情況下，系統會非常頻繁地觀察到度量值。 例如，處理每秒500要求的高輸送量服務，可能會想要針對每個要求發出20個遙測計量。 這表示每秒追蹤10000值。 在這類高輸送量案例中，使用者可能需要避免一些查閱，以協助 SDK。

例如，在此情況下，上述範例會執行度量 "ComputersSold" 的控制碼查閱，然後追蹤觀察到的值42。 相反地，可以針對多個追蹤調用快取控制碼：

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

除了快取計量控制碼，上述範例也會減少`Task.Delay`到50毫秒，讓迴圈的執行頻率較高，而導致 772 `TrackValue()`調用。

## <a name="multi-dimensional-metrics"></a>多維度計量

上一節中的範例會顯示零維度計量。 計量也可以是多維度的。 我們目前最多支援10個維度。

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

執行此程式碼至少60秒，會導致三個不同的遙測專案傳送至 Azure，每個都代表三種外型規格之一的匯總。 您可以在 [記錄（分析）] 視圖中進行檢查：

![多維度度量的 Log analytics 視圖](./media/get-metric/log-analytics-multi-dimensional.png)

以及計量瀏覽器體驗：

![自訂度量](./media/get-metric/custom-metrics.png)

不過，您會注意到您無法以新的自訂維度分割計量，或使用 [計量] 視圖來查看您的自訂維度：

![分割支援](./media/get-metric/splitting-support.png)

根據預設，計量瀏覽器體驗內的多維度度量不會在 Application Insights 資源中開啟。 若要開啟此行為，請選取 [[啟用自訂計量維度的警示](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)]，以移至 [使用量和估計成本] 索引標籤。

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>當有三個以上的維度時，如何使用 MetricIdentifier

目前支援10個維度，但大於三個維度需要使用`MetricIdentifier`：

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

### <a name="enable-multi-dimensional-metrics"></a>啟用多維度計量

若要啟用 Application Insights 資源的多維度計量，請選取 [**使用量和估計成本** > ] [**自訂計量** > **] [****在自訂度量維度** > 上啟用警示]。

當您變更並傳送新的多維度遙測之後，您就能夠套用**分割**。

> [!NOTE]
> 只有在入口網站中開啟功能之後，新傳送的計量才會儲存維度。

![套用分割](./media/get-metric/apply-splitting.png)

並針對每個_FormFactor_維度來查看您的度量匯總：

![尺寸規格](./media/get-metric/formfactor.png)

## <a name="custom-metric-configuration"></a>自訂度量設定

如果您想要改變計量設定，您需要在計量的初始化位置中執行此動作。

### <a name="special-dimension-names"></a>特殊維度名稱

計量不會使用`TelemetryClient`用來存取它們的遙測內容，在類別中可作為常數的特殊維度`MetricDimensionNames`名稱是這項限制的最佳解決方法。

下列「特殊作業要求大小」所傳送的計量匯總-計量**不**會將其`Context.Operation.Name`設定為「特殊作業」。 而`TrackMetric()`或任何其他 TrackXXX （）會正確`OperationName`地設定為「特殊作業」。

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

在此情況下，請使用`MetricDimensionNames`類別中所列的特殊維度名稱來指定`TelemetryContext`值。

例如，當下一個語句所產生的計量匯總傳送至 Application Insights 雲端端點時，其`Context.Operation.Name`資料欄位將會設定為「特殊作業」：

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

這個特殊維度的值會複製到中`TelemetryContext` ，而且不會當做「一般」維度使用。 如果您也想要保留作業維度來進行一般計量探索，您必須針對該目的建立個別的維度：

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>維度和時間序列上限

 若要防止遙測子系統不小心使用您的資源，您可以控制每個計量的資料數列數目上限。 預設限制為每個度量的資料數列總計不超過1000，且每個維度不超過100個不同的值。

 在維度和時間序列上限的內容中，我們`Metric.TrackValue(..)`使用來確保觀察到的限制。 如果已達到限制， `Metric.TrackValue(..)`將會傳回 "False"，且不會追蹤值。 否則會傳回 "True"。 如果計量的資料來源自使用者輸入，這就很有用。

此`MetricConfiguration`函式會使用一些選項，以瞭解如何管理個別計量內的不同數列，以及可指定`IMetricSeriesConfiguration`每個個別度量數列之合計列為的類別的物件：

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

* `seriesCountLimit`這是計量可以包含的最大資料時間序列數目。 一旦達到此限制，會呼叫`TrackValue()`。
* `valuesPerDimensionLimit`以類似的方式限制每個維度的相異值數目。
* `restrictToUInt32Values`判斷是否應該只追蹤非負整數值。

以下範例示範如何傳送訊息，以瞭解是否超過 cap 限制：

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>後續步驟

* [深入瞭解](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)監視背景工作服務應用程式。
* 如需[記錄式和預先匯總計量](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics)的進一步詳細資料。
* [計量瀏覽器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)
* 如何啟用[ASP.NET Core 應用程式](asp-net-core.md)的 Application Insights
* 如何啟用[ASP.NET 應用程式](asp-net.md)的 Application Insights
