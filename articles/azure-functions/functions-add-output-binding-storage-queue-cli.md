---
title: 使用命令列工具將 Azure Functions 連線到 Azure 儲存體
description: 了解如何將輸出繫結新增至您的命令列專案，進而將 Azure Functions 連線到 Azure 儲存體佇列。
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e3c37b368b723cc95302949baa8e85e2a8b621be
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200999"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>使用命令列工具將 Azure Functions 連線到 Azure 儲存體

在本文中，您將整合 Azure 儲存體佇列與您在[前一個快速入門](functions-create-first-azure-function-azure-cli.md)中建立的函式和儲存體帳戶。 您可以使用*輸出繫結*將 HTTP 要求中的資料寫入至佇列中的訊息，以完成這項整合。 完成本文的作業，並不會在先前的快速入門收取的幾美分以外產生額外的費用。 若要深入了解繫結，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。

## <a name="configure-your-local-environment"></a>設定您的本機環境

開始之前，您必須先完成[快速入門：從命令列建立 Azure Functions 專案](functions-create-first-azure-function-azure-cli.md)。 如果您已在該文章結束時清除資源，請重新執行這些步驟以在 Azure 中重新建立函式應用程式和相關資源。

## <a name="retrieve-the-azure-storage-connection-string"></a>擷取 Azure 儲存體連接字串

在先前的快速入門中，當您在 Azure 中建立函式應用程式時，您也已建立儲存體帳戶。 此帳戶的連接字串會安全地儲存在 Azure 的應用程式設定中。 藉由將設定下載到 *local.settings.json* 檔案中，您可以在本機執行函式時，使用該連線寫入至相同帳戶中的儲存體佇列。 

1. 從專案的根目錄執行下列命令 (請將 `<APP_NAME>` 取代為先前快速入門中的函式應用程式名稱)。 此命令將會覆寫檔案中任何現有的值。

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. 開啟 *local.settings.json* 並找出名為 `AzureWebJobsStorage` 的值，也就是儲存體帳戶連接字串。 您會在本文的其他章節中使用名稱 `AzureWebJobsStorage` 和連接字串。

> [!IMPORTANT]
> 由於 *local.settings.json* 中包含從 Azure 下載的祕密，因此請一律將此檔案排除在原始檔控制以外。 使用本機函式專案建立的 *.gitignore* 檔案依預設會排除該檔案。

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>將輸出繫結定義新增至函式

一個函式只能有一個觸發程序，但可以有多個輸入和輸出繫結，如此，您無須撰寫自訂整合程式碼，即可連線至其他 Azure 服務和資源。 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
您會在函式資料夾中的 *function.json* 檔案中宣告這些繫結。 在先前的快速入門中，*HttpExample* 資料夾中的 *function.json* 檔案包含 `bindings` 集合中的兩個繫結：  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
每個繫結至少有一個類型、一個方向和一個名稱。 在上述範例中，第一個繫結的類型為 `httpTrigger`、方向為 `in`。 針對 `in` 方向，`name` 會指定觸發程序叫用函式時所傳入的輸入參數名稱。  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
集合中的第二個繫結名為 `res`。 此 `http` 繫結是用來寫入 HTTP 回應的輸出繫結 (`out`)。 

若要從這個函式寫入至 Azure 儲存體佇列，請新增類型為 `queue`、名稱為 `msg` 的 `out` 繫結，如下列程式碼所示：

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
集合中第二個繫結的類型為 `http`、方向為 `out`，在此情況下，`$return` 的特殊 `name` 表示此繫結會使用函式的傳回值，而不是提供輸入參數。

若要從這個函式寫入至 Azure 儲存體佇列，請新增類型為 `queue`、名稱為 `msg` 的 `out` 繫結，如下列程式碼所示：

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
集合中的第二個繫結名為 `res`。 此 `http` 繫結是用來寫入 HTTP 回應的輸出繫結 (`out`)。 

若要從這個函式寫入至 Azure 儲存體佇列，請新增類型為 `queue`、名稱為 `msg` 的 `out` 繫結，如下列程式碼所示：

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
在此案例下，會將 `msg` 提供給函式作為輸出引數。 針對 `queue` 類型，您也必須在 `queueName` 中指定佇列的名稱，並在 `connection` 中提供 Azure 儲存體連線 (來自 *local.settings.json*) 的*名稱*。 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

