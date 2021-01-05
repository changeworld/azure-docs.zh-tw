---
title: 改善 Azure Functions 中的 Python 應用程式輸送量效能
description: 瞭解如何使用可高度效能的 Python 開發 Azure Functions 應用程式，並在負載下妥善調整規模。
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3383ec322903d15c6090630890d14ce6d90bc96f
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899392"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>改善 Azure Functions 中的 Python 應用程式輸送量效能

使用 Python 開發 Azure Functions 時，您需要瞭解函式的執行方式，以及該效能如何影響函數應用程式的調整方式。 設計高效能應用程式時，需要更重要。 設計、撰寫和設定函式應用程式時要考慮的主要因素是水準調整和輸送量效能設定。

## <a name="horizontal-scaling"></a>水平調整規模
根據預設，Azure Functions 會自動監視應用程式上的負載，並視需要為 Python 建立其他主機執行個體。 Azure Functions 針對不同的觸發程式類型使用內建閾值，以決定何時要加入實例，例如訊息的存留期和 QueueTrigger 的佇列大小。 這些閾值不是使用者可設定的。 如需詳細資訊，請參閱[耗用量和進階方案的運作方式](functions-scale.md#how-the-consumption-and-premium-plans-work)。

## <a name="improving-throughput-performance"></a>改善輸送量效能

預設設定適用于大部分的 Azure Functions 應用程式。 不過，您可以根據您的工作負載設定檔來採用設定，以改善應用程式輸送量的效能。 第一個步驟是瞭解您正在執行的工作負載類型。

|| I/o 系結工作負載 | CPU 系結的工作負載 |
|--| -- | -- |
|函數應用程式特性| <ul><li>應用程式需要處理許多並行調用。</li> <li> 應用程式會處理大量 i/o 事件，例如網路呼叫和磁片讀取/寫入。</li> </ul>| <ul><li>應用程式會執行長時間執行的計算，例如調整大小的影像。</li> <li>應用程式會進行資料轉換。</li> </ul> |
|範例| <ul><li>Web API</li><ul> | <ul><li>資料處理</li><li> 機器學習推斷</li><ul>|

 
因為真實世界的函式工作負載通常是 i/o 和 CPU 系結的混合，所以您應該在實際的生產負載下分析應用程式。


### <a name="performance-specific-configurations"></a>效能特定設定

瞭解函數應用程式的工作負載設定檔之後，您可以使用下列設定來改善函式的輸送量效能。

* [非同步](#async)
* [多個語言背景工作](#use-multiple-language-worker-processes)
* [語言工作者進程內的最大工作者](#set-up-max-workers-within-a-language-worker-process)
* [事件迴圈](#managing-event-loop)
* [垂直調整](#vertical-scaling)



#### <a name="async"></a>Async

因為 [python 是單一執行緒的運行](https://wiki.python.org/moin/GlobalInterpreterLock)時間，所以 python 的主控制項實例預設只能處理一個函式呼叫。 針對處理大量 i/o 事件和/或 i/o 系結的應用程式，您可以透過非同步方式執行函式來大幅改善效能。

若要以非同步方式執行函式，請使用 `async def` 陳述式，這會搭配 [asyncio](https://docs.python.org/3/library/asyncio.html) 直接執行函式：

```python
async def main():
    await some_nonblocking_socket_io_op()
```
以下是使用 HTTP 觸發程式（使用 [>aioHTTP](https://pypi.org/project/aiohttp/) HTTP 用戶端）的函式範例：

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


沒有關鍵詞的函 `async` 式會自動在 ThreadPoolExecutor 執行緒集區中執行：

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

為了達到以非同步方式執行函式的完整優點，程式碼中使用的 i/o 作業/程式庫也需要非同步實作為。 在定義為非同步函式中使用同步 i/o 作業， **可能會** 影響整體效能。 如果您使用的程式庫沒有實作為非同步版本，您仍然可以透過在應用程式中 [管理事件迴圈](#managing-event-loop) ，以非同步方式執行程式碼來獲益。 

以下是一些已實作為非同步模式的用戶端程式庫範例：
- [>aioHTTP](https://pypi.org/project/aiohttp/) -asyncio 的 Http 用戶端/伺服器 
- [串流 API](https://docs.python.org/3/library/asyncio-stream.html) -高階非同步/等候就緒的基本專案，以搭配網路連接使用
- [Janus 佇列](https://pypi.org/project/janus/) -適用于 Python 的安全線程 asyncio 感知佇列
- [pyzmq](https://pypi.org/project/pyzmq/) -適用于 ZeroMQ 的 Python 系結
 
##### <a name="understanding-async-in-python-worker"></a>瞭解 python 背景工作中的非同步

當您在函式簽章之前定義時 `async` ，Python 會將函式標示為協同程式。 呼叫協同程式時，可以將它排程為事件迴圈中的工作。 當您 `await` 在非同步函式中呼叫時，它會將接續註冊至事件迴圈，並允許事件迴圈在等候時間處理下一個工作。

在我們的 Python 背景工作中，背景工作角色會與客戶的函式共用事件迴圈 `async` ，而且能夠同時處理多個要求。 我們強烈建議客戶使用 asyncio 相容程式庫， (例如 [>aioHTTP](https://pypi.org/project/aiohttp/)、 [pyzmq](https://pypi.org/project/pyzmq/)) 。 相較于以同步方式執行的程式庫，採用這些建議將會大幅增加您的函式輸送量。

> [!NOTE]
>  如果您的函式宣告為 `async` 不含任何專案，則函式 `await` 的效能會受到嚴重影響，因為系統會封鎖事件迴圈，以防止 python 背景工作處理並行要求。

#### <a name="use-multiple-language-worker-processes"></a>使用多個語言背景工作處理序

根據預設，每個 Functions 主機執行個體都有單一語言背景工作處理序。 您可以使用 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 應用程式設定，來增加每個主機的背景工作處理序數目 (最多10個)。 Azure Functions 接著會嘗試在這些背景工作中平均散發同時函式叫用。

針對受 CPU 限制的應用程式，您應該將語言背景工作角色的數目設定為與每個函數應用程式可用的核心數目相同或更高。 若要深入瞭解，請參閱 [可用的實例 sku](functions-premium-plan.md#available-instance-skus)。 

I/o 系結應用程式可能也會因為增加的工作者進程數目超過可用的核心數目而受益。 請記住，將工作者數目設定過高可能會影響整體效能，因為所需的內容切換數目已增加。 

FUNCTIONS_WORKER_PROCESS_COUNT 適用於 Functions 在擴增應用程式以符合需求時所建立的每個主機。

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>設定語言工作者進程內的最大工作者

如同在 async [區段](#understanding-async-in-python-worker)中所述，Python 語言背景工作角色會以不同的方式來處理函式和 [協同程式](https://docs.python.org/3/library/asyncio-task.html#coroutines) 。 協同程式會在執行語言背景工作角色的相同事件迴圈中執行。 另一方面，函式調用會在 [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor)中執行，而該函式會由語言工作者以執行緒的形式來維護。

您可以使用 [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) 應用程式設定，設定允許執行同步處理函式的最大背景工作角色的值。 此值會設定 `max_worker` ThreadPoolExecutor 物件的引數，這可讓 Python 使用最多執行緒的集區 `max_worker` 來以非同步方式執行呼叫。 `PYTHON_THREADPOOL_THREAD_COUNT`會套用至函式主機所建立的每個背景工作，而 Python 會決定何時建立新的執行緒，或重複使用現有的閒置執行緒。 針對較舊的 Python 版本 (也就是、、 `3.8` `3.7` 和 `3.6`) ， `max_worker` 值會設定為1。 針對 Python 版本 `3.9` ， `max_worker` 會設定為 `None` 。

針對受 CPU 限制的應用程式，您應該將設定保留為較低的數位，從1開始，並隨著您試驗工作負載而增加。 這項建議是為了減少內容切換花費的時間，並讓 CPU 系結的工作完成。

針對 i/o 系結的應用程式，您應該藉由增加處理每個叫用的執行緒數目來查看大幅增益。 建議您從 Python 預設值開始-核心數 + 4，然後根據您看到的輸送量值進行調整。

針對混合工作負載的應用程式，您應該平衡和設定， `FUNCTIONS_WORKER_PROCESS_COUNT` `PYTHON_THREADPOOL_THREAD_COUNT` 以將輸送量最大化。 若要瞭解您的函式應用程式花費最多時間，建議您對其進行分析，並根據其出現的行為來設定值。 另請 [參閱本節，以瞭解](#use-multiple-language-worker-processes) FUNCTIONS_WORKER_PROCESS_COUNT 的應用程式設定。

> [!NOTE]
>  雖然這些建議同時適用于 HTTP 和非 HTTP 觸發的函式，但您可能需要調整非 HTTP 觸發函式的其他觸發程式特定設定，以從您的函數應用程式取得預期的效能。 如需有關此專案的詳細資訊，請參閱這 [篇文章](functions-best-practices.md)。


#### <a name="managing-event-loop"></a>管理事件迴圈

您應使用 asyncio 相容的協力廠商程式庫。 如果沒有任何協力廠商程式庫符合您的需求，您也可以在 Azure Functions 中管理事件迴圈。 管理事件迴圈可提供您更多的計算資源管理彈性，也可以將同步的 i/o 程式庫包裝到協同程式中。

有許多實用的 Python 官方檔會使用內建的 **asyncio** 程式庫討論 [協同程式和](https://docs.python.org/3/library/asyncio-task.html)工作和 [事件迴圈](https://docs.python.org/3.8/library/asyncio-eventloop.html)。

以下列 [要求](https://github.com/psf/requests) 程式庫作為範例，此程式碼片段會使用 **asyncio** 程式庫將方法包裝到 `requests.get()` 協同程式中，同時執行多個 web 要求以同時 SAMPLE_URL。


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>垂直調整規模
如需更多處理單位（尤其是在 CPU 系結作業中），您可以使用較高的規格升級到高階方案來取得此功能。 使用較高的處理單位，您可以根據可用的核心數目來調整工作者進程計數的數目，並達到更高的平行處理原則程度。 

## <a name="next-steps"></a>後續步驟

如需 Azure Functions Python 開發的詳細資訊，請參閱下列資源：

* [Azure Functions Python 開發人員指南](functions-reference-python.md)
* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 開發人員參考](functions-reference.md)

