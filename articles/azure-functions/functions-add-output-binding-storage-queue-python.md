---
title: 將 Azure 儲存體佇列繫結新增至您的 Python 函式
description: 使用輸出繫結來整合 Azure 儲存體佇列與 Python 函式。
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: 14a381d13da052fd67679ed17bbb6b6711f7a0e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715361"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>將 Azure 儲存體佇列繫結新增至您的 Python 函式

在本文中，您將整合 Azure 儲存體佇列與您在[建立 HTTP 觸發的 Python 函式](functions-create-first-function-python.md)中建立的函式和儲存體帳戶。 您可以使用*輸出繫結*將 HTTP 要求中的資料寫入至佇列中的訊息，以完成這項整合。 完成本文的作業，並不會在先前的快速入門收取的幾美分以外產生額外的費用。

## <a name="prerequisites"></a>Prerequisites

- 完成[建立 HTTP 觸發的 Python 函式](functions-create-first-function-python.md)快速入門。 如果您已在該文章結束時清除資源，請重新執行這些步驟以在 Azure 中重新建立函式應用程式，但將資源保留。

## <a name="retrieve-the-azure-storage-connection-string"></a>擷取 Azure 儲存體連接字串

在先前的快速入門中，當您在 Azure 中建立函式應用程式時，您也已建立儲存體帳戶。 此帳戶的連接字串會安全地儲存在 Azure 的應用程式設定中。 藉由將設定下載到 *local.settings.json* 檔案中，您可以在本機執行函式時，使用該連線寫入至相同帳戶中的儲存體佇列。 

1. 從專案的根目錄執行下列命令 (請將 `<app_name>` 取代為先前快速入門中的函式應用程式名稱)。 此命令將會覆寫檔案中任何現有的值。

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. 開啟 *local.settings.json* 並找出名為 `AzureWebJobsStorage` 的值，也就是儲存體帳戶連接字串。 您會在本文的其他章節中使用名稱 `AzureWebJobsStorage` 和連接字串。

> [!IMPORTANT]
> 由於 *local.settings.json* 中包含從 Azure 下載的祕密，因此請一律將此檔案排除在原始檔控制以外。 使用本機函式專案建立的 *.gitignore* 檔案依預設會排除該檔案。

## <a name="add-an-output-binding-to-functionjson"></a>將輸出繫結新增至 function.json

一個函式只能有一個觸發程序，但可以有多個輸入和輸出繫結，如此，您無須撰寫自訂整合程式碼，即可連線至其他 Azure 服務和資源。 您可以根據用於函式的語言，適當地在函式資料夾的 *function.json* 檔案中宣告這些繫結。

在先前的快速入門中，*HttpExample* 資料夾中的 *function.json* 檔案包含 `bindings` 集合中的兩個繫結：

