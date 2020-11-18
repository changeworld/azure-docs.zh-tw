---
title: 從命令列建立 PowerShell 函式 - Azure Functions
description: 了解如何從命令列建立 PowerShell 函式，然後將本機專案發佈至 Azure Functions 中的無伺服器裝載。
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
ms.openlocfilehash: 45745419932a40a34abe65f54c61c87ab245f487
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424649"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>快速入門：從命令列在 Azure 中建立 PowerShell 函式

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

在本文中，您會使用命令列工具建立可回應 HTTP 要求的 PowerShell 函式。 在本機測試程式碼之後，您可以將其部署到 Azure Functions 的無伺服器環境。

完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。

這也是本文的 [Visual Studio Code 版本](create-first-function-vs-code-powershell.md)。

## <a name="configure-your-local-environment"></a>設定您的本機環境

開始之前，您必須具備下列條件：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.x 版。

+ 下列其中一項用來建立 Azure 資源的工具：

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5.0 版或更新版本。

    + [Azure CLI](/cli/azure/install-azure-cli) 2.4 版或更新版本。

+ [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

### <a name="prerequisite-check"></a>先決條件檢查

確認您的必要條件，這取決於您是使用 Azure CLI 還是 Azure PowerShell 來建立 Azure 資源：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ 在終端機或命令視窗中執行 `func --version`，以確認 Azure Functions Core Tools 為 3.x 版。

+ 執行 `az --version` 以確認 Azure CLI 版本為 2.4 或更新版本。

+ 執行 `az login` 以登入 Azure 並驗證有效訂用帳戶。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ 在終端機或命令視窗中執行 `func --version`，以確認 Azure Functions Core Tools 為 3.x 版。

+ 執行 `(Get-Module -ListAvailable Az).Version` 並確認為 5.0 版或更新版本。 

+ 執行 `Connect-AzAccount` 以登入 Azure 並驗證有效訂用帳戶。

---

## <a name="create-a-local-function-project"></a>建立本機函式專案

在 Azure Functions 中，函式專案是包含一或多個個別函式的容器，而每個函式分別會回應特定的觸發程序。 專案中的所有函式會共用相同的本機和裝載設定。 在本節中，您將建立包含單一函式的函式專案。

1. 執行 `func init` 命令，以使用指定的執行階段在名為 LocalFunctionProj 的資料夾中建立函式專案：  

    ```console
    func init LocalFunctionProj --powershell
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

#### <a name="runps1"></a>run.ps1

*run.ps1* 會定義根據 *function.json* 中的設定而觸發的函式指令碼。

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

針對 HTTP 觸發程序，函式會接收傳至 `$Request` 變數 (定義於 *function.json* 中) 的要求資料。 傳回物件 (在 *function.json* 中定義為 `Response`) 會傳至 `Push-OutputBinding` Cmdlet 作為回應。 

#### <a name="functionjson"></a>function.json

*function.json* 是一個組態檔，會定義函式的輸入和輸出 `bindings`，包括觸發程序類型。 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

每個繫結都需要方向、類型和唯一名稱。 HTTP 觸發程序具有 [`httpTrigger`](functions-bindings-http-webhook-trigger.md) 類型的輸入繫結，和 [`http`](functions-bindings-http-webhook-output.md) 類型的輸出繫結。

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. 在 Azure 中建立函式應用程式：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令會在 Azure 中建立函式應用程式。 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) Cmdlet 會在 Azure 中建立函式應用程式。 
    
    ---
    
    在上一個範例中，將 `<STORAGE_NAME>` 取代為您在上一個步驟中使用的帳戶名稱，並將 `<APP_NAME>` 取代為適合您的全域唯一名稱。 `<APP_NAME>` 也是函式應用程式的預設 DNS 網域。 
    
    此命令會依據 [Azure Functions 使用方案](functions-scale.md#consumption-plan)，建立在您指定的語言執行階段中執行的函式應用程式，而此應用程式在此處產生的使用量是免費的。 此命令也會在相同的資源群組中佈建相關聯的 Azure Application Insights 執行個體，您可將其用於監視函式應用程式和檢視記錄。 如需詳細資訊，請參閱[監視 Azure Functions](functions-monitoring.md)。 在您啟用此執行個體之前，並不會產生任何成本。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [連線至 Azure 儲存體佇列]

[連線至 Azure 儲存體佇列]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell
