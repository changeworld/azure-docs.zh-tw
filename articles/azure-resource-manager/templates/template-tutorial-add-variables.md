---
title: 教學課程 - 將變數新增至範本
description: 將變數新增至 Azure Resource Manager 範本，以簡化語法。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 398b2741d7bafe4c3fdd9520d00eb7fcb766b876
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069606"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>教學課程：將變數新增至 ARM 範本

在本教學課程中，您將了解如何將變數新增至 Azure Resource Manager (ARM) 範本。 變數可讓您撰寫運算式一次，並在整個範本中重複使用它，藉以簡化您的範本。 完成此教學課程需要 **7 分鐘**。

## <a name="prerequisites"></a>Prerequisites

我們建議您完成[有關函式的教學課程](template-tutorial-add-functions.md)，但並非必要。

您必須擁有含 Resource Manager 工具延伸模組的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[範本工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>檢閱範本

在上一個教學課程結束時，您的範本會具有下列 JSON：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

儲存體帳戶名稱的參數很難使用，因為您必須提供唯一的名稱。 如果您已完成本系列中先前的教學課程，則可能已經厭倦猜測唯一的名稱。 您可以藉由新增可為儲存體帳戶建構唯一名稱的變數來解決此問題。

## <a name="use-variable"></a>使用變數

下列範例會反白顯示將變數新增至範本，以建立唯一儲存體帳戶名稱的變更。 複製整個檔案，並以其內容取代您的範本。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

請注意，它包含名為 **uniqueStorageName** 的變數。 此變數會使用四個函式來建構字串值。

您已經熟悉 [parameters](template-functions-deployment.md#parameters) 函式，因此我們將不會進行檢查。

您也很熟悉 [resourceGroup](template-functions-resource.md#resourcegroup) 函式。 在此情況下，您會取得 **id** 屬性，而不是 **location** 屬性，如先前的教學課程所示。 **id** 屬性會傳回資源群組的完整識別碼，包括訂用帳戶識別碼和資源群組名稱。

[uniqueString](template-functions-string.md#uniquestring) 函式會建立 13 個字元的雜湊值。 傳回的值取決於您傳入的參數。 在此教學課程中，您會使用資源群組識別碼作為雜湊值的輸入。 這表示您可以將此範本部署到不同的資源群組，並取得不同的唯一字串值。 不過，如果您部署至相同的資源群組，則會取得相同的值。

[concat](template-functions-string.md#concat) 函式會取得值，並將它們合併。 針對此變數，它會取得來自參數的字串和來自 uniqueString 函式的字串，並將它們合併成一個字串。

**storagePrefix** 參數可讓您傳入前置詞，以協助您識別儲存體帳戶。 您可以建立自己的命名慣例，以便在部署之後，更輕鬆地從一長串資源中識別儲存體帳戶。

最後，請注意，儲存體名稱現在會設定為變數，而不是參數。

## <a name="deploy-template"></a>部署範本

讓我們來部署範本。 部署此範本會比先前的範本更容易，因為您只需提供儲存體名稱的前置詞。

如果您尚未建立資源群組，請參閱[建立資源群組](template-tutorial-create-first-template.md#create-resource-group)。 此範例假設您已將 **templateFile** 變數設為範本檔案的路徑，如[第一個教學課程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要執行此部署命令，您必須擁有[最新版](/cli/azure/install-azure-cli)的 Azure CLI。

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> 如果部署失敗，請使用 **verbose** 參數來取得所建立資源的相關資訊。 使用 **debug** 參數來取得更多資訊以進行偵錯。

## <a name="verify-deployment"></a>驗證部署

您可以從 Azure 入口網站探索資源群組，藉以確認部署。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 從左側功能表中，選取 [資源群組]  。
1. 選取您已部署的目標資源群組。
1. 您會看到儲存體帳戶已經部署。 儲存體帳戶的名稱是 **store** 加上隨機字元的字串。

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個教學課程，則不需要刪除資源群組。

如果您現在要停止，則可能想要刪除資源群組來清除您部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已新增可為儲存體帳戶建立唯一名稱的變數。 在下一個教學課程中，您會從部署的儲存體帳戶傳回值。

> [!div class="nextstepaction"]
> [新增輸出](template-tutorial-add-outputs.md)
