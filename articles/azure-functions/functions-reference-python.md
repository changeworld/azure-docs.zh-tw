---
title: 適用於 Azure Functions 的 Python 開發人員參考
description: 了解如何使用 Python 開發函式
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 8ee13b59812e6a212fbafcf4ea6bfc171e735dc3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190699"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 開發人員指南

本文是使用 Python 開發 Azure Functions 的簡介。 本文假設您已閱讀過下列 [Azure Functions 開發人員指南](functions-reference.md)。 

如需 Python 中的獨立函式範例專案，請參閱 Python 函式[範例](/samples/browse/?products=azure-functions&languages=python)。 

## <a name="programming-model"></a>程式設計模型

Azure Functions 預期函式在 Python 腳本中是可處理輸入並產生輸出的無狀態方法。 根據預設，執行時間會預期方法會實作為在 `__init__.py` 檔案中稱為 `main()` 的全域方法。 您也可以[指定替代的進入點](#alternate-entry-point)。

來自觸發程式和系結的資料，會使用函式*json*檔案中定義的 `name` 屬性，透過方法屬性系結至函式。 例如，下面的_函數. json_描述由名為 `req`的 HTTP 要求所觸發的簡單函式：

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

根據這個定義，包含函式程式碼的 `__init__.py` 檔案可能看起來像下列範例：

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

您也可以使用 Python 類型注釋，在函式中明確宣告屬性類型和傳回類型。 這可協助您使用許多 Python 程式碼編輯器所提供的 intellisense 和自動完成功能。

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

請使用 [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 套件中所包含的 Python 註釋，以將輸入和輸出繫結至方法。

## <a name="alternate-entry-point"></a>替代進入點

您可以選擇性地在*函數. json*檔案中指定 `scriptFile` 和 `entryPoint` 屬性，以變更函式的預設行為。 例如 _，下列函_式會指示執行時間在_main.py_檔案中使用 `customentry()` 方法，做為您的 Azure 函式的進入點。

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>資料夾結構

Python 函式專案的建議資料夾結構如下列範例所示：

```
 __app__
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 tests
```
主要專案資料夾（\_\_應用程式\_\_）可以包含下列檔案：

* 在*本機執行時*，用來儲存應用程式設定和連接字串的。 此檔案不會發行至 Azure。 若要深入瞭解，請參閱[local. settings. file](functions-run-local.md#local-settings-file)。
* *需求 .txt*：包含在發佈至 Azure 時，系統所安裝的套件清單。
* *host. json*：包含會影響函數應用程式中所有函式的全域設定選項。 此檔案會發行至 Azure。 在本機執行時，不支援所有選項。 若要深入瞭解，請參閱[host. json](functions-host-json.md)。
* *funcignore*：（選擇性）宣告不應發行至 Azure 的檔案。
* *.gitignore*：（選擇性）宣告從 git 存放庫排除的檔案，例如 local. settings. json。

每個函式都具有本身的程式碼檔案和繫結設定檔 (function.json)。 

共用程式碼應該保存在 \_\_應用程式\_\_的個別資料夾中。 若要參考 SharedCode 資料夾中的模組，您可以使用下列語法：

```python
from __app__.SharedCode import myFirstHelperFunction
```

若要參考函數的本機模組，您可以使用相對的匯入語法，如下所示：

```python
from . import example
```

將您的專案部署至 Azure 中的函式應用程式時， *FunctionApp*資料夾的完整內容應該包含在套件中，而不是資料夾本身。 我們建議您在與專案資料夾不同的資料夾中維護您的測試，在此範例中 `tests`。 這可讓您不需要將測試程式碼與應用程式一起部署。 如需詳細資訊，請參閱[單元測試](#unit-testing)。

## <a name="triggers-and-inputs"></a>觸發程式和輸入

在 Azure Functions 中輸入會分成兩個類別：觸發程序輸入和額外的輸入。 雖然它們在 `function.json` 檔案中不同，但在 Python 程式碼中的使用方式是相同的。  在本機執行時，觸發程式和輸入來源的連接字串或密碼會對應至 `local.settings.json` 檔案中的值，以及在 Azure 中執行時的應用程式設定。 

例如，下列程式碼示範兩者之間的差異：

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

叫用此函式時，HTTP 要求會以 `req` 形式傳遞至函式。 系統會根據路由 URL 中的_識別碼_從 Azure Blob 儲存體中抓取專案，並在函式主體中以 `obj` 的形式提供。  在這裡，指定的儲存體帳戶是在 AzureWebJobsStorage 應用程式設定中找到的連接字串，這是函數應用程式所使用的相同儲存體帳戶。


## <a name="outputs"></a>Outputs

輸出可以使用傳回值和輸出參數來表示。 如果只有一個輸出，我們建議使用傳回的值。 若為多個輸出，您必須使用輸出參數。

若要使用函式的傳回值作為輸出繫結的值，應該將 `name` 中的繫結 `$return` 屬性設定為 `function.json`。

若要產生多個輸出，請使用[`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python)介面提供的 `set()` 方法，將值指派給系結。 例如，下列函式可以將訊息推送至佇列，同時也會傳回 HTTP 回應。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>記錄

可以透過函式應用程式中的根 [`logging`](https://docs.python.org/3/library/logging.html#module-logging) 處理常式來存取 Azure Functions 執行階段記錄器。 這個記錄器會繫結至 Application Insights，並可讓您將函式執行期間遇到的警告和錯誤加上旗標。

下列範例在透過 HTTP 觸發程序叫用函式時，會記錄一則資訊訊息。

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

其他的記錄方法可讓您在不同追蹤層級寫入主控台記錄中︰

| 方法                 | 描述                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | 在根記錄器上寫入層級為 CRITICAL (重大) 的訊息。  |
| **`error(_message_)`**   | 在根記錄器上寫入層級為 ERROR (錯誤) 的訊息。    |
| **`warning(_message_)`**    | 在根記錄器上寫入層級為 WARNING (警告) 的訊息。  |
| **`info(_message_)`**    | 在根記錄器上寫入層級為 INFO (資訊) 的訊息。  |
| **`debug(_message_)`** | 在根記錄器上寫入層級為 DEBUG (偵錯) 的訊息。  |

若要深入瞭解記錄，請參閱[監視 Azure Functions](functions-monitoring.md)。

## <a name="http-trigger-and-bindings"></a>HTTP 觸發程式和系結

HTTP 觸發程式定義于 function. jon 檔案中。 系結的 `name` 必須符合函式中的具名引數。 在先前的範例中，會使用 `req` 的系結名稱。 這個參數是[HttpRequest]物件，而且會傳回[HttpResponse]物件。

您可以從[HttpRequest]物件取得要求標頭、查詢參數、路由參數和訊息內文。 

下列範例來自[Python 的 HTTP 觸發程式範本](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)。 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

在此函數中，會從[HttpRequest]物件的 `params` 參數取得 `name` 查詢參數的值。 JSON 編碼的訊息本文會使用 `get_json` 方法來讀取。 

同樣地，您可以在傳回的[HttpResponse]物件中設定回應訊息的 `status_code` 和 `headers`。

## <a name="scaling-and-concurrency"></a>調整和並行

根據預設，Azure Functions 會自動監視應用程式上的負載，並視需要建立適用于 Python 的其他主機實例。 函式會針對不同的觸發程式類型使用內建（非使用者可設定）閾值，以決定何時要新增實例，例如訊息的存留期和 QueueTrigger 的佇列大小。 如需詳細資訊，請參閱[耗用量和 Premium 方案的工作方式](functions-scale.md#how-the-consumption-and-premium-plans-work)。

這種調整行為足以滿足許多應用程式的需求。 不過，具有下列任何特性的應用程式可能無法有效地進行調整：

- 應用程式必須處理許多並行調用。
- 應用程式會處理大量的 i/o 事件。
- 應用程式是 i/o 系結的。

在這種情況下，您可以藉由採用非同步模式和使用多個語言背景工作進程，進一步改善效能。

### <a name="async"></a>Async

因為 Python 是單一執行緒執行時間，所以適用于 Python 的主控制項實例一次只能處理一個函式呼叫。 對於處理大量 i/o 事件和/或 i/o 系結的應用程式，您可以透過非同步方式執行函式來改善效能。

若要以非同步方式執行函式，請使用 `async def` 語句，這會直接以[asyncio](https://docs.python.org/3/library/asyncio.html)執行函數：

```python
async def main():
    await some_nonblocking_socket_io_op()
```

沒有 `async` 關鍵字的函式會在 asyncio 執行緒集區中自動執行：

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>使用多個語言工作者進程

根據預設，每個函式主控制項實例都有單一的語言工作者進程。 您可以使用 [ [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) ] 應用程式設定，增加每部主機的工作者進程數（最多10個）。 Azure Functions 接著會嘗試在這些背景工作中平均散發並行函式呼叫。 

FUNCTIONS_WORKER_PROCESS_COUNT 適用于在相應放大應用程式以符合需求時所建立的每個主機。 

## <a name="context"></a>內容

若要在執行期間取得函數的調用內容，請在其簽章中包含[`context`](/python/api/azure-functions/azure.functions.context?view=azure-python)引數。 

例如，

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**內容**](/python/api/azure-functions/azure.functions.context?view=azure-python)類具有下列字串屬性：

`function_directory`  
函式執行所在的目錄。

`function_name`  
函式的名稱。

`invocation_id`  
目前函式引動的識別碼。

## <a name="global-variables"></a>全域變數

不保證會保留您應用程式的狀態，以供未來執行之用。 不過，Azure Functions 執行時間通常會針對相同應用程式的多個執行重複使用相同的進程。 為了快取昂貴計算的結果，請將它宣告為全域變數。 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>環境變數

在函數中，[應用程式設定](functions-app-settings.md)（例如服務連接字串）在執行期間會公開為環境變數。 您可以藉由宣告 `import os`，然後使用 `setting = os.environ["setting-name"]`，來存取這些設定。

下列範例會取得[應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)，並將金鑰命名為 `myAppSetting`：

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

就本機開發而言，應用程式設定會[保留在本機的設定 json 檔案中](functions-run-local.md#local-settings-file)。  

## <a name="python-version"></a>Python 版本 

目前，Azure Functions 支援 Python 3.6. x 和 3.7. x （官方 CPython 散發套件）。 在本機執行時，執行時間會使用可用的 Python 版本。 若要在 Azure 中建立函數應用程式時要求特定的 Python 版本，請使用[`az functionapp create`](/cli/azure/functionapp#az-functionapp-create)命令的 `--runtime-version` 選項。 只有在函數應用程式建立時，才允許版本變更。  

## <a name="package-management"></a>套件管理

使用 Azure Functions Core Tools 或 Visual Studio Code 在本機進行開發時，將所需的套件名稱和版本新增到 `requirements.txt` 檔案，並使用 `pip` 進行安裝。 

例如，可以使用下列要求和 pip 命令從 PyPl 安裝 `requests` 套件。

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>發行到 Azure

當您準備好要發行時，請確定所有公開可用的相依性都列在 [需求 .txt] 檔案中，該檔案位於專案目錄的根目錄中。 

從發行排除的專案檔和資料夾（包括虛擬環境資料夾）會列在 funcignore 檔案中。

有三個組建動作支援將 Python 專案發行至 Azure：

+ 遠端組建：根據需求 .txt 檔案的內容，從遠端取得相依性。 「[遠端組建](functions-deployment-technologies.md#remote-build)」是建議的組建方法。 [遠端] 也是 Azure 工具的預設組建選項。 
+ 本機組建：根據需求 .txt 檔案的內容，在本機取得相依性。 
+ 自訂相依性：您的專案使用我們的工具無法公開使用的套件。 （需要 Docker）。

若要建立您的相依性，並使用持續傳遞（CD）系統發佈，請[使用 Azure Pipelines](functions-how-to-azure-devops.md)。

### <a name="remote-build"></a>遠端組建

根據預設，當您使用下列[func Azure functionapp publish](functions-run-local.md#publish)命令將 Python 專案發佈至 Azure 時，Azure Functions Core Tools 會要求遠端組建。 

```bash
func azure functionapp publish <APP_NAME>
```

在 Azure 中，請記得以您的函式應用程式名稱取代 `<APP_NAME>`。

[Visual Studio Code 的 Azure Functions 延伸](functions-create-first-function-vs-code.md#publish-the-project-to-azure)模組預設也會要求遠端組建。 

### <a name="local-build"></a>本機組建

您可以使用下列[func azure functionapp publish](functions-run-local.md#publish)命令，以本機組建發行，以防止執行遠端組建。 

```command
func azure functionapp publish <APP_NAME> --build local
```

在 Azure 中，請記得以您的函式應用程式名稱取代 `<APP_NAME>`。 

使用 [`--build local`] 選項，就會從需求 .txt 檔案讀取專案相依性，並在本機下載並安裝那些依存的套件。 專案檔和相依性會從您的本機電腦部署至 Azure。 這會導致將較大的部署套件上傳至 Azure。 如果基於某些原因，核心工具無法取得您的需求 .txt 檔案中的相依性，您必須使用 [自訂相依性] 選項來進行發佈。 

### <a name="custom-dependencies"></a>自訂相依性

如果您的專案使用我們的工具無法公開使用的套件，您可以將它們放在 \_\_應用程式\_\_/. python_packages 目錄中，讓它們可供您的應用程式使用。 在發佈之前，請執行下列命令以在本機安裝相依性：

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

使用自訂相依性時，您應該使用 [`--no-build` 發佈] 選項，因為您已經安裝相依性。  

```command
func azure functionapp publish <APP_NAME> --no-build
```

在 Azure 中，請記得以您的函式應用程式名稱取代 `<APP_NAME>`。

## <a name="unit-testing"></a>單元測試

以 Python 撰寫的函式可以使用標準測試架構來測試，就像其他 Python 程式碼一樣。 對於大部分的系結，您可以從 `azure.functions` 封裝建立適當類別的實例，以建立模擬輸入物件。 因為[`azure.functions`](https://pypi.org/project/azure-functions/)套件無法立即使用，請務必透過您的 `requirements.txt` 檔案加以安裝，如上面的[封裝管理](#package-management)一節中所述。 

例如，下列是 HTTP 觸發函式的模擬測試：

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# __app__/HttpTrigger/__init__.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

以下是另一個範例，其中包含已觸發佇列的函式：

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```
## <a name="temporary-files"></a>暫存檔案

`tempfile.gettempdir()` 方法會傳回在 Linux 上 `/tmp`的暫存資料夾。 您的應用程式可以使用此目錄來儲存您的函式在執行期間所產生及使用的暫存檔案。 

> [!IMPORTANT]
> 寫入臨時目錄的檔案不保證會在調用之間保存。 在相應放大期間，暫存檔案不會在實例之間共用。 

下列範例會在臨時目錄中建立名為的暫存檔案（`/tmp`）：

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

我們建議您將測試維護在與專案資料夾不同的資料夾中。 這可讓您不需要將測試程式碼與應用程式一起部署。 

## <a name="known-issues-and-faq"></a>已知問題和常見問題集

所有已知問題和功能要求則會使用 [GitHub 問題](https://github.com/Azure/azure-functions-python-worker/issues)清單追蹤。 如果您遇到問題，但在 GitHub 中卻找不到此問題，請開啟新的問題，並包含問題的詳細說明。

### <a name="cross-origin-resource-sharing"></a>跨原始資源共用

Azure Functions 支援跨原始來源資源分享（CORS）。 CORS 會[在入口網站中](functions-how-to-use-azure-function-app-settings.md#cors)以及透過[Azure CLI](/cli/azure/functionapp/cors)進行設定。 CORS 允許的原始來源清單適用于函數應用層級。 在啟用 CORS 的情況下，回應會包含 `Access-Control-Allow-Origin` 標頭。 如需詳細資訊，請參閱 [跨原始來源資源分享](functions-how-to-use-azure-function-app-settings.md#cors)(英文)。

Python 函式應用程式[目前不支援](https://github.com/Azure/azure-functions-python-worker/issues/444)允許的原始來源清單。 由於這項限制，您必須在 HTTP 函式中明確設定 `Access-Control-Allow-Origin` 標頭，如下列範例所示：

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

請確定您也更新了函數. json，以支援 OPTIONS HTTP 方法：

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Web 瀏覽器會使用這個 HTTP 方法來協調允許的原始來源清單。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure Functions 套件 API 檔](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
* [Blob 儲存體繫結](functions-bindings-storage-blob.md)
* [HTTP 和 Webhook 繫結](functions-bindings-http-webhook.md)
* [佇列儲存體繫結](functions-bindings-storage-queue.md)
* [計時器觸發程序](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
