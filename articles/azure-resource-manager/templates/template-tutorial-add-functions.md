---
title: 教學課程 - 新增範本函式
description: 將範本函式新增至 Azure Resource Manager 範本，以建構值。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe6185f0e7d6ee570c1491f3b21b6aebe4f090ae
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497557"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>教學課程：將範本函式新增至 ARM 範本

在本教學課程中，您將了解如何將[範本函式](template-functions.md)新增至 Azure Resource Manager (ARM) 範本。 您可以使用函式，以動態方式建構值。 除了這些系統提供的範本函式之外，您也可以建立[使用者定義的函式](./template-user-defined-functions.md)。 完成此教學課程需要 **7 分鐘**。

## <a name="prerequisites"></a>Prerequisites

我們建議您完成[有關參數的教學課程](template-tutorial-add-parameters.md)，但並非必要。

您必須擁有含 Resource Manager 工具延伸模組的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[範本工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>檢閱範本

在上一個教學課程結束時，您的範本會具有下列 JSON：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

儲存體帳戶的位置已硬式編碼為**美國東部**。 不過，您可能需要將儲存體帳戶部署到其他區域。 您會再次遇到範本缺乏彈性的問題。 您可以新增 location 的參數，但如果其預設值比硬式編碼的值更合理，那就太好了。

## <a name="use-function"></a>使用函式

如果您已完成本系列中的上一個教學課程，則您已經使用了函式。 當您新增 **"[parameters('storageName')]"** 時，使用了 [parameters](template-functions-deployment.md#parameters) 函式。 方括弧表示括弧內的語法是[範本運算式](template-expressions.md)。 Resource Manager 會解析語法，而不是將它視為常值。

函式可藉由在部署期間以動態方式取得值，來為範本增加彈性。 在此教學課程中，您會使用函式來取得要用於部署之資源群組的位置。

下列範例會反白顯示對新增名為 **location** 的參數所做的變更。  參數預設值會呼叫 [resourceGroup](template-functions-resource.md#resourcegroup) 函式。 此函式會傳回物件，其中包含用於部署之資源群組的相關資訊。 物件上的其中一個屬性是 location 屬性。 當您使用預設值時，儲存體帳戶位置會與資源群組的位置相同。 資源群組內的資源不需共用相同的位置。 您也可以視需要提供不同的位置。

複製整個檔案，並以其內容取代您的範本。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>部署範本

在先前的教學課程中，您已在美國東部建立儲存體帳戶，但您的資源群組是在美國中部建立的。 在此教學課程中，您的儲存體帳戶會建立於與資源群組相同的區域中。 使用 location 的預設值，如此就不需提供該參數值。 您必須為儲存體帳戶提供新名稱，因為您是在不同的位置建立儲存體帳戶。 例如，使用 **store2** 作為前置詞，而不是 **store1**。

如果您尚未建立資源群組，請參閱[建立資源群組](template-tutorial-create-first-template.md#create-resource-group)。 此範例假設您已將 **templateFile** 變數設為範本檔案的路徑，如[第一個教學課程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要執行此部署命令，您必須擁有[最新版](/cli/azure/install-azure-cli)的 Azure CLI。

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> 如果部署失敗，請使用 **debug** 與部署命令切換，以顯示偵錯記錄。  您也可以使用 **verbose** 切換來顯示完整的偵錯記錄。

## <a name="verify-deployment"></a>驗證部署

您可以從 Azure 入口網站探索資源群組，藉以確認部署。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 從左側功能表中，選取 [資源群組]  。
1. 選取您已部署的目標資源群組。
1. 您會看到儲存體帳戶資源已部署，並具有與資源群組相同的位置。

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個教學課程，則不需要刪除資源群組。

如果您現在要停止，則可能想要刪除資源群組來清除您部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已在定義參數的預設值時使用函式。 在此教學課程系列中，您將繼續使用函式。 在本系列結束時，您會將函式新增至範本的每個區段。

> [!div class="nextstepaction"]
> [新增變數](template-tutorial-add-variables.md)
