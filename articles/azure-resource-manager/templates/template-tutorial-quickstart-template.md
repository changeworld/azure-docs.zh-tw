---
title: 教學課程 - 使用快速入門範本
description: 了解如何使用 Azure 快速入門範本來完成範本開發。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 445e7ce2d6e609d75bff38bb3d049a87f184be12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613589"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>教學課程：使用 Azure 快速入門範本

[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)是社群參與的範本存放庫。 您可以在範本開發中使用範例範本。 在此教學課程中，您會尋找網站資源定義，然後將它新增至自己的範本。 完成此教學課程大約需要 **12 分鐘**。

## <a name="prerequisites"></a>Prerequisites

我們建議您完成[有關匯出範本的教學課程](template-tutorial-export-template.md)，但並非必要。

您必須擁有含 Resource Manager 工具延伸模組的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[範本工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>檢閱範本

在上一個教學課程結束時，您的範本會具有下列 JSON：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

此範本適用於部署儲存體帳戶和 App Service 方案，但您可能想要在其中新增網站。 您可以使用預先建置的範本，快速探索部署資源所需的 JSON。

## <a name="find-template"></a>尋找範本

1. 開啟 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)。
1. 在 [搜尋]  中，輸入**部署 Linux Web 應用程式**。
1. 選取標題為**部署基本 Linux Web 應用程式**的範本。 如果您找不到該範本，以下為[直接連結](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)。
1. 選取 [在 GitHub 上瀏覽]  。
1. 選取 [azuredeploy.json]  。
1. 檢閱範本。 特別是尋找 `Microsoft.Web/sites` 資源。

    ![Resource Manager 範本快速入門網站](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>修訂現有範本

將快速入門範本與現有範本合併：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

Web 應用程式名稱在 Azure 上必須是唯一的。 為避免名稱重複，已將 **webAppPortalName** 變數從 **"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"** 更新為 **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"** 。

在 `Microsoft.Web/serverfarms` 定義的結尾新增逗號，以將資源定義與 `Microsoft.Web/sites` 定義分開。

在此新資源中，有數個需注意的重要功能。

您將注意到它有一個名為 **dependsOn** 且已設為 App Service 方案的元素。 這是必要設定，因為 App Service 方案必須存在，才能建立 Web 應用程式。 **dependsOn** 元素會告訴 Resource Manager 如何排序要部署的資源。

**serverFarmId** 屬性會使用 [resourceId](template-functions-resource.md#resourceid) 函式。 此函式會取得資源的唯一識別碼。 在此案例中，它會取得 App Service 方案的唯一識別碼。 Web 應用程式會與一個特定的 App Service 方案相關聯。

## <a name="deploy-template"></a>部署範本

使用 Azure CLI 或 Azure PowerShell 來部署範本。

如果您尚未建立資源群組，請參閱[建立資源群組](template-tutorial-create-first-template.md#create-resource-group)。 此範例假設您已將 **templateFile** 變數設為範本檔案的路徑，如[第一個教學課程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要執行此部署命令，您必須擁有[最新版](/cli/azure/install-azure-cli)的 Azure CLI。

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> 如果部署失敗，請使用 **verbose** 參數來取得所建立資源的相關資訊。 使用 **debug** 參數來取得更多資訊以進行偵錯。

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個教學課程，則不需要刪除資源群組。

如果您現在要停止，則可能想要刪除資源群組來清除您部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

您已了解如何使用快速入門範本進行範本開發。 在下一個教學課程中，您會將標記新增至資源。

> [!div class="nextstepaction"]
> [新增標記](template-tutorial-add-tags.md)
