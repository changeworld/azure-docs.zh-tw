---
title: 從命令列建立 Python 函式 - Azure Functions
description: 了解如何從命令列建立 Python 函式，然後將本機專案發佈至 Azure Functions 中的無伺服器裝載。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 5f8bf6fb6f464bcb74d34e2d99fac16c24b12615
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175707"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>快速入門：從命令列在 Azure 中建立 Python 函式

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

在本文中，您會使用命令列工具建立可回應 HTTP 要求的 Python 函式。 在本機測試程式碼之後，您可以將其部署到 Azure Functions 的無伺服器環境。

完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。

這也是本文的 [Visual Studio Code 版本](create-first-function-vs-code-python.md)。

## <a name="configure-your-local-environment"></a>設定您的本機環境

開始之前，您必須具備下列條件：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.x 版。
  
+ 下列其中一項用來建立 Azure 資源的工具：

    + [Azure CLI](/cli/azure/install-azure-cli) 2.4 版或更新版本。

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5.0 版或更新版本。

+ [Python 3.8 (64 位元)](https://www.python.org/downloads/release/python-382/)、[Python 3.7 (64 位元)](https://www.python.org/downloads/release/python-375/)、[Python 3.6 (64 位元)](https://www.python.org/downloads/release/python-368/) (均受 3.x 版的 Azure Functions 支援)。

### <a name="prerequisite-check"></a>先決條件檢查

確認您的必要條件，這取決於您是使用 Azure CLI 還是 Azure PowerShell 來建立 Azure 資源：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ 在終端機或命令視窗中執行 `func --version`，以確認 Azure Functions Core Tools 為 3.x 版。

+ 執行 `az --version` 以確認 Azure CLI 版本為 2.4 或更新版本。

+ 執行 `az login` 以登入 Azure 並驗證有效訂用帳戶。

+ 執行 `python --version` (Linux/macOS) 或 `py --version` (Windows)，以確認您的 Python 版本回報為 3.8.x、3.7.x 或 3.6.x。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ 在終端機或命令視窗中執行 `func --version`，以確認 Azure Functions Core Tools 為 3.x 版。

+ 執行 `(Get-Module -ListAvailable Az).Version` 並確認為 5.0 版或更新版本。 

+ 執行 `Connect-AzAccount` 以登入 Azure 並驗證有效訂用帳戶。

+ 執行 `python --version` (Linux/macOS) 或 `py --version` (Windows)，以確認您的 Python 版本回報為 3.8.x、3.7.x 或 3.6.x。

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>建立並啟用虛擬環境

在適用的資料夾中執行下列命令，以建立並啟用名為 `.venv` 的虛擬環境。 請務必使用受 Azure Functions 支援的 Python 3.8、3.7 或 3.6。

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

您將在這個已啟用的虛擬環境中執行所有後續命令。 

## <a name="create-a-local-function-project"></a>建立本機函式專案

在 Azure Functions 中，函式專案是包含一或多個個別函式的容器，而每個函式分別會回應特定的觸發程序。 專案中的所有函式會共用相同的本機和裝載設定。 在本節中，您將建立包含單一函式的函式專案。

1. 執行 `func init` 命令，以使用指定的執行階段在名為 LocalFunctionProj 的資料夾中建立函式專案：  

    ```console
    func init LocalFunctionProj --python
    ```

1. 瀏覽至專案資料夾：

    ```console
    cd LocalFunctionProj
    ```
    
    此資料夾會包含專案的各種檔案，包括名為 [local.settings.json](functions-run-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的組態檔。 由於 *local.settings.json* 可能會包含從 Azure 下載的秘密，因此 *.gitignore* 檔案依預設會將該檔案排除在原始檔控制以外。

1. 使用下列命令，將函式新增至您的專案，其中 `--name` 引數是函式的唯一名稱 (HttpExample)，而 `--template` 引數可指定函式的觸發程序 (HTTP)。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new` 會建立符合函式名稱的子資料夾，其中包含適合專案所選語言的程式碼檔案，以及名為 *function.json* 的組態檔。

### <a name="optional-examine-the-file-contents"></a>(選擇性) 檢查檔案內容

如有需要，您可以跳到[在本機執行函式](#run-the-function-locally)，並於稍後再檢查檔案內容。

#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* 包含 `main()` Python 函式，此函式會根據 *function.json* 中的設定而觸發。

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

針對 HTTP 觸發程序，函式會接收變數 `req` 中的要求資料，如 *function.json* 中所定義。 `req` 是 [azure.functions.HttpRequest 類別](/python/api/azure-functions/azure.functions.httprequest)的執行個體。 傳回物件 (在 function.json 中定義為 `$return`)，是 [azure.functions.HttpResponse 類別](/python/api/azure-functions/azure.functions.httpresponse)的執行個體。 若要深入了解，請參閱 [Azure Functions HTTP 觸發程序和繫結](./functions-bindings-http-webhook.md?tabs=python)。

#### <a name="functionjson"></a>function.json

*function.json* 是一個組態檔，會定義函式的輸入和輸出 `bindings`，包括觸發程序類型。

如有需要，您可以變更 `scriptFile` 以叫用不同的 Python 檔案。

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

每個繫結都需要方向、類型和唯一名稱。 HTTP 觸發程序具有 [`httpTrigger`](functions-bindings-http-webhook-trigger.md) 類型的輸入繫結，和 [`http`](functions-bindings-http-webhook-output.md) 類型的輸出繫結。

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>為您的函式建立支援的 Azure 資源

若要將函式程式碼部署至 Azure，您必須先建立三個資源：

- 資源群組，這是相關資源的邏輯容器。
- 儲存體帳戶，用來維護專案的狀態和其他資訊。
- 函式應用程式，可提供用來執行函式程式碼的環境。 函式應用程式可對應至您的本機函式專案，並可讓您將函式分組為邏輯單位，以便管理、部署和共用資源。

請使用下列命令來建立這些項目。 Azure CLI 和 PowerShell 均受支援。

1. 如果您尚未登入 Azure，請於此時登入：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az-login) 命令會將您登入您的 Azure 帳戶。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 會將您登入您的 Azure 帳戶。

    ---

1. 在 `westeurope` 區域中，建立名為 `AzureFunctionsQuickstart-rg` 的資源群組。 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [az group create](/cli/azure/group#az-group-create) 命令會建立資源群組。 您通常會使用 `az account list-locations` 命令傳回的可用區域，在您附近的區域中建立資源群組和資源。

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令會建立資源群組。 您通常會使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 傳回的可用區域，在您附近的區域中建立資源群組和資源。

    ---

    > [!NOTE]
    > 您無法在相同的資源群組中裝載 Linux 和 Windows 應用程式。 如果您有名為 `AzureFunctionsQuickstart-rg` 的現有資源群組，且其中包含 Windows 函式應用程式或 Web 應用程式，則必須使用不同的資源群組。

1. 在您的資源群組和區域中建立一般用途的儲存體帳戶：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令會建立儲存體帳戶。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) Cmdlet 會建立儲存體帳戶。

    ---

    在上述範例中，請將 `<STORAGE_NAME>` 取代為適合您且在 Azure 儲存體中是唯一的名稱。 名稱只能包含 3 到 24 個字元的數字和小寫字母。 `Standard_LRS` 會指定[受 Functions 支援](storage-considerations.md#storage-account-requirements)的一般用途帳戶。
    
    在本快速入門中，儲存體帳戶只會產生幾美分的費用。

1. 在 Azure 中建立函式應用程式：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令會在 Azure 中建立函式應用程式。 如果您使用 Python 3.7 或 3.6，請分別將 `--runtime-version` 變更為 `3.7` 或 `3.6`。
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) Cmdlet 會在 Azure 中建立函式應用程式。 如果您使用 Python 3.7 或 3.6，請分別將 `-RuntimeVersion` 變更為 `3.7` 或 `3.6`。

    ---
    
    在上一個範例中，將 `<STORAGE_NAME>` 取代為您在上一個步驟中使用的帳戶名稱，並將 `<APP_NAME>` 取代為適合您的全域唯一名稱。  `<APP_NAME>` 也是函式應用程式的預設 DNS 網域。 
    
    此命令會依據 [Azure Functions 使用方案](functions-scale.md#consumption-plan)，建立在您指定的語言執行階段中執行的函式應用程式，而此應用程式在此處產生的使用量是免費的。 此命令也會在相同的資源群組中佈建相關聯的 Azure Application Insights 執行個體，您可將其用於監視函式應用程式和檢視記錄。 如需詳細資訊，請參閱[監視 Azure Functions](functions-monitoring.md)。 在您啟用此執行個體之前，並不會產生任何成本。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

執行下列命令，在 Azure 入口網站的 Application Insights 中檢視近乎即時的[串流記錄](functions-run-local.md#enable-streaming-logs)：

```console
func azure functionapp logstream <APP_NAME> --browser
```

在個別終端機視窗或瀏覽器中，再次呼叫遠端函式。 Azure 中的函式執行會有詳細資訊記錄顯示在終端機中。 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [連線至 Azure 儲存體佇列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[有任何問題嗎？請告訴我們。](https://aka.ms/python-functions-qs-survey)