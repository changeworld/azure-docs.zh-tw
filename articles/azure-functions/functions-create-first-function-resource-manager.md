---
title: 使用 Azure Resource Manager 範本建立您的第一個函式
description: 使用 Azure Resource Manager 範本 (ARM 範本) 來建立簡單的 HTTP 觸發無伺服器函式，並將其部署至 Azure。
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: 1e623405faa89ff41eccdaa57578bc8ac94cd78c
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422819"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>快速入門：從 ARM 範本建立及部署 Azure Functions 資源

在本文中，您會使用 Azure Resource Manager 範本 (ARM 範本) 來建立回應 HTTP 要求的函式。 

完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

### <a name="azure-account"></a>Azure 帳戶 

開始之前，您必須擁有具備作用中訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/)。

### <a name="create-a-local-functions-project"></a>建立本機 Functions 專案

本文需要本機函式程式碼專案，才能在您建立的 Azure 資源上執行。 如果您沒有先建立要發佈的專案，就無法完成本文的部署小節。 

選擇下列其中一個索引標籤，遵循連結，然後完成以您選擇的語言建立函式應用程式的小節：

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

在 Visual Studio Code 中，以您選擇的語言建立您的本機函式專案：  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[在 Visual Studio 中建立您的本機函式專案](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[命令列](#tab/command-line)

從命令列以您選擇的語言建立您的本機函式專案：

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

在本機建立專案之後，您會建立在 Azure 中執行新函式所需的資源。 

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/)。

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

此範本會建立下列四個 Azure 資源：

+ [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)：建立 Functions 所需的 Azure 儲存體帳戶。
+ [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms)：為函式應用程式建立無伺服器使用量主控方案。
+ [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites)：建立函式應用程式。
+ [**microsoft.insights/components**](/azure/templates/microsoft.insights/components)：建立用於監視的 Application Insights 執行個體。

## <a name="deploy-the-template"></a>部署範本

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>驗證部署

接下來，您會透過將專案發佈至 Azure 並呼叫函式的 HTTP 端點，來驗證您所建立的函式應用程式裝載資源。

### <a name="publish-the-function-project-to-azure"></a>將函式專案發佈至 Azure

使用下列步驟，將您的專案發佈至新的 Azure 資源：

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

在輸出中，複製 HTTP 觸發程序的 URL。 您可以使用此項目來測試您的函式已在 Azure 中執行。 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案並選取 [發佈]。

1. 在 [挑選發佈目標] 中，選擇 [Azure Functions 使用量方案] 的 [選取現有的]，然後選取 [建立設定檔]。

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="選擇現有的發佈目標":::

1. 選擇您的 **訂用帳戶**，展開資源群組，選取您的函式應用程式，然後選取 [確定]。

1. 發佈完成之後，請複製 **網站 URL**。

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="從發佈摘要複製網站 URL":::

1. 附加路徑 `/api/<FUNCTION_NAME>?name=Functions`，其中 `<FUNCTION_NAME>` 是函式的名稱。 呼叫 HTTP URL 觸發函式的 URL 會採用下列格式：

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

您可以使用此 URL 來測試您的 HTTP 觸發程式函式已在 Azure 中執行。

# <a name="command-line"></a>[命令列](#tab/command-line)

若要將您的本機程式碼發佈至 Azure 中的函式應用程式，請使用 `publish` 命令：

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

在此範例中，以您的函式應用程式名稱取代 `<FUNCTION_APP_NAME>`。 您可能需要使用 `az login` 重新登入。 

在輸出中，複製 HTTP 觸發程序的 URL。 您可以使用此項目來測試您的函式已在 Azure 中執行。

---

### <a name="invoke-the-function-on-azure"></a>在 Azure 上叫用函式

將您為 HTTP 要求複製的 URL 貼到瀏覽器的網址列中，確定 `name` 查詢字串 `?name=Functions` 已附加至此 URL 的結尾，然後執行要求。 

您應該會看到像這樣的回應：

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個步驟並新增 Azure 儲存體佇列輸出繫結，請保留您所有的資源，因為在後續的工作還會用到。

否則，請使用下列命令刪除資源群組及其包含的所有資源，以避免產生額外的成本。

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

將 `<RESOURCE_GROUP_NAME>` 以您的資源群組名稱取代。

## <a name="next-steps"></a>後續步驟

既然您已發佈第一個函式，請將輸出繫結新增至您的函式以深入了解。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [連線至 Azure 儲存體佇列](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [連線至 Azure 儲存體佇列](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[命令列](#tab/command-line)

> [!div class="nextstepaction"]
> [連線至 Azure 儲存體佇列](functions-add-output-binding-storage-queue-cli.md)

---
