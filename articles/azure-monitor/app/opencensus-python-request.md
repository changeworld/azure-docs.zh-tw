---
title: Azure 應用程式 Insights 中使用 OpenCensus Python 的傳入要求追蹤 |Microsoft Docs
description: 透過 OpenCensus Python 監視 Python 應用程式的要求呼叫。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: c94bc949f13ee19a9d2150c9d3c1b6a2bdb959b2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850061"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>使用 OpenCensus Python 追蹤連入要求

使用 OpenCensus Python 和其各種整合來收集傳入的要求資料。 追蹤傳送至 web 應用程式的傳入要求資料，建置於熱門的 web 架構 `django` `flask` 和上 `pyramid` 。 然後，資料會傳送至 Azure 監視器的 Application Insights，作為 `requests` 遙測。

首先，使用最新的[OpenCensus PYTHON SDK](./opencensus-python.md)檢測您的 python 應用程式。

## <a name="tracking-django-applications"></a>追蹤 Django 應用程式

1. 從 PyPI 下載並安裝， `opencensus-ext-django` 並使用中介軟體檢測您的應用程式[PyPI](https://pypi.org/project/opencensus-ext-django/) `django` 。 將會追蹤傳送至您應用程式的傳入要求 `django` 。

2. 在 `opencensus.ext.django.middleware.OpencensusMiddleware` 您的檔案中包含 `settings.py` `MIDDLEWARE` 。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 請確定中的已正確設定 AzureExporter `settings.py` `OPENCENSUS` 。 對於來自您不想要追蹤之 url 的要求，請將它們新增至 `BLACKLIST_PATHS` 。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>追蹤 Flask 應用程式

1. 從 PyPI 下載並安裝， `opencensus-ext-flask` 並使用中介軟體檢測您的應用程式[PyPI](https://pypi.org/project/opencensus-ext-flask/) `flask` 。 將會追蹤傳送至您應用程式的傳入要求 `flask` 。

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. 您也可以透過設定 `flask` 應用程式 `app.config` 。 對於來自您不想要追蹤之 url 的要求，請將它們新增至 `BLACKLIST_PATHS` 。

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>追蹤金字塔應用程式

1. 從 PyPI 下載並安裝， `opencensus-ext-django` 並使用補間檢測您的應用[PyPI](https://pypi.org/project/opencensus-ext-pyramid/)程式 `pyramid` 。 將會追蹤傳送至您應用程式的傳入要求 `pyramid` 。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 您可以 `pyramid` 直接在程式碼中設定您的補間。 對於來自您不想要追蹤之 url 的要求，請將它們新增至 `BLACKLIST_PATHS` 。

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>後續步驟

* [應用程式對應](./app-map.md)
* [可用性](./monitor-web-app-availability.md)
* [搜尋](./diagnostic-search.md)
* [記錄 (分析) 查詢](../log-query/log-query-overview.md)
* [交易診斷](./transaction-diagnostics.md)

