---
title: 教學課程 - 依 Azure 函式觸發容器群組
description: 建立 HTTP 觸發的無伺服器 PowerShell 函式來自動建立 Azure 容器執行個體
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e7315796f2f7f89800b58f5fa607e69cd7ae3447
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935413"
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

## <a name="prerequisites"></a>必要條件

請參閱[使用 Visual Studio Code 在 Azure 中建立您的第一個函式](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell#configure-your-environment)，以了解在您作業系統上安裝及使用 Visual Studio Code 與 Azure Functions 擴充功能的必要條件。

本文中的其他步驟會使用 Azure PowerShell。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell][azure-powershell-install] 及[登入 Azure](/powershell/azure/get-started-azureps#sign-in-to-azure)。

## <a name="create-a-basic-powershell-function"></a>建立基本的 PowerShell 函式

依照[在 Azure 中建立第一個 PowerShell 函式](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell)中的步驟，使用 HTTP 觸發程式範本建立 PowerShell 函式。 使用預設的 Azure 函式名稱 **HttpTrigger**。 如快速入門中所示，在本機測試函式，並將專案發佈至 Azure 中的函式應用程式。 此範例是會傳回文字字串的基本 HTTP 觸發函式。 在本文稍後的步驟中，您會修改函式來建立容器群組。

本文假設您使用名稱 myfunctionapp 來發行專案，而且專案位在以函式應用程式名稱 (也就是 myfunctionapp) 自動命名的 Azure 資源群組中。 請在稍後的步驟中替代您的唯一函式應用程式名稱和資源組名稱。

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>在函式應用程式中啟用由 Azure 管理的身分識別

下列命令會在函式應用程式中啟用系統指派的[受控識別](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity)。 執行應用程式的 PowerShell 主機可以使用此身分識別自動對 Azure 進行驗證，讓函式在該身分識別可存取的 Azure 服務上採取動作。 在本教學課程中，您會對受控識別授與可以在函式應用程式資源群組中建立資源的權限。 

[將身分識別新增](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1)至函式應用程式：

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

將「參與者」角色範圍的身分識別指派給資源群組：

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>修改 HttpTrigger 函式

修改  **HttpTrigger** 函式的 PowerShell 程式碼以建立容器群組。 在函式的 `run.ps1` 檔案中，尋找下列程式碼區塊。 如果有名稱值作為函式 URL 中的查詢字串傳遞，則此程式碼會顯示該名稱值：

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

將此程式碼取代為下列範例區塊。 在這裡，如果有名稱值在查詢字串中傳遞，則會使用 [New-AzContainerGroup][new-azcontainergroup] Cmdlet 以該值命名和建立容器群組。 請務必將資源群組名稱 myfunctionapp 取代為您函式應用程式的資源群組名稱：

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

此範例會建立一個容器群組，其中包含執行 `alpine` 映像的單一容器執行個體。 該容器會執行單一 `echo` 命令，然後終止。 在實際範例中，您可以觸發一個或多個容器群組的建立，以執行批次作業。
 
## <a name="test-function-app-locally"></a>在本機測試函式應用程式

確定函式可在本機執行，然後再將函式應用程式專案重新發佈至 Azure。 在本機執行時，函式不會建立 Azure 資源。 不過，您可以透過是否在查詢字串中傳遞名稱值的方式，來測試函式流程。 若要對函式進行偵錯，請參閱[在本機偵錯 PowerShell Azure Functions](../azure-functions/functions-debug-powershell-local.md)。

## <a name="republish-azure-function-app"></a>重新發佈 Azure 函式應用程式

確認函式在本機執行之後，請將專案重新發佈至 Azure 中的現有函式應用程式。

1. 在 Visual Studio Code 中開啟命令選擇區。 搜尋並選取 `Azure Functions: Deploy to Function App...`。
1. 選取目前的工作資料夾，將其加入 ZIP 檔並部署。
1. 選取訂用帳戶，然後選取現有的函式應用程式名稱 (myfunctionapp)。 確認您要覆寫先前的部署。

建立函式應用程式並套用部署套件之後，即會顯示通知。 在通知中選取 [檢視輸出]，即可檢視建立和部署結果，包括您所更新的 Azure 資源。

## <a name="run-the-function-in-azure"></a>在 Azure 中執行函式

在部署順利完成後，取得函式 URL。 例如，使用 **Azure：Functions** 區域 (在 Visual Studio Code 中) 來複製 **HttpTrigger** 函式 URL，或在 [Azure 入口網站](../azure-functions/functions-get-started.md)中取得函式 URL。

函式 URL 的格式如下：

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>執行函式，但不傳遞名稱

第一次測試時，請執行 `curl` 命令，並傳遞函式 URL，但不附加 `name` 查詢字串。 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

函式會傳回狀態碼 200 和文字 `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response`：

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>執行函式並傳遞容器群組的名稱

現在，執行 `curl` 命令，並將容器群組的名稱 (mycontainergroup) 附加為查詢字串 `?name=mycontainergroup`：

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

函式會傳回狀態碼 200，並觸發容器群組的建立：

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

使用 [Get-AzContainerInstanceLog][get-azcontainerinstancelog] 命令確認容器是否已執行：

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

範例輸出：

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>清除資源

如果您已不再需要這個教學課程中建立的任何資源，便可以執行 [az group delete][az-group-delete] 命令來移除資源群組及其包含的所有資源。 此命令除了會刪除執行中的容器和所有相關資源之外，也會刪除您所建立的函式應用程式。

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

如需有關建立 Azure 函式和發佈函式專案的詳細指引，請參閱 [AzureFunctions 文件](../azure-functions/index.yml)。 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
