---
title: 使用 Azure 監視器（預覽）監視 Python 應用程式 |Microsoft Docs
description: 提供將 OpenCensus Python 與 Azure 監視器連線的指示
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537103"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>設定 Python 應用程式的 Azure 監視器

Azure 監視器透過與[OpenCensus](https://opencensus.io)的整合，支援 Python 應用程式的分散式追蹤、度量集合和記錄。 本文將逐步引導您完成設定 Python 的 OpenCensus，以及將您的監視資料傳送至 Azure 監視器的程式。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- Python 安裝。 本文使用[Python 3.7.0](https://www.python.org/downloads/)，但較舊的版本可能會處理次要變更。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>在 Azure 監視器中建立 Application Insights 資源

首先，您必須在 Azure 監視器中建立 Application Insights 資源，這會產生檢測金鑰（ikey）。 然後，ikey 會用來設定 OpenCensus SDK，以將遙測資料傳送至 Azure 監視器。

1. 選取 [**建立資源** > ] [**開發人員工具** > ] [**Application Insights**]。

   ![新增 Application Insights 資源](./media/opencensus-python/0001-create-resource.png)

1. [設定] 方塊隨即出現。 使用下表來填寫輸入欄位。

   | 設定        | 值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 全域唯一值 | 用來識別您所監視之應用程式的名稱 |
   | **資源群組**     | myResourceGroup      | 要裝載 Application Insights 資料之新資源群組的名稱 |
   | **位置** | 美國東部 | 您附近或接近應用程式裝載位置附近的位置 |

1. 選取 [建立]  。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>使用適用于 Azure 監視器的 OpenCensus Python SDK 進行檢測

安裝 OpenCensus Azure 監視器匯出工具：

```console
python -m pip install opencensus-ext-azure
```

如需封裝和整合的完整清單，請參閱[OpenCensus 套件](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)。

> [!NOTE]
> 此`python -m pip install opencensus-ext-azure`命令假設您已針對 Python `PATH`安裝設定環境變數。 如果您尚未設定此變數，您必須提供 Python 可執行檔所在位置的完整目錄路徑。 結果會是如下所示的命令`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`：。

SDK 會使用三個 Azure 監視器匯出工具，將不同類型的遙測傳送至 Azure 監視器：追蹤、計量和記錄。 如需這些遙測類型的詳細資訊，請參閱[資料平臺總覽](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。 使用下列指示，透過這三個匯出工具傳送這些遙測類型。

## <a name="telemetry-type-mappings"></a>遙測類型對應

OpenCensus 提供的匯出工具會對應至您將在 Azure 監視器中看到的遙測類型。

![將遙測類型從 OpenCensus 對應至 Azure 監視器的螢幕擷取畫面](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>追蹤

> [!NOTE]
> `Trace`在 OpenCensus 中，是指[分散式追蹤](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)。 `AzureExporter`將`requests`和`dependency`遙測傳送至 Azure 監視器。

1. 首先，讓我們在本機產生一些追蹤資料。 在 [Python 閒置] 或您選擇的編輯器中，輸入下列程式碼。

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

2. 執行程式碼會重複提示您輸入值。 在每個專案中，值都會列印到 shell，而 OpenCensus Python 模組會產生對應的`SpanData`部分。 OpenCensus 專案會[將追蹤定義為範圍樹狀結構](https://opencensus.io/core-concepts/tracing/)。
    
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

3. 雖然輸入值有助於示範用途，但最後我們會想要發出`SpanData`來 Azure 監視器。 直接將連接字串傳遞至匯出工具，或在環境變數`APPLICATIONINSIGHTS_CONNECTION_STRING`中指定。 根據下列程式碼範例，修改上一個步驟中的程式碼：

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

4. 現在，當您執行 Python 腳本時，仍然應該提示您輸入值，但只會在 shell 中列印該值。 建立`SpanData`的將會傳送至 Azure 監視器。 您可以在`dependencies`底下找到發出的 span 資料。 如需連出要求的詳細資訊，請參閱[OpenCensus Python 相依](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)性。
如需連入要求的詳細資訊，請參閱 OpenCensus Python [requests](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)。

#### <a name="sampling"></a>取樣

如需在 OpenCensus 中取樣的詳細資訊，請參閱[OpenCensus 中的取樣](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="trace-correlation"></a>追蹤相互關聯

如需追蹤資料中遙測相互關聯的詳細資訊，請參閱 OpenCensus Python[遙測相互關聯](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)。

#### <a name="modify-telemetry"></a>修改遙測

如需如何在將已追蹤的遙測傳送至 Azure 監視器之前加以修改的詳細資訊，請參閱 OpenCensus Python[遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

### <a name="metrics"></a>計量

1. 首先，讓我們來產生一些本機計量資料。 我們將建立一個簡單的計量，以追蹤使用者按下 Enter 的次數。

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
2. 執行程式碼會重複提示您按下 Enter 鍵。 建立度量以追蹤按下 Enter 鍵的次數。 在每個專案中，值會遞增，而計量資訊會顯示在主控台中。 此資訊包括目前的值，以及計量更新時的目前時間戳記。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 雖然輸入值有助於示範用途，但最終我們會想要發出計量資料以 Azure 監視器。 直接將連接字串傳遞至匯出工具，或在環境變數`APPLICATIONINSIGHTS_CONNECTION_STRING`中指定。 根據下列程式碼範例，修改上一個步驟中的程式碼：

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

4. 匯出程式會以固定間隔將計量資料傳送至 Azure 監視器。 預設值為每15秒。 我們會追蹤單一計量，因此此計量資料（包含其內含的任何值和時間戳記）會每隔一段時間傳送。 您可以在`customMetrics`底下找到資料。

#### <a name="standard-metrics"></a>標準計量

根據預設，計量匯出工具會將一組標準計量傳送至 Azure 監視器。 您可以停用此功能， `enable_standard_metrics`方法是`False`在計量匯出工具的函式中將旗標設定為。

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
以下是目前已傳送的標準計量清單：

- 可用的記憶體（位元組）
- CPU 處理器時間（百分比）
- 傳入要求速率（每秒）
- 連入要求平均執行時間（毫秒）
- 傳出要求速率（每秒）
- 進程 CPU 使用量（百分比）
- 進程私用位元組（位元組）

您應該能夠在中`performanceCounters`看到這些計量。 傳入要求速率會在下`customMetrics`。 如需詳細資訊，請參閱[效能計數器](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)。

#### <a name="modify-telemetry"></a>修改遙測

如需如何在將已追蹤的遙測傳送至 Azure 監視器之前加以修改的詳細資訊，請參閱 OpenCensus Python[遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

### <a name="logs"></a>記錄

1. 首先，讓我們來產生一些本機記錄資料。

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

2.  程式碼會持續要求輸入的值。 會針對每個輸入的值發出記錄專案。

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

3. 雖然輸入值有助於示範用途，但最後我們會想要發出記錄資料以 Azure 監視器。 直接將連接字串傳遞至匯出工具，或在環境變數`APPLICATIONINSIGHTS_CONNECTION_STRING`中指定。 根據下列程式碼範例，修改上一個步驟中的程式碼：

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

4. 匯出程式會將記錄資料傳送至 Azure 監視器。 您可以在`traces`底下找到資料。 

> [!NOTE]
> `traces`在此內容中，與不相同`Tracing`。 `traces`指的是在使用`AzureLogHandler`時，您會在 Azure 監視器中看到的遙測類型。 `Tracing`指的是 OpenCensus 中的概念，並與[分散式追蹤](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)相關。

5. 若要格式化記錄訊息，您可以在`formatters`內建的 PYTHON[記錄 API](https://docs.python.org/3/library/logging.html#formatter-objects)中使用。

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

6. 您也可以使用 [custom_dimensions] 欄位，將自訂屬性新增至您的記錄訊息中的*額外*關鍵字引數。 在 Azure 監視器`customDimensions`中，這些會以索引鍵/值組的形式出現。
> [!NOTE]
> 若要讓這項功能正常執行，您必須將字典傳遞至 custom_dimensions 欄位。 如果您傳遞任何其他類型的引數，記錄器將會忽略它們。

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

OpenCensus Python 不會自動追蹤和傳送`exception`遙測。 它們會透過 Python 記錄`AzureLogHandler`程式庫來使用例外狀況來傳送。 您可以新增自訂屬性，就像一般記錄一樣。

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
因為您必須明確地記錄例外狀況，所以由使用者決定要如何記錄未處理的例外狀況。 OpenCensus 不會限制使用者想要執行此動作的方式，只要它們明確地記錄例外狀況遙測即可。

#### <a name="sampling"></a>取樣

如需在 OpenCensus 中取樣的詳細資訊，請參閱[OpenCensus 中的取樣](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="log-correlation"></a>記錄相互關聯

如需如何使用追蹤內容資料來擴充記錄檔的詳細資訊，請參閱 OpenCensus Python[記錄整合](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)。

#### <a name="modify-telemetry"></a>修改遙測

如需如何在將已追蹤的遙測傳送至 Azure 監視器之前加以修改的詳細資訊，請參閱 OpenCensus Python[遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

## <a name="view-your-data-with-queries"></a>使用查詢來查看您的資料

您可以透過 [**記錄（分析）** ] 索引標籤來查看您的應用程式所傳送的遙測資料。

![[總覽] 窗格的螢幕擷取畫面，其中選取了紅色方塊中的 [記錄（分析）]](./media/opencensus-python/0010-logs-query.png)

在 [作用中] 底下的清單**中：**

- 對於使用 Azure 監視器追蹤匯出工具傳送的遙測，傳入要求會`requests`出現在底下。 外寄或同進程要求會出現`dependencies`在底下。
- 針對與 Azure 監視器計量匯出工具一起傳送的遙測，傳送的`customMetrics`度量會顯示在底下。
- 針對使用 Azure 監視器記錄匯出工具傳送的遙測，記錄會`traces`出現在底下。 例外狀況會`exceptions`出現在底下。

如需如何使用查詢和記錄的詳細資訊，請參閱[Azure 監視器中的記錄](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)。

## <a name="learn-more-about-opencensus-for-python"></a>深入瞭解適用于 Python 的 OpenCensus

* [在 GitHub 上 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [自訂](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub 上的 Azure 監視器匯出工具](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus 整合](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure 監視器範例應用程式](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>後續步驟

* [追蹤連入要求](./../../azure-monitor/app/opencensus-python-dependency.md)
* [追蹤不進入的要求](./../../azure-monitor/app/opencensus-python-request.md)
* [應用程式對應](./../../azure-monitor/app/app-map.md)
* [端對端效能監視](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>警示

* [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)：建立測試，以確保網路上看得見您的網站。
* [智慧型診斷](../../azure-monitor/app/proactive-diagnostics.md)︰這些測試會自動執行，您不需要採取任何動作來設定它們。 它們會讓您知道應用程式是否有不尋常的失敗要求率。
* 計量[警示](../../azure-monitor/app/alerts.md)：設定警示，在計量超出臨界值時警告您。 您可以在撰寫於程式碼中的自訂度量上設定它們。
