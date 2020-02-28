---
title: 使用 OpenCensus Python Azure 應用程式深入解析中的相依性追蹤 |Microsoft Docs
description: 透過 OpenCensus Python 監視 Python 應用程式的相依性呼叫。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669925"
---
# <a name="track-dependencies-with-opencensus-python"></a>使用 OpenCensus Python 追蹤相依性

「相依性」（dependency）是指應用程式所呼叫的外部元件。 使用 OpenCensus Python 和其各種整合來收集相依性資料。 然後，資料會以 `dependencies` 遙測傳送至 Azure 監視器下的 Application Insights。

首先，使用最新的[OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)檢測您的 python 應用程式。

## <a name="in-process-dependencies"></a>同進程相依性

OpenCensus 適用于 Azure 監視器的 Python SDK 可讓您傳送「同進程」相依性遙測（應用程式內所發生的資訊和邏輯）。 同進程相依性將會有 [`type`] 欄位，做為分析中的 `INPROC`。

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>與「要求」整合的相依性

使用 OpenCensus `requests` 整合來追蹤您的連出要求。

從[PyPI](https://pypi.org/project/opencensus-ext-requests/)下載並安裝 `opencensus-ext-requests`，並將它新增至追蹤整合。 將會追蹤使用 Python[要求](https://pypi.org/project/requests/)程式庫傳送的要求。

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

使用 OpenCensus `httplib` 整合來追蹤您的連出要求。

從[PyPI](https://pypi.org/project/opencensus-ext-httplib/)下載並安裝 `opencensus-ext-httplib`，並將它新增至追蹤整合。 將會追蹤使用[HTTP](https://docs.python.org/3.7/library/http.client.html)傳送的要求 Python3 或[HTTPlib](https://docs.python.org/2/library/httplib.html) for Python2。

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

使用 OpenCensus `django` 整合來追蹤您的連出 Django 要求。

從[PyPI](https://pypi.org/project/opencensus-ext-django/)下載並安裝 `opencensus-ext-django`，並將下列程式程式碼新增至 Django `settings.py` 檔的 `MIDDLEWARE` 區段。

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

使用 OpenCensus `mysql` 整合來追蹤您的 MYSQL 相依性。 此整合支援[mysql-連接器](https://pypi.org/project/mysql-connector-python/)程式庫。

從[PyPI](https://pypi.org/project/opencensus-ext-mysql/)下載並安裝 `opencensus-ext-mysql`，並將下列幾行新增至您的程式碼。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>與 "pymysql" 整合的相依性

使用 OpenCensus `pymysql` 整合來追蹤您的 PyMySQL 相依性。

從[PyPI](https://pypi.org/project/opencensus-ext-pymysql/)下載並安裝 `opencensus-ext-pymysql`，並將下列幾行新增至您的程式碼。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>與 "于 postgresql" 整合的相依性

使用 OpenCensus `postgresql` 整合來追蹤您的于 postgresql 相依性。 此整合支援[psycopg2](https://pypi.org/project/psycopg2/)程式庫。

從[PyPI](https://pypi.org/project/opencensus-ext-postgresql/)下載並安裝 `opencensus-ext-postgresql`，並將下列幾行新增至您的程式碼。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>與 "pymongo" 整合的相依性

使用 OpenCensus `pymongo` 整合來追蹤您的 MongoDB 相依性。 此整合支援[pymongo](https://pypi.org/project/pymongo/)程式庫。

從[PyPI](https://pypi.org/project/opencensus-ext-pymongo/)下載並安裝 `opencensus-ext-pymongo`，並將下列幾行新增至您的程式碼。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>與 "sqlalchemy" 整合的相依性

使用 OpenCensus `sqlalchemy` 整合來追蹤您的相依性。 不論基礎資料庫為何，此整合都會追蹤[sqlalchemy](https://pypi.org/project/SQLAlchemy/)套件的使用方式。

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
