---
title: 適用於 Azure Functions 的 Python 開發人員參考
description: 了解如何使用 Python 開發函式
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 30f40db33b6aa8b40202c023f301265565257180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276682"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 開發人員指南

本文是使用 Python 開發 Azure Functions 的簡介。 本文假設您已閱讀過下列 [Azure Functions 開發人員指南](functions-reference.md)。 

有關 Python 中的獨立函數示例專案，請參閱[Python 函數示例](/samples/browse/?products=azure-functions&languages=python)。 

## <a name="programming-model"></a>程式設計模型

Azure 函數期望函數是 Python 腳本中處理輸入並生成輸出的無狀態方法。 預設情況下，運行時期望該方法作為在檔中調用`main()`的`__init__.py`全域方法實現。 您還可以[指定備用進入點](#alternate-entry-point)。

觸發器和綁定中的資料通過使用`name`*函數.json*檔中定義的屬性通過方法屬性綁定到函數。 例如，下面的_函數.json_描述了由名為 的`req`HTTP 要求觸發的簡單函數：

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

基於此定義，`__init__.py`包含函數代碼的檔可能類似于以下示例：

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

您還可以使用 Python 類型注釋顯式聲明函數中的屬性類型和返回類型。 這有助於您使用許多 Python 代碼編輯器提供的感知和自動完成功能。

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

請使用 [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 套件中所包含的 Python 註釋，以將輸入和輸出繫結至方法。

## <a name="alternate-entry-point"></a>備用進入點

您可以通過有選擇地指定*函數.json*檔中 的 和`scriptFile``entryPoint`屬性來更改函數的預設行為。 例如，下面的_函數.json_告訴運行時在`customentry()`_main.py_檔中使用 方法，作為 Azure 函數的進入點。

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

Python 函數專案的建議資料夾結構如下所示：

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 tests
```
主專案資料夾 （\_\_\_\_應用程式 ） 可以包含以下檔：

* *本地.settings.json*：用於在本地運行時存儲應用設置和連接字串。 此檔案不會發行至 Azure。 要瞭解更多資訊，請參閱[本地設置.file](functions-run-local.md#local-settings-file)。
* *要求.txt*： 包含系統發佈到 Azure 時安裝的包的清單。
* *host.json*： 包含影響函數應用中所有函數的全域配置選項。 此檔案會發行至 Azure。 在本地運行時，並非所有選項都受支援。 要瞭解更多資訊，請參閱[host.json](functions-host-json.md)。
* *.funcignore*：（可選）聲明不應發佈到 Azure 的檔。
* *.gitignore*：（可選）聲明從 git 存儲庫中排除的檔，如 local.settings.json。

每個函式都具有本身的程式碼檔案和繫結設定檔 (function.json)。 

將專案部署到 Azure 中的函數應用時，主專案*\_\_（app\_*） 資料夾的全部內容應包含在包中，而不是資料夾本身。 我們建議您在獨立于專案資料夾的資料夾中維護測試，在此示例中`tests`。 這樣，您就無法將測試代碼與應用一起部署。 有關詳細資訊，請參閱[單元測試](#unit-testing)。

## <a name="import-behavior"></a>匯入行為

您可以使用顯式相對參照和絕對引用在函數代碼中導入模組。 根據上面顯示的資料夾結構，以下導入工作從函數檔*\_\_\_\_應用 [\_我的第一個\_函數\\]\_init\_\_.py*：

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

以下導入無法在同一檔中*工作*：

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

共用代碼應保存在*\_\_應用中\_* 的單獨資料夾中。 要引用*共用\_代碼*資料夾中的模組，可以使用以下語法：

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>觸發器和輸入

在 Azure Functions 中輸入會分成兩個類別：觸發程序輸入和額外的輸入。 儘管它們在`function.json`檔中不同，但在 Python 代碼中，用法是相同的。  觸發器和輸入源的連接字串或機密映射到在本地運行時`local.settings.json`檔中的值，以及在 Azure 中運行時的應用程式設定。 

例如，以下代碼演示了兩者之間的區別：

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

叫用此函式時，HTTP 要求會以 `req` 形式傳遞至函式。 將根據路由 URL 中的_ID_從 Azure Blob 存儲中檢索條目，並在函數體`obj`中提供。  此處指定的存儲帳戶是在 AzureWebJobs 存儲應用設置中找到的連接字串，該設置與函數應用使用的存儲帳戶相同。


## <a name="outputs"></a>輸出

輸出可以使用傳回值和輸出參數來表示。 如果只有一個輸出，我們建議使用傳回的值。 若為多個輸出，您必須使用輸出參數。

若要使用函式的傳回值作為輸出繫結的值，應該將 `function.json` 中的繫結 `name` 屬性設定為 `$return`。

要生成多個輸出，`set()`請使用[`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python)介面提供的方法為綁定分配值。 例如，下列函式可以將訊息推送至佇列，同時也會傳回 HTTP 回應。

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

可通過函數應用中的根[`logging`](https://docs.python.org/3/library/logging.html#module-logging)處理常式訪問 Azure 函數運行時記錄器。 這個記錄器會繫結至 Application Insights，並可讓您將函式執行期間遇到的警告和錯誤加上旗標。

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

要瞭解有關日誌記錄的更多詳細資訊，請參閱[監視 Azure 函數](functions-monitoring.md)。

## <a name="http-trigger-and-bindings"></a>HTTP 觸發器和綁定

HTTP 觸發器在函數.jon 檔中定義。 綁定`name`的 必須與函數中的具名引數匹配。 在前面的示例中，使用綁定名稱`req`。 此參數是[HttpRequest]物件，並返回[HttpResponse]物件。

從[HttpRequest]物件中，可以獲取請求標頭、查詢參數、路由參數和郵件內文。 

下面的示例來自 Python 的[HTTP 觸發器範本](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)。 

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

在此函數中`name`，查詢參數的值從`params`[HttpRequest]物件的參數中獲取。 使用`get_json`方法讀取 JSON 編碼的郵件內文。 

同樣，您可以在返回的`status_code``headers`[HttpResponse]物件中設置 和 回應訊息。

## <a name="scaling-and-concurrency"></a>縮放和併發

預設情況下，Azure 函數會自動監視應用程式的負載，並根據需要為 Python 創建其他主機實例。 函數使用不同觸發器類型的內置（不可使用者可配置）閾值來確定何時添加實例，例如佇列觸發器的消息和佇列大小。 有關詳細資訊，請參閱[消費和高級計畫的工作原理](functions-scale.md#how-the-consumption-and-premium-plans-work)。

此縮放行為對於許多應用程式來說就足夠了。 但是，具有以下任何特徵的應用程式可能無法有效地擴展：

- 應用程式需要處理許多併發調用。
- 應用程式處理大量 I/O 事件。
- 應用程式受 I/O 約束。

在這種情況下，您可以通過採用非同步模式和使用多語言輔助角色進程來進一步提高性能。

### <a name="async"></a>非同步處理

由於 Python 是單線程運行時，因此 Python 的主機實例一次只能處理一個函式呼叫。 對於處理大量 I/O 事件和/或受 I/O 綁定的應用程式，可以通過非同步運行函數來提高性能。

要非同步地運行函數，請使用 語句`async def`，該語句直接以[非同步方式](https://docs.python.org/3/library/asyncio.html)運行該函數：

```python
async def main():
    await some_nonblocking_socket_io_op()
```

沒有關鍵字的`async`函數在非同步執行緒池中自動運行：

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>使用多種語言輔助角色進程

預設情況下，每個函數主機實例都有一個語言輔助進程。 通過使用[FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count)應用程式設定，可以增加每個主機的工作進程數（最多 10 個）。 然後，Azure 函數嘗試在這些輔助人員之間均勻地分配同時調用函數。 

FUNCTIONS_WORKER_PROCESS_COUNT適用于函數在擴展應用程式以滿足需求時創建的每個主機。 

## <a name="context"></a>Context

要在執行期間獲取函數的調用上下文，請在[`context`](/python/api/azure-functions/azure.functions.context?view=azure-python)函數的簽名中包括參數。 

例如：

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**上下文**](/python/api/azure-functions/azure.functions.context?view=azure-python)類具有以下字串屬性：

`function_directory`  
函式執行所在的目錄。

`function_name`  
函數的名稱。

`invocation_id`  
目前函式引動的識別碼。

## <a name="global-variables"></a>全域變數

不能保證應用的狀態將保留，以便將來執行。 但是，Azure 函數運行時通常為同一應用的多次執行重用同一進程。 為了緩存昂貴的計算結果，將其聲明為全域變數。 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>環境變數

在函數中，[應用程式設定](functions-app-settings.md)（如服務連接字串）在執行期間作為環境變數公開。 您可以通過聲明`import os`然後使用 訪問這些設置。 `setting = os.environ["setting-name"]`

下面的示例獲取[應用程式設定](functions-how-to-use-azure-function-app-settings.md#settings)，鍵名為`myAppSetting`：

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

對於本地開發，應用程式設定[保存在本地.settings.json 檔中](functions-run-local.md#local-settings-file)。  

## <a name="python-version"></a>Python 版本 

Azure 函數支援以下 Python 版本：

| Functions 版本 | Python<sup>*</sup>版本 |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>官方 CPython 發行版本

要在 Azure 中創建函數應用時請求特定的 Python 版本，`--runtime-version`請使用 命令[`az functionapp create`](/cli/azure/functionapp#az-functionapp-create)的選項。 函數執行階段版本由 選項`--functions-version`設置。 在創建函數應用時設置 Python 版本，無法更改。  

在本地運行時，運行時使用可用的 Python 版本。 

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

準備好發佈後，請確保所有公開可用的依賴項都列在需求.txt 檔中，該檔位於專案目錄的根目錄中。 

從發佈中排除的專案檔案和資料夾（包括虛擬環境資料夾）列在 .funcignore 檔中。

支援將 Python 專案發佈到 Azure 的三個生成操作：

+ 遠端生成：根據需求.txt 檔的內容遠端獲取依賴項。 [遠端生成](functions-deployment-technologies.md#remote-build)是推薦的生成方法。 遠端也是 Azure 工具的預設生成選項。 
+ 本地生成：根據需求.txt 檔的內容在本地獲取依賴項。 
+ 自訂依賴項：您的專案使用我們的工具未公開的包。 （需要 Docker。

要生成依賴項並使用連續傳遞 （CD） 系統發佈，[請使用 Azure 管道](functions-how-to-azure-devops.md)。

### <a name="remote-build"></a>遠端生成

預設情況下，當您使用以下[func Azure 函數應用發佈](functions-run-local.md#publish)命令將 Python 專案發佈到 Azure 時，Azure 函數核心工具會請求遠端生成。 

```bash
func azure functionapp publish <APP_NAME>
```

在 Azure 中，請記得以您的函式應用程式名稱取代 `<APP_NAME>`。

預設情況下，[視覺化工作室代碼的 Azure 函數擴展](functions-create-first-function-vs-code.md#publish-the-project-to-azure)也會請求遠端生成。 

### <a name="local-build"></a>本地生成

可以使用以下[func azure 函數應用發佈](functions-run-local.md#publish)命令使用本地生成進行發佈，從而防止執行遠端生成。 

```command
func azure functionapp publish <APP_NAME> --build local
```

在 Azure 中，請記得以您的函式應用程式名稱取代 `<APP_NAME>`。 

使用`--build local`選項，將從需求.txt 檔中讀取專案依賴項，並將這些從屬包下載並安裝在本地。 專案檔案和依賴項從本地電腦部署到 Azure。 這將導致將更大的部署包上載到 Azure。 如果由於某種原因，核心工具無法獲取需求.txt 檔中的依賴項，則必須使用自訂依賴項選項進行發佈。 

### <a name="custom-dependencies"></a>自訂依賴項

如果您的專案使用我們的工具未公開的包，\_\_您可以通過將它們放在應用\_\_/.python_packages 目錄中，使其可供應用使用。 在發佈之前，運行以下命令以在本地安裝依賴項：

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

使用自訂依賴項時，應使用`--no-build`發佈選項，因為已安裝依賴項。  

```command
func azure functionapp publish <APP_NAME> --no-build
```

在 Azure 中，請記得以您的函式應用程式名稱取代 `<APP_NAME>`。

## <a name="unit-testing"></a>單元測試

使用標準測試框架可以像其他 Python 代碼一樣測試在 Python 中編寫的函數。 對於大多數綁定，可以通過從`azure.functions`包創建適當類的實例來創建類比輸入物件。 由於[`azure.functions`](https://pypi.org/project/azure-functions/)包不能立即可用，請務必通過檔`requirements.txt`安裝它，如上面[的包管理](#package-management)部分所述。 

例如，以下是 HTTP 觸發函數的類比測試：

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

下面是另一個示例，具有佇列觸發函數：

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
## <a name="temporary-files"></a>[暫存檔案]

該方法`tempfile.gettempdir()`返回一個暫存檔案夾，該資料夾在 Linux`/tmp`上是 。 應用程式可以使用此目錄存放函數在執行期間生成和使用的暫存檔案。 

> [!IMPORTANT]
> 寫入臨時目錄的檔不能保證在調用之間保留。 在橫向擴展期間，暫存檔案不會在實例之間共用。 

下面的示例在臨時目錄中創建一個命名的暫存檔案 （`/tmp`：

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

我們建議您將測試維護在獨立于專案資料夾的資料夾中。 這樣，您就無法將測試代碼與應用一起部署。 

## <a name="known-issues-and-faq"></a>已知問題和常見問題集

所有已知問題和功能要求則會使用 [GitHub 問題](https://github.com/Azure/azure-functions-python-worker/issues)清單追蹤。 如果您遇到問題，但在 GitHub 中卻找不到此問題，請開啟新的問題，並包含問題的詳細說明。

### <a name="cross-origin-resource-sharing"></a>跨原始資源共用

Azure 函數支援跨源資源分享 （CORS）。 CORS[在門戶中](functions-how-to-use-azure-function-app-settings.md#cors)或通過 Azure [CLI](/cli/azure/functionapp/cors)進行配置。 CORS 允許的源清單適用于函數應用級別。 啟用 CORS 後，回應`Access-Control-Allow-Origin`將包含標頭。 如需詳細資訊，請參閱 [跨原始來源資源分享](functions-how-to-use-azure-function-app-settings.md#cors)(英文)。

Python 函數應用[當前不支援](https://github.com/Azure/azure-functions-python-worker/issues/444)允許的源清單。 由於此限制，您必須在 HTTP 函數`Access-Control-Allow-Origin`中明確設置標頭，如以下示例所示：

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

請確保還更新函數.json 以支援 OPTIONS HTTP 方法：

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Web 瀏覽器使用此 HTTP 方法協商允許的源清單。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Azure 函數包 API 文檔](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions 的最佳做法](functions-best-practices.md)
* [Azure 函數觸發器和綁定](functions-triggers-bindings.md)
* [Blob 儲存體繫結](functions-bindings-storage-blob.md)
* [HTTP 和 Webhook 繫結](functions-bindings-http-webhook.md)
* [佇列儲存體繫結](functions-bindings-storage-queue.md)
* [計時器觸發程序](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
