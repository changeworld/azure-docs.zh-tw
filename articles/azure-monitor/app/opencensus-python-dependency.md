---
title: 使用 OpenCensus Python 在 Azure 應用程式洞察中跟蹤依賴項 |微軟文檔
description: 通過 OpenCensus Python 監視 Python 應用的依賴項調用。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669925"
---
# <a name="track-dependencies-with-opencensus-python"></a>使用 OpenCensus Python 跟蹤依賴項

依賴項是應用程式調用的外部元件。 依賴項資料使用 OpenCensus Python 及其各種集成進行收集。 然後，資料作為`dependencies`遙測資料發送到 Azure 監視器下的應用程式見解。

首先，使用最新的[OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md)來檢測您的 Python 應用程式。

## <a name="in-process-dependencies"></a>進程內依賴項

Azure 監視器的 OpenCensus Python SDK 允許您發送"進程內"依賴項遙測（應用程式中發生的資訊和邏輯）。 進程內依賴項將具有`type`與分析一樣`INPROC`的欄位。

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>具有"請求"集成的依賴項

使用 OpenCensus`requests`集成跟蹤傳出請求。

從`opencensus-ext-requests`[PyPI](https://pypi.org/project/opencensus-ext-requests/)下載並安裝，並將其添加到跟蹤集成中。 將使用 Python 請求庫發送[的請求](https://pypi.org/project/requests/)將被跟蹤。

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

## <a name="dependencies-with-httplib-integration"></a>具有"HTTPlib"集成的依賴項

通過 OpenCensus`httplib`集成跟蹤傳出請求。

從`opencensus-ext-httplib`[PyPI](https://pypi.org/project/opencensus-ext-httplib/)下載並安裝，並將其添加到跟蹤集成中。 將使用[HTTP.client](https://docs.python.org/3.7/library/http.client.html)為 Python3 或[HTTPlib](https://docs.python.org/2/library/httplib.html)發送的 Python2 請求將被跟蹤。

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

## <a name="dependencies-with-django-integration"></a>具有"django"集成的依賴關係

使用 OpenCensus`django`集成跟蹤傳出的 Django 請求。

從[PyPI](https://pypi.org/project/opencensus-ext-django/)下載並安裝`MIDDLEWARE``settings.py``opencensus-ext-django`，並將以下行添加到 Django 檔中的部分。

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

可以提供其他配置，讀取[自訂以](https://github.com/census-instrumentation/opencensus-python#customization)進行完整參考。

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

## <a name="dependencies-with-mysql-integration"></a>具有"mysql"集成的依賴項

使用 OpenCensus`mysql`集成跟蹤 MYSQL 依賴項。 此集成支援[mysql 連接器](https://pypi.org/project/mysql-connector-python/)庫。

從`opencensus-ext-mysql`[PyPI](https://pypi.org/project/opencensus-ext-mysql/)下載並安裝，並將以下行添加到代碼中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>具有"pymysql"集成的依賴項

使用 OpenCensus`pymysql`集成跟蹤您的 PyMySQL 依賴項。

從`opencensus-ext-pymysql`[PyPI](https://pypi.org/project/opencensus-ext-pymysql/)下載並安裝，並將以下行添加到代碼中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>具有"後格雷sql"集成的依賴項

使用 OpenCensus`postgresql`集成跟蹤 PostgreSQL 依賴項。 此集成支援[psycopg2](https://pypi.org/project/psycopg2/)庫。

從`opencensus-ext-postgresql`[PyPI](https://pypi.org/project/opencensus-ext-postgresql/)下載並安裝，並將以下行添加到代碼中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>具有"pymongo"集成的依賴關係

使用 OpenCensus`pymongo`集成跟蹤您的 MongoDB 依賴項。 此集成支援[pymongo](https://pypi.org/project/pymongo/)庫。

從`opencensus-ext-pymongo`[PyPI](https://pypi.org/project/opencensus-ext-pymongo/)下載並安裝，並將以下行添加到代碼中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>具有"sqlchemy"集成的依賴項

使用 OpenCensus`sqlalchemy`集成使用 SQLAlchemy 跟蹤您的依賴項。 此集成跟蹤[sqlalchemy](https://pypi.org/project/SQLAlchemy/)包的使用方式，而不考慮基礎資料庫。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>後續步驟

* [應用程式映射](../../azure-monitor/app/app-map.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
* [搜尋](../../azure-monitor/app/diagnostic-search.md)
* [日誌（分析）查詢](../../azure-monitor/log-query/log-query-overview.md)
* [交易診斷](../../azure-monitor/app/transaction-diagnostics.md)
