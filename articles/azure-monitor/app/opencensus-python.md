---
title: 使用 Azure 監視器 (預覽) 監視 Python 應用程式 | Microsoft Docs
description: 提供可將 OpenCensus Python 與 Azure 監視器連接起來的指示
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.custom: tracking-python
ms.openlocfilehash: bef2f1c48241a3f0215481aeb0da3fcc237ddb50
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86076619"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>設定 Python 應用程式的 Azure 監視器

Azure 監視器可透過與 [OpenCensus](https://opencensus.io) 整合來支援 Python 應用程式的分散式追蹤、計量收集和記錄。 這篇文章會逐步引導您完成設定 Python 的 OpenCensus，以及將您的監視資料傳送至 Azure 監視器的程式。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- Python 安裝。 本文使用[Python 3.7.0](https://www.python.org/downloads/release/python-370/)，雖然其他版本可能會使用次要變更。 SDK 僅支援 Python 2.7 和 3.4-v 3.7。
- 建立 Application Insights [資源](./create-new-resource.md)。 系統會為您的資源指派自己的檢測金鑰（ikey）。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>使用適用於 Azure 監視器的 OpenCensus Python SDK 進行檢測

安裝 OpenCensus Azure 監視器匯出工具：

```console
python -m pip install opencensus-ext-azure
```

如需套件和整合的完整清單，請參閱 [OpenCensus 套件](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)。

> [!NOTE]
> `python -m pip install opencensus-ext-azure` 命令假設您已設定 Python 安裝的 `PATH` 環境變數。 如果您尚未設定此變數，則必須提供 Python 可執行檔所在位置的完整目錄路徑。 結果會是如下所示的命令：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

SDK 會使用三個 Azure 監視器匯出工具，將不同類型的遙測傳送至 Azure 監視器。 它們是追蹤、計量和記錄。 如需這些遙測類型的詳細資訊，請參閱[資料平台概觀](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。 使用下列指示，透過這三個匯出工具傳送這些遙測類型。

## <a name="telemetry-type-mappings"></a>遙測類型對應

OpenCensus 提供的匯出工具會對應至您在 Azure 監視器中看到的遙測類型。

| 可檢視性的要件 | Azure 監視器中的遙測類型    | 說明                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| 記錄                    | 追蹤、例外狀況、customEvents   | 記錄遙測，例外狀況遙測，事件遙測 |
| 計量                 | customMetrics、performanceCounters | 自訂計量效能計數器                |
| 追蹤                 | 要求相依性             | 傳入要求，連出要求                |

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

1. 程式碼會持續要求輸入值。 系統會針對每個輸入的值發出記錄項目。

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

1. 雖然輸入值對於示範有幫助，但我們最終還是想要將記錄資料發給 Azure 監視器。 直接將連接字串傳遞至匯出工具。 或者，您可以在環境變數中指定它 `APPLICATIONINSIGHTS_CONNECTION_STRING` 。 根據下列程式碼範例，修改得自上一個步驟的程式碼：

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

1. 匯出程式會將記錄資料傳送至 Azure 監視器。 您可以在 `traces` 底下找到該資料。 

    > [!NOTE]
    > 在此內容中，與 `traces` 不相同 `tracing` 。 在這裡， `traces` 指的是當您使用時，您會在 Azure 監視器中看到的遙測類型 `AzureLogHandler` 。 但 `tracing` 指的是 OpenCensus 中的概念，並與[分散式追蹤](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)相關。

    > [!NOTE]
    > 根記錄器已設定警告層級。 這表示系統會忽略您所傳送且嚴重性較低的任何記錄，而不會傳送到 Azure 監視器。 如需詳細資訊，請參閱[文件](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel)。

1. 您也可以使用 [custom_dimensions] 欄位，在*額外*的關鍵字引數中，將自訂屬性新增至記錄訊息。 在 Azure 監視器中，這些屬性會以索引鍵/值組的形式顯示 `customDimensions` 。
    > [!NOTE]
    > 若要讓這項功能生效，您必須將字典傳遞至 custom_dimensions 欄位。 如果您傳遞任何其他類型的引數，記錄器會忽略它們。

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

#### <a name="configure-logging-for-django-applications"></a>設定 Django 應用程式的記錄

您可以針對 Django 應用程式，在上述的應用程式代碼中明確設定記錄，也可以在 Django 的記錄設定中加以指定。 這段程式碼可以進入您用來 Django 設定的任何檔案。 如需如何設定 Django 設定的詳細說明，請參閱[Django 設定](https://docs.djangoproject.com/en/3.0/topics/settings/)。 如需設定記錄的詳細資訊，請參閱[Django 記錄](https://docs.djangoproject.com/en/3.0/topics/logging/)。

```python
 LOGGING = {
     "handlers": {
         "azure": {
             "level": "DEBUG",
          "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
             "instrumentation_key": "<your-ikey-here>",
          },
         "console": {
             "level": "DEBUG",
             "class": "logging.StreamHandler",
             "stream": sys.stdout,
          },
       },
     "loggers": {
         "logger_name": {"handlers": ["azure", "console"]},
     },
 }
```

請確定您使用的記錄器與設定中指定的名稱相同。

```python
 import logging
        
 logger = logging.getLogger("logger_name")
 logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>傳送例外狀況

OpenCensus Python 不會自動追蹤和傳送 `exception` 遙測。 它們是透過 `AzureLogHandler` Python 記錄程式庫中的例外狀況來傳送。 您可以新增自訂屬性，方法就和一般記錄一樣。

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
因為您必須明確地記錄例外狀況，所以由使用者決定要如何記錄未處理的例外狀況。 OpenCensus 不會限制使用者要如何執行此動作，只要它們明確地記錄例外狀況遙測即可。

#### <a name="send-events"></a>傳送事件

您可以用與 `customEvent` 傳送遙測完全相同的方式來傳送遙測， `trace` 但改為使用 `AzureEventHandler` 。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>取樣

如需如何在 OpenCensus 中取樣的相關資訊，請參閱 [OpenCensus 中的取樣](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="log-correlation"></a>記錄相互關聯

如需如何使用追蹤內容資料來擴充記錄的詳細資訊，請參閱 OpenCensus Python [記錄整合](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)。

#### <a name="modify-telemetry"></a>修改遙測

如需如何在將已追蹤的遙測傳送至 Azure 監視器之前加以修改的詳細資訊，請參閱 OpenCensus Python[遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。


### <a name="metrics"></a>計量

1. 首先，讓我們產生一些本機計量資料。 我們會建立一個簡單的度量，以追蹤使用者選取**Enter**鍵的次數。

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
1. 重複執行程式碼，會提示您選取**Enter 鍵**。 建立計量以追蹤已選取**Enter**的次數。 在每個專案中，值會遞增，而計量資訊會顯示在主控台中。 此資訊包括目前的值，以及計量更新當時的時間戳記。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. 雖然輸入值對於示範有幫助，但我們最終還是想要將計量資料發給 Azure 監視器。 直接將連接字串傳遞至匯出工具。 或者，您可以在環境變數中指定它 `APPLICATIONINSIGHTS_CONNECTION_STRING` 。 根據下列程式碼範例，修改得自上一個步驟的程式碼：

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

1. 匯出程式會以固定間隔將計量資料傳送至 Azure 監視器。 預設值為每隔 15 秒。 我們會追蹤單一計量，因此每個間隔都會傳送此計量資料，其中包含其內含的任何值和時間戳記。 您可以在 `customMetrics` 底下找到該資料。

#### <a name="performance-counters"></a>效能計數器

根據預設，計量匯出工具會將一組效能計數器傳送至 Azure 監視器。 若要停用此功能，您可以在計量匯出工具的建構函式中將 `enable_standard_metrics` 旗標設定為 `False`。

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

這些效能計數器目前已傳送：

- 可用的記憶體 (位元組)
- CPU 處理器時間 (百分比)
- 連入要求速率 (每秒)
- 連入要求平均執行時間 (毫秒)
- 處理序 CPU 使用量 (百分比)
- 處理序私用位元組 (位元組)

您應該能夠在 `performanceCounters` 中看到這些計量。 如需詳細資訊，請參閱[效能計數器](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)。

#### <a name="modify-telemetry"></a>修改遙測

如需如何在將已追蹤的遙測傳送至 Azure 監視器之前進行修改的相關資訊，請參閱 OpenCensus Python[遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

### <a name="tracing"></a>追蹤

> [!NOTE]
> 在 OpenCensus 中， `tracing` 是指[分散式追蹤](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)。 `AzureExporter` 會將 `requests` 和 `dependency` 遙測傳送至 Azure 監視器。

1. 首先，讓我們在本機產生一些追蹤資料。 在 Python IDLE 或您選擇的編輯器中，輸入下列程式碼：

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

1. 重複執行程式碼會提示您輸入值。 在每個專案中，值都會列印到 shell。 OpenCensus Python 模組會產生對應的部分 `SpanData` 。 OpenCensus 專案會[將追蹤定義為範圍樹狀結構](https://opencensus.io/core-concepts/tracing/)。
    
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

1. 雖然輸入值有助於示範用途，但最後我們會想要發出 `SpanData` 至 Azure 監視器。 直接將連接字串傳遞至匯出工具。 或者，您可以在環境變數中指定它 `APPLICATIONINSIGHTS_CONNECTION_STRING` 。 根據下列程式碼範例，修改得自上一個步驟的程式碼：

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

1. 現在當您執行 Python 指令碼時，系統應該仍會提示您輸入值，但只有該值會列印在殼層中。 建立的 `SpanData` 會傳送至 Azure 監視器。 您可以在 `dependencies` 底下找到發出的範圍資料。 如需連出要求的詳細資訊，請參閱[OpenCensus Python 相依](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)性。
如需連入要求的詳細資訊，請參閱 OpenCensus Python [requests](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)。

#### <a name="sampling"></a>取樣

如需如何在 OpenCensus 中取樣的相關資訊，請參閱 [OpenCensus 中的取樣](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="trace-correlation"></a>追蹤相互關聯

如需追蹤資料中遙測相互關聯的詳細資訊，請參閱 OpenCensus Python[遙測相互關聯](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)。

#### <a name="modify-telemetry"></a>修改遙測

如需如何在將已追蹤的遙測傳送至 Azure 監視器之前加以修改的詳細資訊，請參閱 OpenCensus Python[遙測處理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

## <a name="configure-azure-monitor-exporters"></a>設定 Azure 監視器匯出工具

如圖所示，有三個不同的 Azure 監視器匯出工具支援 OpenCensus。 每一個會將不同類型的遙測傳送至 Azure 監視器。 若要查看每個匯出程式所傳送的遙測類型，請參閱下列清單。

每個匯出工具都會接受相同的引數來進行設定，並透過函式傳遞。 您可以在這裡查看每個相關的詳細資料：

- `connection_string`：用來連接到您 Azure 監視器資源的連接字串。 優先于 `instrumentation_key` 。
- `enable_standard_metrics`：用於 `AzureMetricsExporter` 。 指示匯出程式自動傳送[效能計數器](https://docs.microsoft.com/azure/azure-monitor/platform/app-insights-metrics#performance-counters)計量給 Azure 監視器。 預設為 `True`。
- `export_interval`：用來指定匯出的頻率（以秒為單位）。
- `instrumentation_key`：用來連接到您 Azure 監視器資源的檢測金鑰。
- `logging_sampling_rate`：用於 `AzureLogHandler` 。 提供用於匯出記錄的取樣率 [0，1.0]。 預設為1.0。
- `max_batch_size`：指定一次匯出的遙測大小上限。
- `proxies`：指定要用來將資料傳送至 Azure 監視器的一系列 proxy。 如需詳細資訊，[請參閱 proxy](https://requests.readthedocs.io/en/master/user/advanced/#proxies)。
- `storage_path`：本機儲存體資料夾所在的路徑（未傳送的遙測）。 `opencensus-ext-azure`從 v 1.0.3，預設路徑是 OS 臨時目錄 + `opencensus-python`  +  `your-ikey` 。 在 v 1.0.3 之前，預設路徑是 $USER + `.opencensus`  +  `.azure`  +  `python-file-name` 。

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
* [追蹤不進入的要求](./../../azure-monitor/app/opencensus-python-request.md)
* [應用程式對應](./../../azure-monitor/app/app-map.md)
* [端對端效能監視](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>警示

* [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)：建立測試，以確保網路上看得見您的網站。
* [智慧型診斷](../../azure-monitor/app/proactive-diagnostics.md)︰這些測試會自動執行，您不需要採取任何動作來設定它們。 它們會讓您知道應用程式是否有不尋常的失敗要求率。
* [計量警示](../../azure-monitor/platform/alerts-log.md)：設定警示以在計量超出臨界值時警告您。 您可以在撰寫於程式碼中的自訂度量上設定它們。
