---
title: 教學課程 - 建立及部署範本
description: 建立第一個 Azure Resource Manager 範本。 在本教學課程中，您將了解範本檔案語法及部署儲存體帳戶的方式。
author: mumian
ms.date: 05/20/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2180ca80d87643eb885d814318e516b4b3c53f37
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714792"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>教學課程：建立及部署您的第一個 ARM 範本

本教學課程將介紹 Azure Resource Manager (ARM) 範本。 其中示範如何建立入門範本，並將它部署至 Azure。 您將了解範本的結構，以及使用範本所需的工具。 完成此教學課程大約需要 **12 分鐘**，但實際時間則會根據您需要安裝的工具數目而有所不同。

此教學課程是系列中的第一個。 當您進行本課程系列時，將會逐步修改起始範本，直到您已探索 ARM 範本的所有核心部分為止。 這些元素都是適用於更複雜範本的建置組塊。 我們希望在本系列結束時，您能夠有信心地建立自己的範本，並準備好使用範本來將部署自動化。

如果您想要了解使用範本的優點，以及為何要使用範本來將部署自動化，請參閱 [Azure Resource Manager 範本](overview.md)。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="get-tools"></a>取得工具

首先，確定您擁有建立及部署範本所需的工具。

### <a name="editor"></a>編輯器

範本是 JSON 檔案。 若要建立範本，您需要良好的 JSON 編輯器。 我們建議使用含 Resource Manager 工具擴充功能的 Visual Studio Code。 如果您需要安裝這些工具，請參閱[使用 Visual Studio Code 來建立 ARM 範本](use-vs-code-to-create-template.md)。

### <a name="command-line-deployment"></a>命令列部署

您也需要 Azure PowerShell 或 Azure CLI 來部署範本。 如果您使用 Azure CLI，則必須擁有最新版本。 如需安裝指示，請參閱：

- [安裝 Azure PowerShell](/powershell/azure/install-az-ps)
- [在 Windows 上安裝 Azure CLI](/cli/azure/install-azure-cli-windows)
- [在 Linux 上安裝 Azure CLI](/cli/azure/install-azure-cli-linux)

安裝 Azure PowerShell 或 Azure CLI 之後，請確定您是第一次登入。 如需說明，請參閱[登入 - PowerShell](/powershell/azure/install-az-ps#sign-in) 或[登入 - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in)。

好的，您已經準備好開始了解範本。

## <a name="create-your-first-template"></a>建立您的第一個範本

1. 開啟已安裝 Resource Manager 工具擴充功能的 Visual Studio Code。
1. 從 [檔案]  功能表中，選取 [新增檔案]  以建立新檔案。
1. 從 [檔案]  功能表中，選取 [另存新檔]  。
1. 將檔案命名為 **azuredeploy**，然後選取 [JSON]  副檔名。 檔案的完整名稱是 **azuredeploy.json**。
1. 將檔案儲存到您的工作站。 選取容易記住的路徑，因為您稍後將在部署範本時提供該路徑。
1. 將下列 JSON 複製並貼到檔案中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    您的 VS Code 環境看起來像這樣：

    ![Resource Manager 範本 Visual Studio Code 第一個範本](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    此範本不會部署任何資源。 我們會從空白範本開始，讓您能夠熟悉部署範本的步驟，同時將發生錯誤的機會降到最低。

    JSON 檔案具有下列元素：

    - **$schema**：指定 JSON 結構描述檔案的位置。 結構描述檔案會描述範本中可用的屬性。 例如，結構描述會將**資源**定義為範本的其中一個有效屬性。 不必擔心結構描述的日期是 2019-04-01。 此結構描述版本是最新的，而且包含所有最新功能。 結構描述日期尚未變更，因為在引進它之後就沒有任何重大變更。
    - **contentVersion**：指定範本版本 (例如 1.0.0.0)。 您可以為此元素提供任何值。 使用此值在範本中記載重大變更。 使用範本部署資源時，這個值可用來確定使用的是正確的範本。
    - **resources**：包含您想要部署或更新的資源。 它目前是空的，但您稍後將會新增資源。

1. 儲存檔案。

恭喜，您已建立第一個範本。

## <a name="sign-in-to-azure"></a>登入 Azure

若要開始使用 Azure PowerShell/Azure CLI，請使用您的 Azure 認證登入。

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
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>部署範本

若要部署範本，請使用 Azure CLI 或 Azure PowerShell。 使用您所建立的資源群組。 為部署提供名稱，讓您可以輕鬆地在部署記錄中識別它。 為了方便起見，也要建立變數來儲存範本檔案的路徑。 此變數可讓您更輕鬆地執行部署命令，因為您不需要在每次部署時重新輸入路徑。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要執行此部署命令，您必須擁有[最新版本](/cli/azure/install-azure-cli)的 Azure CLI。

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

此部署命令會傳回結果。 尋找 `ProvisioningState`，以查看部署是否成功。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![PowerShell 部署佈建狀態](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Azure CLI 部署佈建狀態](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> 如果部署失敗，請使用 **debug** 與部署命令切換，以顯示偵錯記錄。  您也可以使用 **verbose** 切換來顯示完整的偵錯記錄。

## <a name="verify-deployment"></a>驗證部署

您可以從 Azure 入口網站探索資源群組，藉以確認部署。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 從左側功能表中，選取 [資源群組]  。

1. 選取在最後一個程序中部署的資源群組。 預設名稱為 **myResourceGroup**。 您應該不會在資源群組內看到任何已部署的資源。

1. 請注意，在概觀右上方會顯示部署的狀態。 選取 [1 成功]  。

   ![檢視部署狀態](./media/template-tutorial-create-first-template/deployment-status.png)

1. 您會看到資源群組的部署記錄。 選取 [blanktemplate]  。

   ![選取部署](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. 您會看到部署的摘要。 在此情況下，由於未部署任何資源，所以不會看到很多內容。 在本系列稍後，您可能發現它對於在部署記錄中檢閱摘要很有幫助。 請注意，您可以在左側查看部署期間所使用的輸入、輸出和範本。

   ![檢視部署摘要](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個教學課程，則不需要刪除資源群組。

如果您現在要停止，則可能想要刪除資源群組。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

您已建立要部署至 Azure 的簡單範本。 在下一個教學課程中，您會將儲存體帳戶新增至範本，並將其部署至您的資源群組。

> [!div class="nextstepaction"]
> [新增資源](template-tutorial-add-resource.md)
