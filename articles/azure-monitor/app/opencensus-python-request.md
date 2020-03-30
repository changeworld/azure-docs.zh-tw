---
title: 使用 OpenCensus Python 在 Azure 應用程式洞察中進行傳入請求跟蹤 |微軟文檔
description: 通過 OpenCensus Python 監視 Python 應用的請求呼叫。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669942"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>使用 OpenCensus Python 跟蹤傳入請求

傳入請求資料使用 OpenCensus Python 及其各種集成進行收集。 跟蹤發送到在常用 Web 框架`django``flask`和 之上構建的 Web 應用程式的傳入請求`pyramid`資料。 然後，資料作為`requests`遙測資料發送到 Azure 監視器下的應用程式見解。

首先，使用最新的[OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md)來檢測您的 Python 應用程式。

## <a name="tracking-django-applications"></a>跟蹤 Django 應用程式

1. 從[PyPI](https://pypi.org/project/opencensus-ext-django/)下載和安裝`django``opencensus-ext-django`，使用中介軟體檢測您的應用程式。 發送到您的`django`應用程式的傳入請求將被跟蹤。

2. 包括在`opencensus.ext.django.middleware.OpencensusMiddleware`您的`settings.py`檔下`MIDDLEWARE`。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 確保 Azure 匯出器在 下`settings.py`已`OPENCENSUS`正確配置。

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

4. 您還可以為`settings.py`不想跟蹤的請求向下`BLACKLIST_PATHS`添加 URL。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>跟蹤燒瓶應用

1. 從[PyPI](https://pypi.org/project/opencensus-ext-flask/)下載和安裝`flask``opencensus-ext-flask`，使用中介軟體檢測您的應用程式。 發送到您的`flask`應用程式的傳入請求將被跟蹤。

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

2. 您可以直接在代碼`flask`中配置中介軟體。 對於來自不希望跟蹤的 URL 的請求，請將它們添加到`BLACKLIST_PATHS`。

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

## <a name="tracking-pyramid-applications"></a>跟蹤金字塔應用程式

1. 從[PyPI](https://pypi.org/project/opencensus-ext-pyramid/)下載和安裝`pyramid``opencensus-ext-django`，用補間檢測您的應用程式。 發送到您的`pyramid`應用程式的傳入請求將被跟蹤。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 可以直接在代碼中`pyramid`配置補間。 對於來自不希望跟蹤的 URL 的請求，請將它們添加到`BLACKLIST_PATHS`。

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

* [應用程式映射](../../azure-monitor/app/app-map.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
* [搜尋](../../azure-monitor/app/diagnostic-search.md)
* [日誌（分析）查詢](../../azure-monitor/log-query/log-query-overview.md)
* [交易診斷](../../azure-monitor/app/transaction-diagnostics.md)
