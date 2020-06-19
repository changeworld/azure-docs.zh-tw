---
title: 使用 Azure 監視器 (預覽) 監視 Python 應用程式 | Microsoft Docs
description: 提供可將 OpenCensus Python 與 Azure 監視器連接起來的指示
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6b8343d08962d8ce749e1160b0226b68571571f8
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815718"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>設定 Python 應用程式的 Azure 監視器

Azure 監視器可透過與 [OpenCensus](https://opencensus.io) 整合來支援 Python 應用程式的分散式追蹤、計量收集和記錄。 本文會逐步引導您完成設定「適用於 Python 的 OpenCensus」並將監視資料傳送至 Azure 監視器的程序。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- Python 安裝。 本文使用 [Python 3.7.0](https://www.python.org/downloads/)，但較舊的版本只要稍微變更應該也能運作。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>在 Azure 監視器中建立 Application Insights 資源

首先，您必須在 Azure 監視器中建立會產生檢測金鑰 (ikey) 的 Application Insights 資源。 然後，使用 ikey 來設定 OpenCensus SDK，以將遙測資料傳送至 Azure 監視器。

1. 選取 [建立資源] > [開發人員工具] > [Application Insights]。

   ![新增 Application Insights 資源](./media/opencensus-python/0001-create-resource.png)

1. 隨即會出現設定方塊。 使用下表來填寫輸入欄位。

   | 設定        | 值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 全域唯一值 | 用來識別您所監視之應用程式的名稱 |
   | **資源群組**     | myResourceGroup      | 用來裝載 App Insights 資料的新資源群組名稱 |
   | **位置** | 美國東部 | 您附近或接近應用程式裝載位置的地點 |

1. 選取 [建立]。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>使用適用於 Azure 監視器的 OpenCensus Python SDK 進行檢測

安裝 OpenCensus Azure 監視器匯出工具：

```console
python -m pip install opencensus-ext-azure
```

如需套件和整合的完整清單，請參閱 [OpenCensus 套件](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)。

> [!NOTE]
> `python -m pip install opencensus-ext-azure` 命令假設您已設定 Python 安裝的 `PATH` 環境變數。 如果您尚未設定此變數，則必須提供 Python 可執行檔所在位置的完整目錄路徑。 結果會是如下所示的命令：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

SDK 會使用三個 Azure 監視器匯出工具，將不同類型的遙測傳送至 Azure 監視器：追蹤、計量和記錄。 如需這些遙測類型的詳細資訊，請參閱[資料平台概觀](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。 使用下列指示，透過這三個匯出工具傳送這些遙測類型。

## <a name="telemetry-type-mappings"></a>遙測類型對應

以下是 OpenCensus 所提供、與您會在 Azure 監視器中看到的遙測類型對應的匯出工具。

![將遙測類型從 OpenCensus 對應至 Azure 監視器的螢幕擷取畫面](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>追蹤

> [!NOTE]
> OpenCensus 中的 `Trace` 是指[分散式追蹤](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)。 `AzureExporter` 會將 `requests` 和 `dependency` 遙測傳送至 Azure 監視器。

1. 首先，讓我們在本機產生一些追蹤資料。 在 Python IDLE 或您選擇的編輯器中，輸入下列程式碼。

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. 執行程式碼會重複提示您輸入值。 隨著每次輸入，值會列印至殼層，而且 OpenCensus Python 模組會產生 `SpanData` 的對應片段。 OpenCensus 專案會[將追蹤定義為範圍樹狀結構](https://opencensus.io/core-concepts/tracing/)。
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. 雖然輸入值對於示範有幫助，但我們最終還是想要將 `SpanData` 發給 Azure 監視器。 請直接將連接字串傳遞至匯出工具，或者您也可以在環境變數 `APPLICATIONINSIGHTS_CONNECTION_STRING` 中加以指定。 根據下列程式碼範例，修改得自上一個步驟的程式碼：

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. 現在當您執行 Python 指令碼時，系統應該仍會提示您輸入值，但只有該值會列印在殼層中。 建立的 `SpanData` 將會傳送至 Azure 監視器。 您可以在 `dependencies` 底下找到發出的範圍資料。 如需連出要求的詳細資訊，請參閱 OpenCensus Python [相依性](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)。
如需連入要求的詳細資訊，請參閱 OpenCensus Python [要求](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)。

#### <a name="sampling"></a>取樣

如需如何在 OpenCensus 中取樣的相關資訊，請參閱 [OpenCensus 中的取樣](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="trace-correlation"></a>追蹤相互關聯

如需追蹤資料中遙測相互關聯的詳細資訊，請參閱 OpenCensus Python [遙測相互關聯](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)。

#### <a name="modify-telemetry"></a>修改遙測

如需如何先修改所追蹤的遙測再將其傳送至 Azure 監視器的詳細資訊，請參閱 OpenCensus Python [遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

### <a name="metrics"></a>計量

1. 首先，讓我們產生一些本機計量資料。 我們會建立一個簡單的計量，以追蹤使用者按下 Enter 鍵的次數。

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. 執行程式碼會重複提示您按下 Enter 鍵。 隨即會建立計量以追蹤按下 Enter 鍵的次數。 隨著每次輸入，值會遞增，而且計量資訊會顯示在主控台中。 此資訊包括目前的值，以及計量更新當時的時間戳記。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 雖然輸入值對於示範有幫助，但我們最終還是想要將計量資料發給 Azure 監視器。 請直接將連接字串傳遞至匯出工具，或者您也可以在環境變數 `APPLICATIONINSIGHTS_CONNECTION_STRING` 中加以指定。 根據下列程式碼範例，修改得自上一個步驟的程式碼：

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. 匯出工具會以固定間隔將計量資料傳送至 Azure 監視器。 預設值為每隔 15 秒。 我們追蹤的是單一計量，因此此計量資料 (包含其內含的任何值和時間戳記) 會在每個間隔時傳送。 您可以在 `customMetrics` 底下找到該資料。

#### <a name="standard-metrics"></a>標準計量

根據預設，計量匯出工具會將一組標準計量傳送至 Azure 監視器。 若要停用此功能，您可以在計量匯出工具的建構函式中將 `enable_standard_metrics` 旗標設定為 `False`。

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```
以下是目前傳送的標準計量清單：

- 可用的記憶體 (位元組)
- CPU 處理器時間 (百分比)
- 連入要求速率 (每秒)
- 連入要求平均執行時間 (毫秒)
- 連出要求速率 (每秒)
- 處理序 CPU 使用量 (百分比)
- 處理序私用位元組 (位元組)

您應該能夠在 `performanceCounters` 中看到這些計量。 連入要求速率會在 `customMetrics` 底下。 如需詳細資訊，請參閱[效能計數器](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)。

#### <a name="modify-telemetry"></a>修改遙測

如需如何先修改所追蹤的遙測再將其傳送至 Azure 監視器的詳細資訊，請參閱 OpenCensus Python [遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

### <a name="logs"></a>記錄

1. 首先，讓我們產生一些本機記錄資料。

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  程式碼會持續要求輸入值。 系統會針對每個輸入的值發出記錄項目。

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. 雖然輸入值對於示範有幫助，但我們最終還是想要將記錄資料發給 Azure 監視器。 請直接將連接字串傳遞至匯出工具，或者您也可以在環境變數 `APPLICATIONINSIGHTS_CONNECTION_STRING` 中加以指定。 根據下列程式碼範例，修改得自上一個步驟的程式碼：

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. 匯出工具會將記錄資料傳送至 Azure 監視器。 您可以在 `traces` 底下找到該資料。 

    > [!NOTE]
    > 在此內容中，`traces` 與 `Tracing` 不同。 `traces` 是指使用 `AzureLogHandler` 時，您會在 Azure 監視器中看到的遙測類型。 `Tracing` 指的則是 OpenCensus 中的概念，且與[分散式追蹤](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)有關。

5. 若要將記錄訊息格式化，您可以在內建的 Python [記錄 API](https://docs.python.org/3/library/logging.html#formatter-objects) 中使用 `formatters`。

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. 您也可以使用 [custom_dimensions] 欄位，在*額外的*關鍵字引數中將自訂屬性新增至記錄訊息。 在 Azure 監視器的 `customDimensions` 中，這些屬性會以索引鍵/值組的形式顯示。
    > [!NOTE]
    > 若要讓這項功能生效，您必須將字典傳遞至 custom_dimensions 欄位。 如果您傳遞任何其他類型的引數，記錄器會將其忽略。

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="sending-exceptions"></a>傳送例外狀況

OpenCensus Python 不會自動追蹤和傳送 `exception` 遙測。 系統會透過 Python 記錄程式庫，使用例外狀況透過 `AzureLogHandler` 來加以傳送。 您可以新增自訂屬性，方法就和一般記錄一樣。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
因為您必須明確地記錄例外狀況，所以使用者可自己決定要如何記錄未處理的例外狀況。 OpenCensus 不會限制使用者要如何執行此動作，但前提是其必須明確記錄例外狀況遙測。

#### <a name="sampling"></a>取樣

如需如何在 OpenCensus 中取樣的相關資訊，請參閱 [OpenCensus 中的取樣](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="log-correlation"></a>記錄相互關聯

如需如何使用追蹤內容資料來擴充記錄的詳細資訊，請參閱 OpenCensus Python [記錄整合](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)。

#### <a name="modify-telemetry"></a>修改遙測

如需如何先修改所追蹤的遙測再將其傳送至 Azure 監視器的詳細資訊，請參閱 OpenCensus Python [遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

## <a name="view-your-data-with-queries"></a>使用查詢來檢視資料

您可以透過 [記錄 (分析)] 索引標籤來檢視從應用程式傳來的遙測資料。

![[概觀] 窗格的螢幕擷取畫面，紅色方塊中已選取 [記錄 (分析)]](./media/opencensus-python/0010-logs-query.png)

在 [使用中] 底下的清單中：

- 針對使用 Azure 監視器追蹤匯出工具傳送的遙測，連入要求會出現在 `requests` 底下。 連出要求或處理中要求會出現在 `dependencies` 底下。
- 針對使用 Azure 監視器計量匯出工具傳送的遙測，已傳送的計量會出現在 `customMetrics` 底下。
- 針對使用 Azure 監視器記錄匯出工具傳送的遙測，記錄會出現在 `traces` 底下。 例外狀況會出現在 `exceptions` 底下。

如需如何使用查詢和記錄的詳細資訊，請參閱 [Azure 監視器中的記錄](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)。

## <a name="learn-more-about-opencensus-for-python"></a>深入了解適用於 Python 的 OpenCensus

* [GitHub 上的 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [自訂](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub 上的 Azure 監視器匯出工具](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus 整合](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure 監視器應用程式範例](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>後續步驟

* [追蹤連入要求](./../../azure-monitor/app/opencensus-python-dependency.md)
* [追蹤連出要求](./../../azure-monitor/app/opencensus-python-request.md)
* [應用程式對應](./../../azure-monitor/app/app-map.md)
* [端對端效能監視](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>警示

* [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)：建立測試，以確保網路上看得見您的網站。
* [智慧型診斷](../../azure-monitor/app/proactive-diagnostics.md)︰這些測試會自動執行，您不需要採取任何動作來設定它們。 它們會讓您知道應用程式是否有不尋常的失敗要求率。
* [計量警示](../../azure-monitor/platform/alerts-log.md)：設定警示以在計量超出臨界值時警告您。 您可以在撰寫於程式碼中的自訂度量上設定它們。