```json
{
  "scriptFile": "__init__.py",
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

每個繫結至少有一個類型、一個方向和一個名稱。 在上述範例中，第一個繫結的類型為 `httpTrigger`、方向為 `in`。 針對 `in` 方向，`name` 會指定觸發程序叫用函式時所傳入的輸入參數名稱。

第二個繫結的類型為 `http`、方向為 `out`，在此情況下，`$return` 的特殊 `name` 表示此繫結會使用函式的傳回值，而不是提供輸入參數。

若要從這個函式寫入至 Azure 儲存體佇列，請新增類型為 `queue`、名稱為 `msg` 的 `out` 繫結，如下列程式碼所示：

```json
{
  "scriptFile": "__init__.py",
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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

在此案例下，會將 `msg` 提供給函式作為輸出引數。 針對 `queue` 類型，您也必須在 `queueName` 中指定佇列的名稱，並在 `connection` 中提供 Azure 儲存體連線 (來自 *local.settings.json*) 的*名稱*。

如需繫結的詳細資訊，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)與[佇列輸出設定](functions-bindings-storage-queue.md#output---configuration)。

## <a name="add-code-to-use-the-output-binding"></a>新增程式碼以使用輸出繫結

在 *function.json* 中指定佇列繫結之後，您現在可以更新函式以接收 `msg` 輸出參數，並將訊息寫入至佇列。

將 `msg` 參數新增至函式定義和 `msg.set(name)` 陳述式下的 `if name:`，以更新 *HttpExample\\\_\_init\_\_.py*，使其符合下列程式碼。

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

`msg` 參數是 [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) 的執行個體。 其 `set` 方法會將字串訊息寫入至佇列，在此案例中為傳至 URL 查詢字串中所含函式的名稱。

請留意，您*無須*撰寫任何程式碼來進行驗證、取得佇列參考或寫入資料。 這些整合工作全都可在 Azure Functions 執行階段和佇列輸出繫結中輕易處理。

## <a name="run-and-test-the-function-locally"></a>在本機執行和測試函式

1. 在終端機或命令提示字元中，瀏覽至您的函式專案資料夾 *LocalFunctionProj*。

1. 使用下列命令啟動 Azure Functions 執行階段主機。

    ```
    func host start
    ```

1. 在啟動完成，且您看到 `HttpExample` 端點的 URL 後，請將其 URL 複製到瀏覽器，並附加查詢字串 `?name=<your-name>`，使完整的 URL 呈現為 `http://localhost:7071/api/HttpExample?name=Guido`。 瀏覽器應該會顯示類似於 `Hello Guido` 的訊息，如先前的文章所示。

    若未看到 `HttpExample` 端點，請使用 **Ctrl**+**C** 停止主機，並檢查輸出中是否有錯誤。 例如，如果 *function.json* 中有錯誤，主機就不會啟用端點。 同時請確認您是從函式專案資料夾執行 `func host start`，而不是 *HttpExample* 資料夾。

1. 完成作業後，請使用 **Ctrl**+**C** 停止主機。

> [!TIP]
> 在啟動期間，主機會下載並安裝[儲存體繫結延伸模組](functions-bindings-storage-blob.md#packages---functions-2x-and-higher)和其他 Microsoft 繫結延伸模組。 之所以會執行此安裝，是因為具有下列屬性的 *host.json* 檔案依預設會啟用繫結延伸模組：
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> 如果發生任何與繫結延伸模組相關的錯誤，請檢查 *host.json* 中是否有上述屬性存在。

## <a name="view-the-message-in-the-azure-storage-queue"></a>檢視 Azure 儲存體佇列中的訊息

您的函式在產生網頁瀏覽器的 HTTP 回應時，也會同時呼叫 `msg.set(name)`，而將訊息寫入至名為 `outqueue` 的 Azure 儲存體佇列，如佇列繫結中所指定。 您可以在 [Azure 入口網站](../storage/queues/storage-quickstart-queues-portal.md)或 [Microsoft Azure 儲存體總管](https://storageexplorer.com/)中檢視佇列。 您也可以在 Azure CLI 中檢視佇列，如下列步驟所說明：

1. 開啟函式專案的 *local.setting.json* 檔案，並複製連接字串值。 在終端機或命令視窗中，執行下列命令 (請貼上您的特定連接字串以取代 `<connection_string>`)，以建立名為 `AZURE_STORAGE_CONNECTION_STRING`的環境變數。 (此環境變數意味著您無須使用 `--connection-string` 引數將連接字串提供給每個後續命令。)

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (選擇性) 使用 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 命令檢視您帳戶中的儲存體佇列。 此命令的輸出應該會包含名為 `outqueue` 的佇列，這是函式將其第一個訊息寫入至該佇列時所建立的。
    
    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. 使用 [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) 命令檢視此佇列中的訊息，這應該是您先前測試函式時所使用的名字。 此命令會以 [base64 編碼](functions-bindings-storage-queue.md#encoding)擷取佇列中的第一個訊息，因此您也必須將訊息解碼，以便以文字格式檢視。

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    由於您必須從 base64 對訊息集合取值並解碼，請執行 PowerShell 並使用 PowerShell 命令。

    ---
    
## <a name="redeploy-the-project-to-azure"></a>將專案重新部署至 Azure

現在，您已在本機測試函式，並確認該函式已將訊息寫入至 Azure 儲存體佇列，接下來您可以重新部署專案，以更新在 Azure 上執行的端點。

1. 在 *LocalFunctionsProj* 資料夾中，使用 [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) 命令來重新部署專案 (請將 `<app_name>` 取代為您的應用程式名稱)。

    ```
    func azure functionapp publish <app_name>
    ```
    
1. 如先前的快速入門所示，使用瀏覽器或 CURL 來測試已重新部署的函式。

    # <a name="browsertabbrowser"></a>[瀏覽器](#tab/browser)
    
    將發佈命令的輸出中顯示的完整**叫用 URL** 複製到瀏覽器網址列中 (請附加查詢參數 `&name=Azure`)。 瀏覽器應該會顯示與您在本機執行函式時類似的輸出。

    ![使用瀏覽器在 Azure 上執行函式的輸出](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curltabcurl"></a>[curl](#tab/curl)
    
    使用**叫用 URL** 來執行 [curl](https://curl.haxx.se/) (請附加參數 `&name=Azure`)。 命令的輸出應該是文字 "Hello Azure"。
    
    ![使用 curl 在 Azure 上執行函式的輸出](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. 再次檢查儲存體佇列 (如上一節所述)，以確認其中包含寫入至佇列的新訊息。

    如果您使用 Azure CLI 來檢查佇列，則 `az storage message peek` 命令只會顯示佇列中的第一個訊息。 若要模擬處理訊息，請改用 `az storage message get` 並搭配所有相同的引數。 `get` 命令會傳回訊息，並將其從佇列中移除。 接著，您可以重複相同的命令，直到佇列變空為止 (且命令會產生錯誤)。

## <a name="clean-up-resources"></a>清除資源

如果您繼續進行下一個步驟 ([啟用 Application Insights 整合](functions-monitoring.md#manually-connect-an-app-insights-resource))，請保留您所有的資源，因為在後續的工作還會用到。

否則，請使用下列命令刪除資源群組及其包含的所有資源，以避免產生額外的成本。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [啟用 Application Insights 與 Azure Functions 應用程式的整合](functions-monitoring.md#manually-connect-an-app-insights-resource)

其他資源：

- [Python 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=python)。
- [Azure Functions Python 開發人員指南](functions-reference-python.md)
- [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)。
- [Functions 定價頁面](https://azure.microsoft.com/pricing/details/functions/)
- [估計使用方案成本](functions-consumption-costs.md)文章。
