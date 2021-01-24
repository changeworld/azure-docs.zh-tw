---
title: 使用 OpenCensus Python Azure 應用程式見解中的傳入要求追蹤 |Microsoft Docs
description: 透過 OpenCensus Python 監視 Python 應用程式的要求呼叫。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 4abb795335bfcb2c9b335d4fb09ddc9fdb2476b4
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746572"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>使用 OpenCensus Python 追蹤連入要求

傳入的要求資料是使用 OpenCensus Python 及其各種整合來收集。 追蹤傳送至 web 應用程式的傳入要求資料（建立在熱門的 web 架構 `django` 和上） `flask` `pyramid` 。 然後，資料會傳送至 Azure 監視器作為遙測下的 Application Insights `requests` 。

首先，使用最新的 [OpenCensus PYTHON SDK](./opencensus-python.md)來檢測您的 python 應用程式。

## <a name="tracking-django-applications"></a>追蹤 Django 應用程式

1. `opencensus-ext-django`從[PyPI](https://pypi.org/project/opencensus-ext-django/)下載並安裝，然後使用中介軟體來檢測您的應用程式 `django` 。 將會追蹤傳送到您應用程式的連入要求 `django` 。

2. 包含 `opencensus.ext.django.middleware.OpencensusMiddleware` 在您的檔案中 `settings.py` `MIDDLEWARE` 。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 請確定您的下已正確設定 `settings.py` AzureExporter `OPENCENSUS` 。 針對您不想要追蹤的 url 要求，將其新增至 `EXCLUDELIST_PATHS` 。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>追蹤 Flask 應用程式

1. `opencensus-ext-flask`從[PyPI](https://pypi.org/project/opencensus-ext-flask/)下載並安裝，然後使用中介軟體來檢測您的應用程式 `flask` 。 將會追蹤傳送到您應用程式的連入要求 `flask` 。

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

2. 您也可以透過設定您的 `flask` 應用程式 `app.config` 。 針對您不想要追蹤的 url 要求，將其新增至 `EXCLUDELIST_PATHS` 。

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>追蹤金字塔應用程式

1. `opencensus-ext-django`從[PyPI](https://pypi.org/project/opencensus-ext-pyramid/)下載並安裝，並使用補間檢測您的應用程式 `pyramid` 。 將會追蹤傳送到您應用程式的連入要求 `pyramid` 。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 您可以 `pyramid` 直接在程式碼中設定您的補間。 針對您不想要追蹤的 url 要求，將其新增至 `EXCLUDELIST_PATHS` 。

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>追蹤 FastAPI 應用程式

OpenCensus 沒有適用于 FastAPI 的擴充功能。 若要撰寫您自己的 FastAPI 中介軟體，請完成下列步驟：

1. 需要下列相依性： 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. 新增 [FastAPI 中介軟體](https://fastapi.tiangolo.com/tutorial/middleware/)。 請確定您已設定 span 種類伺服器： `span.span_kind = SpanKind.SERVER` 。

3. 執行您的應用程式。 對您 FastAPI 應用程式所進行的呼叫應該會自動追蹤，而且應該將遙測資料直接記錄至 Azure 監視器。

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>後續步驟

* [應用程式對應](./app-map.md)
* [可用性](./monitor-web-app-availability.md)
* [搜尋](./diagnostic-search.md)
* [記錄 (分析) 查詢](../log-query/log-query-overview.md)
* [交易診斷](./transaction-diagnostics.md)

