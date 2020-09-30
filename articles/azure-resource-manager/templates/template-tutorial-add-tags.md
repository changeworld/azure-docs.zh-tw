---
title: 教學課程 - 將標記新增至範本中的資源
description: 將標記新增至您在 Azure Resource Manager 範本中部署的資源。 標記可讓您以邏輯方式組織資源。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: ace23e392d90e77c02df7861df96c066c82eee33
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069062"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>教學課程：在 ARM 範本中新增標記

在本教學課程中，您將了解如何將標記新增至 Azure Resource Manager (ARM) 範本中的資源。 [標記](../management/tag-resources.md)可協助您以邏輯方式組織資源。 標記值會顯示在成本報告中。 完成此教學課程需要 **8 分鐘**。

## <a name="prerequisites"></a>Prerequisites

我們建議您完成[有關快速入門範本的教學課程](template-tutorial-quickstart-template.md)，但並非必要。

您必須擁有含 Resource Manager 工具延伸模組的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[範本工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>檢閱範本

您先前的範本已部署儲存體帳戶、App Service 方案和 Web 應用程式。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

部署這些資源之後，您可能需要追蹤成本，並尋找屬於某個分類的資源。 您可以新增標記來協助解決這些問題。

## <a name="add-tags"></a>新增標記

您可以為資源加上標記，以新增可協助您識別其用法的值。 例如，您可以新增會列出環境和專案的標記。 您可以新增標記來識別成本中心或擁有該資源的小組。 新增任何對組織有意義的值。

下列範例會反白顯示對範本所做的變更。 複製整個檔案，並以其內容取代您的範本。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>部署範本

現在可以開始部署範本並查看結果。

如果您尚未建立資源群組，請參閱[建立資源群組](template-tutorial-create-first-template.md#create-resource-group)。 此範例假設您已將 **templateFile** 變數設為範本檔案的路徑，如[第一個教學課程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
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
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> 如果部署失敗，請使用 **verbose** 參數來取得所建立資源的相關資訊。 使用 **debug** 參數來取得更多資訊以進行偵錯。

## <a name="verify-deployment"></a>驗證部署

您可以從 Azure 入口網站探索資源群組，藉以確認部署。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 從左側功能表中，選取 [資源群組]  。
1. 選取您已部署的目標資源群組。
1. 選取其中一個資源，例如儲存體帳戶資源。 您會看到它現在具有標記。

   ![顯示標記](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個教學課程，則不需要刪除資源群組。

如果您現在要停止，則可能想要刪除資源群組來清除您部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已將標記新增至資源。 在下一個教學課程中，您將了解如何使用參數檔案來簡化將值傳入到範本的作業。

> [!div class="nextstepaction"]
> [使用參數檔案](template-tutorial-use-parameter-file.md)
