---
title: 使用 OpenCensus Python Azure 應用程式深入解析中的相依性追蹤 |Microsoft Docs
description: 透過 OpenCensus Python 監視 Python 應用程式的相依性呼叫。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: tracking-python
ms.openlocfilehash: e99ea1d5a90455a4c470346fbbe5d1095c426e81
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499455"
---
# <a name="track-dependencies-with-opencensus-python"></a>使用 OpenCensus Python 追蹤相依性

相依性是由應用程式呼叫的外部元件。 使用 OpenCensus Python 和其各種整合來收集相依性資料。 然後，資料會傳送至 Azure 監視器的 Application Insights，作為 `dependencies` 遙測。

首先，使用最新的[OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)檢測您的 python 應用程式。

## <a name="in-process-dependencies"></a>同進程相依性

OpenCensus 適用于 Azure 監視器的 Python SDK 可讓您傳送「同進程」相依性遙測（應用程式內所發生的資訊和邏輯）。 同進程相依性的欄位會 `type` 和 `INPROC` 分析相同。

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>與「要求」整合的相依性

使用 OpenCensus 整合來追蹤您的連出要求 `requests` 。

從 PyPI 下載並安裝， `opencensus-ext-requests` 並將它新增至追蹤整合。 [PyPI](https://pypi.org/project/opencensus-ext-requests/) 將會追蹤使用 Python[要求](https://pypi.org/project/requests/)程式庫傳送的要求。

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>與 "HTTPlib" 整合的相依性

使用 OpenCensus 整合來追蹤您的連出要求 `httplib` 。

從 PyPI 下載並安裝， `opencensus-ext-httplib` 並將它新增至追蹤整合。 [PyPI](https://pypi.org/project/opencensus-ext-httplib/) 將會追蹤使用[HTTP](https://docs.python.org/3.7/library/http.client.html)傳送的要求 Python3 或[HTTPlib](https://docs.python.org/2/library/httplib.html) for Python2。

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>與 "django" 整合的相依性

使用 OpenCensus 整合來追蹤您的連出 Django 要求 `django` 。

> [!NOTE]
> 唯一追蹤的傳出 Django 要求是對資料庫發出的呼叫。 如需對 Django 應用程式提出的要求，請參閱連[入要求](./opencensus-python-request.md#tracking-django-applications)。

`opencensus-ext-django`從[PyPI](https://pypi.org/project/opencensus-ext-django/)下載並安裝，並將下列程式程式碼新增至 Django 檔案中的 `MIDDLEWARE` 區段 `settings.py` 。

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

可以提供其他設定，請閱讀[自訂](https://github.com/census-instrumentation/opencensus-python#customization)以取得完整的參考。

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
            connection_string="InstrumentationKey=<your-ikey-here>"
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>與「mysql」整合的相依性

使用 OpenCensus 整合來追蹤您的 MYSQL 相依性 `mysql` 。 此整合支援[mysql-連接器](https://pypi.org/project/mysql-connector-python/)程式庫。

從 PyPI 下載並安裝， `opencensus-ext-mysql` 並將下列幾行新增至您的程式碼。 [PyPI](https://pypi.org/project/opencensus-ext-mysql/)

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>與 "pymysql" 整合的相依性

使用 OpenCensus 整合來追蹤您的 PyMySQL 相依性 `pymysql` 。

從 PyPI 下載並安裝， `opencensus-ext-pymysql` 並將下列幾行新增至您的程式碼。 [PyPI](https://pypi.org/project/opencensus-ext-pymysql/)

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>與 "于 postgresql" 整合的相依性

使用 OpenCensus 整合來追蹤您的于 postgresql 相依性 `postgresql` 。 此整合支援[psycopg2](https://pypi.org/project/psycopg2/)程式庫。

從 PyPI 下載並安裝， `opencensus-ext-postgresql` 並將下列幾行新增至您的程式碼。 [PyPI](https://pypi.org/project/opencensus-ext-postgresql/)

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>與 "pymongo" 整合的相依性

使用 OpenCensus 整合來追蹤您的 MongoDB 相依性 `pymongo` 。 此整合支援[pymongo](https://pypi.org/project/pymongo/)程式庫。

從 PyPI 下載並安裝， `opencensus-ext-pymongo` 並將下列幾行新增至您的程式碼。 [PyPI](https://pypi.org/project/opencensus-ext-pymongo/)

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>與 "sqlalchemy" 整合的相依性

使用 OpenCensus 整合來追蹤您的相依性 SQLAlchemy `sqlalchemy` 。 不論基礎資料庫為何，此整合都會追蹤[sqlalchemy](https://pypi.org/project/SQLAlchemy/)套件的使用方式。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>後續步驟

* [應用程式對應](../../azure-monitor/app/app-map.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
* [搜尋](../../azure-monitor/app/diagnostic-search.md)
* [記錄（分析）查詢](../../azure-monitor/log-query/log-query-overview.md)
* [交易診斷](../../azure-monitor/app/transaction-diagnostics.md)
