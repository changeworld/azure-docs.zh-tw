---
title: 使用 OpenCensus Python Azure 應用程式見解中的相依性追蹤 |Microsoft Docs
description: 透過 OpenCensus Python 監視 Python 應用程式的相依性呼叫。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: abfd5e104bd4854781a0d3c9d08544506279518a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850078"
---
# <a name="track-dependencies-with-opencensus-python"></a>使用 OpenCensus Python 追蹤相依性

相依性是由應用程式呼叫的外部元件。 相依性資料是使用 OpenCensus Python 及其各種整合來收集。 然後，資料會傳送至 Azure 監視器作為遙測下的 Application Insights `dependencies` 。

首先，使用最新的 [OpenCensus PYTHON SDK](./opencensus-python.md)來檢測您的 python 應用程式。

## <a name="in-process-dependencies"></a>同進程相依性

適用于 Azure 監視器的 OpenCensus Python SDK 可讓您傳送「同進程」相依性遙測， (在應用程式) 中發生的資訊和邏輯。 同進程的相依性會讓 `type` 欄位成為 `INPROC` analytics 中的欄位。

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>與「要求」整合相關的相依性

使用 OpenCensus 整合追蹤您的連出要求 `requests` 。

`opencensus-ext-requests`從[PyPI](https://pypi.org/project/opencensus-ext-requests/)下載並安裝，並將它新增至追蹤整合。 將會追蹤使用 Python [要求](https://pypi.org/project/requests/) 程式庫傳送的要求。

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

使用 OpenCensus 整合追蹤您的連出要求 `httplib` 。

`opencensus-ext-httplib`從[PyPI](https://pypi.org/project/opencensus-ext-httplib/)下載並安裝，並將它新增至追蹤整合。 使用 HTTP 傳送的要求。將會追蹤 Python3 或[HTTPlib](https://docs.python.org/2/library/httplib.html) for Python2 的[用戶端](https://docs.python.org/3.7/library/http.client.html)。

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

使用 OpenCensus 整合來追蹤傳出的 Django 要求 `django` 。

> [!NOTE]
> 唯一追蹤的傳出 Django 要求是對資料庫發出的呼叫。 針對 Django 應用程式所提出的要求，請參閱連 [入要求](./opencensus-python-request.md#tracking-django-applications)。

`opencensus-ext-django`從[PyPI](https://pypi.org/project/opencensus-ext-django/)下載並安裝，然後將下列這一行新增至 Django 檔案中的 `MIDDLEWARE` 區段 `settings.py` 。

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

您可以提供其他設定、讀取 [自訂](https://github.com/census-instrumentation/opencensus-python#customization) 以取得完整參考。

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

## <a name="dependencies-with-mysql-integration"></a>與「mysql」整合相關的相依性

使用 OpenCensus 整合來追蹤您的 MYSQL 相依性 `mysql` 。 這種整合支援 [mysql 連接器](https://pypi.org/project/mysql-connector-python/) 程式庫。

`opencensus-ext-mysql`從[PyPI](https://pypi.org/project/opencensus-ext-mysql/)下載並安裝，並將下列幾行新增至您的程式碼。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>與 "pymysql" 整合的相依性

使用 OpenCensus 整合來追蹤您的 PyMySQL 相依性 `pymysql` 。

`opencensus-ext-pymysql`從[PyPI](https://pypi.org/project/opencensus-ext-pymysql/)下載並安裝，並將下列幾行新增至您的程式碼。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>與 "于 postgresql" 整合的相依性

使用 OpenCensus 整合來追蹤您的于 postgresql 相依性 `postgresql` 。 這種整合支援 [psycopg2](https://pypi.org/project/psycopg2/) 程式庫。

`opencensus-ext-postgresql`從[PyPI](https://pypi.org/project/opencensus-ext-postgresql/)下載並安裝，並將下列幾行新增至您的程式碼。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>與 "pymongo" 整合的相依性

使用 OpenCensus 整合來追蹤您的 MongoDB 相依性 `pymongo` 。 這種整合支援 [pymongo](https://pypi.org/project/pymongo/) 程式庫。

`opencensus-ext-pymongo`從[PyPI](https://pypi.org/project/opencensus-ext-pymongo/)下載並安裝，並將下列幾行新增至您的程式碼。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>與 "sqlalchemy" 整合的相依性

使用 OpenCensus 整合以 SQLAlchemy 追蹤您的相依性 `sqlalchemy` 。 這項整合會追蹤 [sqlalchemy](https://pypi.org/project/SQLAlchemy/) 封裝的使用方式，而不論基礎資料庫為何。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>後續步驟

* [應用程式對應](./app-map.md)
* [可用性](./monitor-web-app-availability.md)
* [搜尋](./diagnostic-search.md)
* [記錄 (分析) 查詢](../log-query/log-query-overview.md)
* [交易診斷](./transaction-diagnostics.md)

