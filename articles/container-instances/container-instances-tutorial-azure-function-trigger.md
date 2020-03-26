---
title: 教學課程 - 依 Azure 函式觸發容器群組
description: 建立 HTTP 觸發的無伺服器 PowerShell 函式來自動建立 Azure 容器執行個體
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78331020"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>教學課程：使用 HTTP 觸發的 Azure 函式來建立容器群組

[Azure Functions](../azure-functions/functions-overview.md) 是無伺服器計算服務，可執行指令碼或程式碼以回應各種事件，例如 Azure 儲存體佇列中的 HTTP 要求、計時器或訊息。

在本教學課程中，您會建立接受 HTTP 要求並觸發[容器群組](container-instances-container-groups.md)部署的 Azure 函式。 此範例將說明使用 Azure Functions 在 Azure 容器執行個體中自動建立資源的基本概念。 您可以針對更複雜的案例或其他事件觸發程式，修改或擴充範例。 

您會了解如何：

> [!div class="checklist"]
> * 使用具有 [Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)的 Visual Studio Code 來建立基本的 HTTP 觸發 PowerShell 函式。
> * 在函式應用程式中啟用身分識別，並賦予其建立 Azure 資源的權限。
> * 修改並重新發佈 PowerShell 函式，以自動部署單一容器的容器群組。
> * 確認容器的 HTTP 觸發部署。

> [!IMPORTANT]
> 適用於 Azure Functions 的 PowerShell 目前為預覽版。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="prerequisites"></a>Prerequisites

請參閱[在 Azure 中建立您的第一個函式](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment)，以了解在您作業系統上安裝及使用 Visual Studio Code 與 Azure Functions 的必要條件。

本文中的部分步驟會使用 Azure CLI。 您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成這些步驟。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-a-basic-powershell-function"></a>建立基本的 PowerShell 函式

依照[在 Azure 中建立第一個 PowerShell 函式](../azure-functions/functions-create-first-function-powershell.md)中的步驟，使用 HTTP 觸發程式範本建立 PowerShell 函式。 使用預設的 Azure 函式名稱 **HttpTrigger**。 如快速入門中所示，在本機測試函式，並將專案發佈至 Azure 中的函式應用程式。 此範例是會傳回文字字串的基本 HTTP 觸發函式。 在本文稍後的步驟中，您會修改函式來建立容器群組。

本文假設您使用名稱 myfunctionapp  來發行專案，而且專案位在以函式應用程式名稱 (也就是 myfunctionapp  ) 自動命名的 Azure 資源群組中。 請在稍後的步驟中替代您的唯一函式應用程式名稱和資源組名稱。

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>在函式應用程式中啟用由 Azure 管理的身分識別

現在，在函式應用程式中啟用系統指派的[受控識別](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity)。 執行應用程式的 PowerShell 主機可以使用此身分識別自動進行驗證，讓函式在該身分識別可存取的 Azure 服務上採取動作。 在本教學課程中，您會對受控識別授與可以在函式應用程式資源群組中建立資源的權限。 

首先，使用 [az group show][az-group-show] 命令來取得函式應用程式資源群組的識別碼，並將其儲存在環境變數中。 此範例假設您會在 Bash 殼層中執行命令。

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

執行 [az functionapp identity app assign][az-functionapp-identity-app-assign]，將本機身分識別指派給函式應用程式，並將參與者角色指派給資源群組。 此角色可讓身分識別建立其他資源，例如資源群組中的容器群組。

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>修改 HttpTrigger 函式

修改  **HttpTrigger** 函式的 PowerShell 程式碼以建立容器群組。 在函式的 `run.ps1` 檔案中，尋找下列程式碼區塊。 如果有名稱值作為函式 URL 中的查詢字串傳遞，則此程式碼會顯示該名稱值：

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

將此程式碼取代為下列範例區塊。 在這裡，如果有名稱值在查詢字串中傳遞，則會使用 [New-AzContainerGroup][new-azcontainergroup] Cmdlet 以該值命名和建立容器群組。 請務必將資源群組名稱 myfunctionapp  取代為您函式應用程式的資源群組名稱：

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

