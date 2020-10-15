---
title: 教學課程 - 部署本機 Azure Resource Manager 範本
description: 了解如何從您的本機電腦部署 Azure Resource Manager 範本
ms.date: 05/20/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: fe13376ced428713703f2bd5cf33941129dec1d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611617"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>教學課程：部署本機 Azure Resource Manager 範本

了解如何從您的本機電腦部署 Azure Resource Manager 範本。 完成此教學課程大約需要 **8 分鐘**。

此教學課程是系列中的第一個。 當您完成此課程系列時，您會藉由建立連結的範本將範本模組化、將連結的範本儲存在儲存體帳戶中，並使用 SAS 權杖保護連結的範本，並了解如何建立 DevOp 管線以部署範本。 此系列著重於範本部署。  如果您想要了解範本開發，請參閱[初學者教學課程](./template-tutorial-create-first-template.md)。

## <a name="get-tools"></a>取得工具

首先，請確定您擁有部署範本所需的工具。

### <a name="command-line-deployment"></a>命令列部署

您需要 Azure PowerShell 或 Azure CLI 來部署範本。 如需安裝指示，請參閱：

- [安裝 Azure PowerShell](/powershell/azure/install-az-ps)
- [在 Windows 上安裝 Azure CLI](/cli/azure/install-azure-cli-windows)
- [在 Linux 上安裝 Azure CLI](/cli/azure/install-azure-cli-linux)

安裝 Azure PowerShell 或 Azure CLI 之後，請確定您是第一次登入。 如需說明，請參閱[登入 - PowerShell](/powershell/azure/install-az-ps#sign-in) 或[登入 - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in)。

### <a name="editor-optional"></a>編輯器 (選擇性)

範本是 JSON 檔案。 若要檢閱/編輯範本，您需要良好的 JSON 編輯器。 我們建議使用含 Resource Manager 工具擴充功能的 Visual Studio Code。 如果您需要安裝這些工具，請參閱[快速入門：使用 Visual Studio Code 建立 Azure Resource Manager 範本](quickstart-create-templates-use-visual-studio-code.md)。

## <a name="review-template"></a>檢閱範本

範本會部署儲存體帳戶、App Service 方案和 Web 應用程式。 如果您想要建立範本，則可以瀏覽[關於快速入門範本的教學課程](template-tutorial-quickstart-template.md)。 但這並非完成本教學課程的必要條件。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能使用數字和小寫字母。 名稱必須是唯一的。 在範本中，儲存體帳戶名稱是附加了 "store" 的專案名稱，且專案名稱的長度必須介於 3 到 11 個字元之間。 因此，專案名稱必須符合儲存體帳戶名稱需求，且少於 11 個字元。

以副檔名. json 將範本的複本儲存到您的本機電腦，例如 azuredeploy.json。 您稍後會在本教學課程中部署此範本。

## <a name="sign-in-to-azure"></a>登入 Azure

若要開始使用 Azure PowerShell/Azure CLI 部署範本，請使用您的 Azure 認證登入。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

如果您有多個 Azure 訂用帳戶，請選取您要使用的訂用帳戶：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>建立資源群組

部署範本時，您必須指定將包含資源的資源群組。 執行部署命令之前，使用 Azure CLI 或 Azure PowerShell 來建立資源群組。 選取下列程式碼區段中的索引標籤，以在 Azure PowerShell 和 Azure CLI 之間進行選擇。 本文中的 CLI 範例是針對 Bash Shell 所撰寫。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>部署範本

使用一或兩個部署選項來部署範本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

若要深入了解如何使用 Azure PowerShell 來部署範本，請參閱[使用 Resource Manager 範本與 Azure PowerShell 部署資源](./deploy-powershell.md)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

若要深入了解如何使用 Azure CLI 來部署範本，請參閱[使用 Resource Manager 範本和 Azure CLI 部署資源](./deploy-cli.md)。

---

## <a name="clean-up-resources"></a>清除資源

您可以藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
2. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

您已了解如何部署本機範本。 在下一個教學課程中，您會將範本分成主要範本和連結的範本，並了解如何儲存和保護連結的範本。

> [!div class="nextstepaction"]
> [部署連結的範本](./deployment-tutorial-linked-template.md)
