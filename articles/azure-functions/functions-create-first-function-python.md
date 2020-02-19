---
title: 在 Azure Functions 中建立適用於 HTTP 要求的無伺服器 Python 函式
description: 使用 Azure Functions 建立無伺服器 Python 程式碼並部署至雲端。
ms.date: 02/11/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: a781e10cee4cf433de5e837490d901020a875205
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157885"
---
# <a name="quickstart-create-a-python-function-in-azure-that-responds-to-http-requests"></a>快速入門：在 Azure 中建立可回應 HTTP 要求的 Python 函式

在本文中，您會使用命令列工具建立可回應 HTTP 要求的 Python 函式。 在本機測試程式碼之後，您可以將其部署到 Azure Functions 的無伺服器環境。 完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。

這也是本文的 [Visual Studio Code 版本](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-python)。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](./functions-run-local.md#v2) 2.7.1846 版或更新版本。
- [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 版或更新版本。 
- [Python 3.7.4 - 64 位元](https://www.python.org/downloads/release/python-374/)。 (Python 3.7.4 已進行 Azure Functions 的驗證；Python 3.8 和更新版本尚不受支援。)

### <a name="prerequisite-check"></a>先決條件檢查

1. 在終端機或命令視窗中，執行 `func --version`，以確認 Azure Functions Core Tools 為 2.7.1846 版或更新版本。
1. 執行 `az --version` 以檢查 Azure CLI 版本為 2.0.76 或更新版本。
1. 執行 `az login` 以登入 Azure 並驗證有效訂用帳戶。
1. 執行 `python --version` (Linux/MacOS) 或 `py --version` (Windows)，以確認您的 Python 版本回報為 3.7.x。

## <a name="create-and-activate-a-virtual-environment"></a>建立並啟用虛擬環境

在適用的資料夾中執行下列命令，以建立並啟用名為 `.venv` 的虛擬環境。 請務必使用受 Azure Functions 支援的 Python 3.7。


# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

如果 Python 未在您的 Linux 發行版本上安裝 venv 套件，請執行下列命令：

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

您將在這個已啟用的虛擬環境中執行所有後續命令。 (若要退出虛擬環境，請執行 `deactivate`。)

## <a name="create-a-local-function-project"></a>建立本機函式專案

在 Azure Functions 中，函式專案是包含一或多個個別函式的容器，而每個函式分別會回應特定的觸發程序。 專案中的所有函式會共用相同的本機和裝載設定。 在本節中，您將建立包含單一函式的函式專案。

1. 在虛擬環境中執行 `func init` 命令，以使用指定的執行階段在名為 *LocalFunctionProj* 的資料夾中建立函式專案：

    ```
    func init LocalFunctionProj --python
    ```
    
    此資料夾會包含專案的各種檔案，包括名為 [local.settings.json](functions-run-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的組態檔。 由於 *local.settings.json* 可能會包含從 Azure 下載的秘密，因此 *.gitignore* 檔案依預設會將該檔案排除在原始檔控制以外。

    > [!TIP]
    > 由於函式專案會繫結至特定執行階段，因此專案中的所有函式都必須以相同的語言撰寫。

1. 瀏覽至專案資料夾：

    ```
    cd LocalFunctionProj
    ```
    
1. 使用下列命令，將函式新增至您的專案，其中 `--name` 引數是函式的唯一名稱，而 `--template` 引數可指定函式的觸發程序。 `func new` 建立符合函式名稱的子資料夾，其中包含適合專案所選語言的程式碼檔案，以及名為 *function.json* 的組態檔。

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>(選擇性) 檢查檔案內容

如有需要，您可以跳到[在本機執行函式](#run-the-function-locally)，並於稍後再檢查檔案內容。

#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* 包含 `main()` Python 函式，此函式會根據 *function.json* 中的設定而觸發。

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
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
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

針對 HTTP 觸發程序，函式會接收變數 `req` 中的要求資料，如 *function.json* 中所定義。 `req` 是 [azure.functions.HttpRequest 類別](/python/api/azure-functions/azure.functions.httprequest)的執行個體。 傳回物件 (在 function.json  中定義為 `$return`)，是 [azure.functions.HttpResponse 類別](/python/api/azure-functions/azure.functions.httpresponse)的執行個體。 若要深入了解，請參閱 [Azure Functions HTTP 觸發程序和繫結](functions-bindings-http-webhook.md)。

#### <a name="functionjson"></a>function.json

*function.json* 是一個組態檔，會定義函式的輸入和輸出 `bindings`，包括觸發程序類型。 如有需要，您可以變更 `scriptFile` 以叫用不同的 Python 檔案。

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

每個繫結都需要方向、類型和唯一名稱。 HTTP 觸發程序具有 [`httpTrigger`](functions-bindings-http-webhook.md#trigger) 類型的輸入繫結，和 [`http`](functions-bindings-http-webhook.md#output) 類型的輸出繫結。


## <a name="run-the-function-locally"></a>在本機執行函式

啟動 *LocalFunctionProj* 資料夾中的本機 Azure Functions 執行階段主機，以啟動函式：

```
func start
```

此時應該會出現下列輸出。 (如果 HttpExample 未顯示如下，表示您可能從 *HttpExample* 資料夾內啟動了主機。 在此情況下，請使用 **Ctrl**+**C** 停止主機，並瀏覽至上層 *LocalFunctionProj* 資料夾，然後重新執行 `func start`。)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

從這個輸出中將 `HttpExample` 函式的 URL 複製到瀏覽器，並附加查詢字串 `?name=<your-name>`，使其成為完整的 URL (如 `http://localhost:7071/api/HttpExample?name=Functions`)。 瀏覽器應該會顯示類似於 `Hello Functions` 的訊息：

![在本機瀏覽器中執行函式的結果](./media/functions-create-first-function-python/function-test-local-browser.png)

您在其中執行 `func start` 的終端機，也會在您提出要求時顯示記錄輸出。

在準備就緒後，請按 **Ctrl**+**C** 以停止函式主機。

## <a name="create-supporting-azure-resources-for-your-function"></a>為您的函式建立支援的 Azure 資源

若要將函式程式碼部署至 Azure，您必須先建立三個資源：

- 資源群組，這是相關資源的邏輯容器。
- Azure 儲存體帳戶，其可維護專案的狀態和其他資訊。
- Azure 函式應用程式，其可提供環境來執行函式程式碼。 函式應用程式可對應至您的本機函式專案，並可讓您將函式分組為邏輯單位，以便管理、部署和共用資源。

您可以使用 Azure CLI 命令來建立這些項目。 每個命令都會在完成時提供 JSON 輸出。

1. 如果您尚未執行此作業，請使用 [az login](/cli/azure/reference-index#az-login) 命令登入 Azure：

    ```azurecli
    az login
    ```
    
1. 使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 下列範例會在 `westeurope` 區域建立名為 `AzureFunctionsQuickstart-rg` 的資源群組。 (您通常會使用來自 `az account list-locations` 命令的可用區域，在您附近的區域中建立資源群組和資源。)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > 您無法在相同的資源群組中裝載 Linux 和 Windows 應用程式。 如果您有名為 `AzureFunctionsQuickstart-rg` 的現有資源群組，且其中包含 Windows 函式應用程式或 Web 應用程式，則必須使用不同的資源群組。
    
1. 使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令，在您的資源群組和區域中建立一般用途的儲存體帳戶。 在下列範例中，使用適合您的全域唯一名稱取代 `<storage_name>`。 名稱只能包含 3 到 24 個字元的數字和小寫字母。 `Standard_LRS` 可指定典型的一般用途帳戶。

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    在本快速入門中，儲存體帳戶只會產生幾美分的費用。
    
1. 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令來建立 Functions 應用程式。 在下列範例中，使用您在上一個步驟中所用的帳戶名稱取代 `<storage_name>`，並使用適合您的全域唯一名稱取代 `<app_name>`。 `<app_name>` 也是函式應用程式的預設 DNS 網域。

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    此命令會依據 [Azure Functions 使用方案](functions-scale.md#consumption-plan)，建立執行指定語言執行階段的函式應用程式，而此應用程式在此處產生的使用量是免費的。 此命令也會在相同的資源群組中佈建相關聯的 Azure Application Insights 執行個體，您可將其用於監視函式應用程式和檢視記錄。 如需詳細資訊，請參閱[監視 Azure Functions](functions-monitoring.md)。 在您啟用此執行個體之前，並不會產生任何成本。
    
## <a name="deploy-the-function-project-to-azure"></a>將函式專案部署至 Azure

備妥必要的資源後，您就可以開始使用 [func azure functionapp publish](functions-run-local.md#project-file-deployment) 命令，將您的本機函式專案部署至 Azure 中的函式應用程式。 在下列範例中，請將 `<app_name>` 取代為您的應用程式名稱。

```
func azure functionapp publish <app_name>
```

如果您看到錯誤：「找不到具有該名稱的應用程式」，請稍等幾秒再重試，因為 Azure 在上一個 `az functionapp create` 命令之後可能尚未完全初始化應用程式。

發佈命令會顯示類似於下列輸出的結果 (為了簡單起見已將其截斷)：

```output
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
```

## <a name="invoke-the-function-on-azure"></a>在 Azure 上叫用函式

由於您的函式會使用 HTTP 觸發程序，因此您在叫用函式時，可以在瀏覽器中對其 URL 提出 HTTP 要求，或使用 curl 之類的工具。 無論採用何種方式，`code` URL 參數都會是您向函式端點授與叫用權限的唯一函式金鑰。

# <a name="browser"></a>[瀏覽器](#tab/browser)

將發佈命令的輸出中顯示的完整**叫用 URL** 複製到瀏覽器網址列中 (請附加查詢參數 `&name=Azure`)。 瀏覽器應該會顯示與您在本機執行函式時類似的輸出。

![使用瀏覽器在 Azure 上執行函式的輸出](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curl"></a>[curl](#tab/curl)

使用**叫用 URL** 來執行 [curl](https://curl.haxx.se/) (請附加參數 `&name=Azure`)。 命令的輸出應該是文字 "Hello Azure"。

![使用 curl 在 Azure 上執行函式的輸出](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> 若要針對已發佈的 Python 應用程式檢視近乎即時的記錄，請使用 [Application Insights 即時計量資料流](functions-monitoring.md#streaming-logs)。

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個步驟[新增 Azure 儲存體佇列輸出繫結](functions-add-output-binding-storage-queue-python.md)，請保留您所有的資源，因為在後續的工作還會用到。

否則，請使用下列命令刪除資源群組及其包含的所有資源，以避免產生額外的成本。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增 Azure 儲存體佇列輸出繫結](functions-add-output-binding-storage-queue-python.md)