此範例會建立一個容器群組，其中包含執行 `alpine` 映像的單一容器執行個體。 該容器會執行單一 `echo` 命令，然後終止。 在實際範例中，您可以觸發一個或多個容器群組的建立，以執行批次作業。
 
## <a name="test-function-app-locally"></a>在本機測試函式應用程式

確定函式可在本機正確執行，然後再將函式應用程式專案重新發佈至 Azure。 如 [PowerShell 快速入門](../azure-functions/functions-create-first-function-powershell.md)中所示，在 PowerShell 指令碼中插入本機中斷點，並在其上方插入 `Wait-Debugger` 呼叫。 如需偵錯指引，請參閱[在本機偵錯 PowerShell Azure Functions](../azure-functions/functions-debug-powershell-local.md)。


## <a name="republish-azure-function-app"></a>重新發佈 Azure 函式應用程式

確認函式在本機電腦上正確執行之後，就可以將專案重新發佈到 Azure 中現有的函式應用程式。

> [!NOTE]
> 請記得先移除對 `Wait-Debugger` 的任何呼叫，再將函式發佈至 Azure。

1. 在 Visual Studio Code 中開啟命令選擇區。 搜尋並選取 `Azure Functions: Deploy to function app...`。
1. 選取目前的工作資料夾，將其加入 ZIP 檔並部署。
1. 選取訂用帳戶，然後選取現有的函式應用程式名稱 (myfunctionapp  )。 確認您要覆寫先前的部署。

建立函式應用程式並套用部署套件之後，即會顯示通知。 在通知中選取 [檢視輸出]  ，即可檢視建立和部署結果，包括您所更新的 Azure 資源。

## <a name="run-the-function-in-azure"></a>在 Azure 中執行函式

在部署順利完成後，取得函式 URL。 例如，使用 **Azure：Functions** 區域 (在 Visual Studio 程式碼中) 來複製 **HttpTrigger** 函式 URL，或在 [Azure 入口網站](../azure-functions/functions-create-first-azure-function.md#test-the-function)中取得函式 URL。

函式 URL 包含唯一的程式碼，其格式如下：

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>執行函式，但不傳遞名稱

第一次測試時，請執行 `curl` 命令，並傳遞函式 URL，但不附加 `name` 查詢字串。 請務必包含函式的唯一程式碼。

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

函式會傳回狀態碼 400 和文字 `Please pass a name on the query string or in the request body`：

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>執行函式並傳遞容器群組的名稱

現在，將容器群組的名稱 (mycontainergroup  ) 附加為查詢字串 `&name=mycontainergroup`，並執行 `curl` 命令：

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

函式會傳回狀態碼 200，並觸發容器群組的建立：

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

使用 [az container logs][az-container-logs] 命令確認容器已在執行：

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

範例輸出：

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>清除資源

如果您已不再需要這個教學課程中建立的任何資源，便可以執行 [az group delete][az-group-delete] 命令來移除資源群組及其包含的所有資源。 此命令除了會刪除執行中的容器和所有相關資源之外，也會刪除您所建立的容器登錄。

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立接受 HTTP 要求並觸發容器群組部署的 Azure 函式。 您已了解如何︰

> [!div class="checklist"]
> * 使用具有 Azure Functions 擴充功能的 Visual Studio Code 來建立基本的 HTTP 觸發 PowerShell 函式。
> * 在函式應用程式中啟用身分識別，並賦予其建立 Azure 資源的權限。
> * 修改 PowerShell 函式程式碼，以自動部署單一容器的容器群組。
> * 確認容器的 HTTP 觸發部署。

如需啟動和監視容器化作業的詳細範例，請參閱部落格文章：[搭配 PowerShell Azure Functions 和 Azure 容器執行個體的事件驅動無伺服器容器](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b)\(英文\)，以及相關的[程式碼範例](https://github.com/anthonychu/functions-powershell-run-aci)。

如需有關建立 Azure 函式和發佈函式專案的詳細指引，請參閱 [AzureFunctions 文件](/azure/azure-functions/)。 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
