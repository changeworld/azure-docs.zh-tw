---
title: 使用 Azure 監視器（預覽）監視 Python 應用程式 |微軟文檔
description: 提供使用 Azure 監視器連接 OpenCensus Python 的說明
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537103"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>為 Python 應用程式設定 Azure 監視器

Azure 監視器通過與[OpenCensus](https://opencensus.io)集成支援 Python 應用程式的分散式跟蹤、指標收集和日誌記錄。 本文將引導您完成為 Python 設置 OpenCensus 並將監視資料發送到 Azure 監視器的過程。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- Python 安裝。 本文使用[Python 3.7.0](https://www.python.org/downloads/)，但早期版本可能會處理細微的更改。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登錄到 Azure[門戶](https://portal.azure.com/)。

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>在 Azure 監視器中創建應用程式見解資源

首先，您必須在 Azure 監視器中創建應用程式見解資源，這將生成檢測金鑰 （ikey）。 然後，ikey 用於配置 OpenCensus SDK 以將遙測資料發送到 Azure 監視器。

1. 選擇 **"創建資源** > **開發人員工具** > **應用程式見解**"。

   ![添加應用程式見解資源](./media/opencensus-python/0001-create-resource.png)

1. 將顯示一個配置框。 使用下表填寫輸入欄位。

   | 設定        | 值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 全球獨特價值 | 用來識別您所監視之應用程式的名稱 |
   | **資源組**     | myResourceGroup      | 承載應用程式見解資料的新資源組的名稱 |
   | **位置** | 美國東部 | 您附近的位置或應用託管位置附近 |

1. 選取 [建立]****。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>具有用於 Azure 監視器的 OpenCensus Python SDK 的儀器

安裝 OpenCensus Azure 監視器匯出器：

```console
python -m pip install opencensus-ext-azure
```

有關包和集成的完整清單，請參閱[OpenCensus 包](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)。

> [!NOTE]
> 該`python -m pip install opencensus-ext-azure`命令假定您為 Python`PATH`安裝設置了環境變數。 如果尚未配置此變數，則需要為 Python 可執行檔所在的位置提供完整的目錄路徑。 結果是這樣的命令： `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

SDK 使用三個 Azure 監視器匯出器向 Azure 監視器發送不同類型的遙測資料：跟蹤、指標和日誌。 有關這些遙測類型的詳細資訊，請參閱[資料平臺概述](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。 使用以下說明通過三個匯出器發送這些遙測類型。

## <a name="telemetry-type-mappings"></a>遙測類型映射

以下是 OpenCensus 提供的匯出器，映射到將在 Azure 監視器中看到的遙測類型。

![從 OpenCensus 到 Azure 監視器的遙測類型映射螢幕截圖](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>追蹤

> [!NOTE]
> `Trace`在 OpenCensus 中是指[分散式跟蹤](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)。 向`AzureExporter` `requests` Azure`dependency`監視器發送和遙測。

1. 首先，讓我們在本地生成一些跟蹤資料。 在 Python IDLE 或您選擇的編輯器中，輸入以下代碼。

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

2. 執行程式碼會重複提示您輸入值。 使用每個條目時，該值將列印到 shell，OpenCensus Python 模組將生成相應的 部分`SpanData`。 OpenCensus 專案會[將追蹤定義為範圍樹狀結構](https://opencensus.io/core-concepts/tracing/)。
    
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

3. 儘管輸入值有助於演示目的，但最終我們希望向 Azure 監視器發出`SpanData`。 將連接字串直接傳遞到匯出器，也可以在環境變數`APPLICATIONINSIGHTS_CONNECTION_STRING`中指定它。 根據以下代碼示例從上一步修改代碼：

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

4. 現在，當您運行 Python 腳本時，仍應提示您輸入值，但僅在 shell 中列印該值。 創建的`SpanData`將發送到 Azure 監視器。 您可以在 下`dependencies`找到發出的跨距資料。 有關傳出請求的更多詳細資訊，請參閱 OpenCensus Python[依賴項](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)。
有關傳入請求的更多詳細資訊，請參閱 OpenCensus Python[請求](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)。

#### <a name="sampling"></a>取樣

有關開放Census中採樣的資訊，請查看[OpenCensus 中的採樣](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="trace-correlation"></a>跟蹤關聯

有關跟蹤資料中的遙測相關性的詳細資訊，請查看 OpenCensus Python[遙測相關性](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)。

#### <a name="modify-telemetry"></a>修改遙測

有關如何在將跟蹤遙測發送到 Azure 監視器之前對其進行修改的詳細資訊，請參閱 OpenCensus Python[遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

### <a name="metrics"></a>計量

1. 首先，讓我們生成一些本地指標資料。 我們將創建一個簡單的指標來跟蹤使用者按 Enter 的次數。

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
2. 運行代碼將反復提示您按 Enter。 創建指標以跟蹤按下 Enter 的次數。 使用每個條目時，該值將遞增，指標資訊將顯示在主控台中。 該資訊包括更新指標時的當前值和目前時間戳。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 儘管輸入值有助於演示目的，但最終我們希望向 Azure 監視器發出指標資料。 將連接字串直接傳遞到匯出器，也可以在環境變數`APPLICATIONINSIGHTS_CONNECTION_STRING`中指定它。 根據以下代碼示例從上一步修改代碼：

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

4. 匯出器將在固定時間間隔內向 Azure 監視器發送指標資料。 預設值為每 15 秒一次。 我們正在跟蹤單個指標，因此此指標資料（包含任何值和時間戳記）將每隔一段時間發送。 您可以在 下`customMetrics`找到資料。

#### <a name="standard-metrics"></a>標準指標

預設情況下，指標匯出器將向 Azure 監視器發送一組標準指標。 您可以通過在`enable_standard_metrics`指標匯出器的建構函式中`False`設置標誌來禁用此功能。

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
以下是當前發送的標準指標清單：

- 可用記憶體（位元組）
- CPU 處理器時間（百分比）
- 傳入請求率（每秒）
- 傳入請求平均執行時間（毫秒）
- 傳出請求率（每秒）
- 進程 CPU 使用率（百分比）
- 處理專用位元組（位元組）

您應該能夠在 中`performanceCounters`看到這些指標。 傳入請求率將低於`customMetrics`。 有關詳細資訊，請參閱[效能計數器](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)。

#### <a name="modify-telemetry"></a>修改遙測

有關如何在將跟蹤遙測發送到 Azure 監視器之前對其進行修改的詳細資訊，請參閱 OpenCensus Python[遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

### <a name="logs"></a>記錄

1. 首先，讓我們生成一些本地日誌資料。

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

2.  代碼將不斷要求輸入值。 為每個輸入的值發出日誌條目。

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

3. 儘管輸入值有助於演示目的，但最終我們希望向 Azure 監視器發出日誌資料。 將連接字串直接傳遞到匯出器，也可以在環境變數`APPLICATIONINSIGHTS_CONNECTION_STRING`中指定它。 根據以下代碼示例從上一步修改代碼：

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

4. 匯出器將向 Azure 監視器發送日誌資料。 您可以在 下`traces`找到資料。 

> [!NOTE]
> `traces`在此上下文中與`Tracing`不同。 `traces`指在使用 時將在 Azure 監視器中看到的遙測類型`AzureLogHandler`。 `Tracing`指 OpenCensus 中的概念，與[分散式跟蹤](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)有關。

5. 要設置日誌消息的格式，可以在內置`formatters`的 Python 日誌記錄[API](https://docs.python.org/3/library/logging.html#formatter-objects)中使用 。

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

6. 您還可以使用custom_dimensions欄位將自訂屬性添加到*額外*關鍵字參數中的日誌消息。 這些將在 Azure 監視器中`customDimensions`顯示為鍵值對。
> [!NOTE]
> 要此功能正常工作，您需要將字典傳遞給custom_dimensions欄位。 如果傳遞任何其他類型的參數，記錄器將忽略它們。

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

#### <a name="sending-exceptions"></a>發送異常

OpenCensus Python 不會自動跟蹤和`exception`發送遙測資料。 它們`AzureLogHandler`通過使用 Python 日誌記錄庫使用異常通過 發送。 您可以像使用普通日誌記錄一樣添加自訂屬性。

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
由於必須顯式記錄異常，因此使用者應如何記錄未處理的異常。 OpenCensus 不會限制使用者想要執行此操作的方式，只要他們顯式記錄異常遙測資料。

#### <a name="sampling"></a>取樣

有關開放Census中採樣的資訊，請查看[OpenCensus 中的採樣](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="log-correlation"></a>記錄相互關聯

有關如何使用跟蹤上下文資料豐富日誌的詳細資訊，請參閱 OpenCensus Python[日誌集成](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)。

#### <a name="modify-telemetry"></a>修改遙測

有關如何在將跟蹤遙測發送到 Azure 監視器之前對其進行修改的詳細資訊，請參閱 OpenCensus Python[遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

## <a name="view-your-data-with-queries"></a>使用查詢查看資料

您可以通過 **"日誌（分析）"** 選項卡查看從應用程式發送的遙測資料。

![在紅色框中選中的"日誌（分析）"概覽窗格的螢幕截圖](./media/opencensus-python/0010-logs-query.png)

在 **"活動"** 下的清單中：

- 對於隨 Azure 監視器跟蹤匯出器一起發送的遙測，`requests`傳入請求將顯示在 下。 傳出或進程內請求顯示在 下`dependencies`。
- 對於隨 Azure 監視器指標匯出器一起發送的遙測，發送的`customMetrics`指標將顯示在 下。
- 對於隨 Azure 監視器日誌匯出器一起發送的遙測`traces`資料，日誌將顯示在 下。 異常顯示在 下`exceptions`。

有關如何使用查詢和日誌的詳細資訊，請參閱[Azure 監視器 中的日誌](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)。

## <a name="learn-more-about-opencensus-for-python"></a>瞭解有關 Python 開放Census 的更多詳細資訊

* [在 GitHub 上打開 Census Python](https://github.com/census-instrumentation/opencensus-python)
* [自訂](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub 上的 Azure 監視器匯出器](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [開放普查集成](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure 監視器應用程式範例](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>後續步驟

* [跟蹤傳入請求](./../../azure-monitor/app/opencensus-python-dependency.md)
* [跟蹤外出請求](./../../azure-monitor/app/opencensus-python-request.md)
* [應用程式對應](./../../azure-monitor/app/app-map.md)
* [端對端效能監視](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>警示

* [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)：建立測試，以確保網路上看得見您的網站。
* [智慧型診斷](../../azure-monitor/app/proactive-diagnostics.md)︰這些測試會自動執行，您不需要採取任何動作來設定它們。 它們會讓您知道應用程式是否有不尋常的失敗要求率。
* [指標警報](../../azure-monitor/app/alerts.md)：設置警報，以便在指標超過閾值時發出警告。 您可以在撰寫於程式碼中的自訂度量上設定它們。
