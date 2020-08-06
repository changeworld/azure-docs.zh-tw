---
title: 教學課程 - 將輸出新增至範本
description: 將輸出新增至 Azure Resource Manager 範本，以簡化語法。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4c90f1ce4bc3f3f5ed91bee86bb1f6f9093876c7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497778"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>教學課程：將輸出新增至 ARM 範本

在本教學課程中，您將了解如何從 Azure Resource Manager (ARM) 範本傳回值。 當您需要所部署資源的值時，可以使用輸出。 完成此教學課程需要 **7 分鐘**。

## <a name="prerequisites"></a>Prerequisites

我們建議您完成[有關變數的教學課程](template-tutorial-add-variables.md)，但並非必要。

您必須擁有含 Resource Manager 工具延伸模組的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[範本工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>檢閱範本

在上一個教學課程結束時，您的範本會具有下列 JSON：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

它會部署儲存體帳戶，但不會傳回任何有關儲存體帳戶的資訊。 您可能需要擷取新資源的屬性，以便稍後可供參考。

## <a name="add-outputs"></a>新增輸出

您可以使用輸出來從範本傳回值。 例如，可能有助於取得新儲存體帳戶的端點。

下列範例會反白顯示對範本所做以新增輸出值的變更。 複製整個檔案，並以其內容取代您的範本。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

關於您新增的輸出值，有一些需注意的重要項目。

傳回值的類型會設定為 **object**，這表示它會傳回 JSON 物件。

它會使用 [reference](template-functions-resource.md#reference) 函式，來取得儲存體帳戶的執行階段狀態。 若要取得資源的執行階段狀態，您會傳入資源的名稱或識別碼。 在此案例中，您會使用您用來建立儲存體帳戶名稱的相同變數。

最後，它會從儲存體帳戶傳回 **primaryEndpoints** 屬性

## <a name="deploy-template"></a>部署範本

您已經準備好部署範本，並查看傳回的值。

如果您尚未建立資源群組，請參閱[建立資源群組](template-tutorial-create-first-template.md#create-resource-group)。 此範例假設您已將 **templateFile** 變數設為範本檔案的路徑，如[第一個教學課程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要執行此部署命令，您必須擁有[最新版](/cli/azure/install-azure-cli)的 Azure CLI。

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

在部署命令的輸出中，只有在輸出是 JSON 格式時，才會看到類似下列範例的物件：

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> 如果部署失敗，請使用 **debug** 與部署命令切換，以顯示偵錯記錄。  您也可以使用 **verbose** 切換來顯示完整的偵錯記錄。

## <a name="review-your-work"></a>檢閱工作

您已在最後六個教學課程中完成許多作業。 讓我們花一點時間來檢閱您已完成的作業。 您已建立含易於提供之參數的範本。 此範本可在不同環境中重複使用，因為它允許自訂且可以動態方式建立所需的值。 它也會傳回您可在指令碼中使用之儲存體帳戶的相關資訊。

現在，讓我們查看資源群組和部署記錄。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 從左側功能表中，選取 [資源群組]  。
1. 選取您已部署的目標資源群組。
1. 根據您所執行的步驟而定，資源群組中應該至少有一個儲存體帳戶，且可能有數個。
1. 您也應該在記錄中列出數個成功的部署。 選取該連結。

   ![選取部署](./media/template-tutorial-add-outputs/select-deployments.png)

1. 您會在記錄中看到所有部署。 選取名為 **addoutputs** 的部署。

   ![顯示部署記錄](./media/template-tutorial-add-outputs/show-history.png)

1. 您可以檢閱輸入。

   ![顯示輸入](./media/template-tutorial-add-outputs/show-inputs.png)

1. 您可以檢閱輸出。

   ![顯示輸出](./media/template-tutorial-add-outputs/show-outputs.png)

1. 您可以檢閱範本。

   ![顯示範本](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個教學課程，則不需要刪除資源群組。

如果您現在要停止，則可能想要刪除資源群組來清除您部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已將傳回值新增至範本。 在下一個教學課程中，您將了解如何匯出範本，並在範本中使用該匯出範本的組件。

> [!div class="nextstepaction"]
> [使用匯出的範本](template-tutorial-export-template.md)