如需繫結的詳細資訊，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)與[佇列輸出設定](functions-bindings-storage-queue-output.md#configuration)。

## <a name="add-code-to-use-the-output-binding"></a>新增程式碼以使用輸出繫結

在 *function.json* 中指定佇列繫結之後，您現在可以更新函式以接收 `msg` 輸出參數，並將訊息寫入至佇列。

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

請留意，您*無須*撰寫任何程式碼來進行驗證、取得佇列參考或寫入資料。 這些整合工作全都可在 Azure Functions 執行階段和佇列輸出繫結中輕易處理。

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>檢視 Azure 儲存體佇列中的訊息

您可以在 [Azure 入口網站](../storage/queues/storage-quickstart-queues-portal.md)或 [Microsoft Azure 儲存體總管](https://storageexplorer.com/)中檢視佇列。 您也可以在 Azure CLI 中檢視佇列，如下列步驟所說明：

1. 開啟函式專案的 *local.setting.json* 檔案，並複製連接字串值。 在終端機或命令視窗中，執行下列命令 (請貼上您的特定連接字串以取代 `<MY_CONNECTION_STRING>`)，以建立名為 `AZURE_STORAGE_CONNECTION_STRING`的環境變數。 (此環境變數意味著您無須使用 `--connection-string` 引數將連接字串提供給每個後續命令。)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (選擇性) 使用 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 命令檢視您帳戶中的儲存體佇列。 此命令的輸出應該會包含名為 `outqueue` 的佇列，這是函式將其第一個訊息寫入至該佇列時所建立的。
    
    ```azure-cli
    az storage queue list --output tsv
    ```

1. 使用 [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) 命令來讀取此佇列中的訊息，這應該是您先前測試函式時所使用的名字。 此命令會讀取並移除佇列中的第一個訊息。 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    此指令碼會使用 certutil 將本機暫存檔案中的 base64 編碼訊息集合解碼。 如果沒有任何輸出，請嘗試從指令碼中移除 `> NUL`，以停止隱藏 certutil 輸出 (以防萬一發生錯誤)。 
    
    ---
    
    由於訊息本文會以 [base64 編碼](functions-bindings-storage-queue-trigger.md#encoding)儲存，因此必須先將訊息解碼才會顯示。 執行 `az storage message get` 之後，就會從佇列中移除此訊息。 如果 `outqueue` 中只有一個訊息，則當您第二次執行此命令時，將不會擷取訊息，而是會收到錯誤。

## <a name="redeploy-the-project-to-azure"></a>將專案重新部署至 Azure

現在，您已在本機確認函式已將訊息寫入至 Azure 儲存體佇列，您可以重新部署專案，以更新在 Azure 上執行的端點。

1. 在 *LocalFunctionsProj* 資料夾中，使用 [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) 命令來重新部署專案 (請將 `<APP_NAME>` 取代為您的應用程式名稱)。

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. 如先前的快速入門所示，使用瀏覽器或 CURL 來測試已重新部署的函式。

    # <a name="browser"></a>[瀏覽器](#tab/browser)
    
    將發佈命令的輸出中顯示的完整**叫用 URL** 複製到瀏覽器網址列中 (請附加查詢參數 `&name=Functions`)。 瀏覽器應該會顯示與您在本機執行函式時類似的輸出。

    ![使用瀏覽器在 Azure 上執行函式的輸出](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    使用**叫用 URL** 來執行 [`curl`](https://curl.haxx.se/) (請附加參數 `&name=Functions`)。 命令的輸出應該是文字 "Hello Functions"。
    
    ![使用 CURL 在 Azure 上執行函式的輸出](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. 再次檢查儲存體佇列 (如上一節所述)，以確認其中包含寫入至佇列的新訊息。

## <a name="clean-up-resources"></a>清除資源

完成之後，請使用下列命令刪除資源群組及其包含的所有資源，以避免產生額外的成本。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>後續步驟

您已更新 HTTP 觸發的函式，以將資料寫入至儲存體佇列。 您現在可以深入了解如何使用 Core Tools 和 Azure CLI，從命令列開發 Functions：

+ [使用 Azure Functions Core Tools](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [C# 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=csharp)。

+ [Azure Functions C# 開發人員參考](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=javascript)。

+ [Azure Functions JavaScript 開發人員指南](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [TypeScript 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=typescript)。

+ [Azure Functions TypeScript 開發人員指南](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Python 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=python)。

+ [Azure Functions Python 開發人員指南](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [PowerShell 中完整函式專案的範例](/samples/browse/?products=azure-functions&languages=azurepowershell)。

+ [Azure Functions PowerShell 開發人員指南](functions-reference-powershell.md) 
::: zone-end
+ [Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)

+ [Functions 定價頁面](https://azure.microsoft.com/pricing/details/functions/)

+ [估計取用方案成本](functions-consumption-costs.md) 
