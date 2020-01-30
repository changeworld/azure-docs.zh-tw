---
title: 教學課程 - 將參數新增至範本
description: 將參數新增至 Azure Resource Manager 範本，以使其可重複使用。
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 89101a96f4fc228e2d5c45d67e10b52ac5d8aa11
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773196"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>教學課程：將參數新增至 Resource Manager 範本

在[上一個教學課程](template-tutorial-add-resource.md)中，您已了解如何將儲存體帳戶新增至範本並加以部署。 在此教學課程中，您將了解如何藉由新增參數來改善範本。 完成此教學課程大約需要 **14 分鐘**。

## <a name="prerequisites"></a>Prerequisites

我們建議您完成[有關資源的教學課程](template-tutorial-add-resource.md)，但並非必要。

您必須擁有含 Resource Manager 工具延伸模組的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[範本工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>檢閱範本

在上一個教學課程結束時，您的範本會具有下列 JSON：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

您可能已注意到此範本有問題。 儲存體帳戶名稱是硬式編碼的。 您每次只能使用此範本來部署相同的儲存體帳戶。 若要部署不同名稱的儲存體帳戶，您必須建立新的範本，這顯然不是將部署自動化的實用方式。

## <a name="make-template-reusable"></a>讓範本可重複使用

為了讓範本可重複使用，我們會新增可用來傳入儲存體帳戶名稱的參數。 下列範例中反白顯示的 JSON 會顯示範本中已變更的內容。 **storageName** 參數會識別為字串。 最大長度會設為 24 個字元，以避免任何名稱太長。

複製整個檔案，並以其內容取代您的範本。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>部署範本

讓我們來部署範本。 下列範例會使用 Azure CLI 或 PowerShell 來部署範本。 請注意，您會提供儲存體帳戶名稱作為部署命令中的其中一個值。 針對儲存體帳戶名稱，提供您在上一個教學課程中使用的相同名稱。

如果您尚未建立資源群組，請參閱[建立資源群組](template-tutorial-create-first-template.md#create-resource-group)。 此範例假設您已將 **templateFile** 變數設為範本檔案的路徑，如[第一個教學課程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>了解資源更新

在上一節中，您已使用稍早建立的相同名稱來部署儲存體帳戶。 您可能想知道重新部署會對資源產生何種影響。

如果資源已經存在，而且在屬性中偵測不到任何變更，則不會採取任何動作。 如果資源已經存在，而且屬性已變更，就會更新資源。 如果它不存在，即會加以建立。

這種處理更新的方式，表示您的範本可以包含 Azure 解決方案所需的所有資源。 您可以安全地重新部署範本，並了解資源只有在需要時才會變更或建立。 例如，若您已將檔案新增至儲存體帳戶，您可以重新部署儲存體帳戶，而不會遺失那些檔案。

## <a name="customize-by-environment"></a>依環境自訂

參數可提供針對特定環境量身訂做的值，讓您自訂部署。 例如，您可以根據是否要部署到可用來開發、測試和生產的環境，來傳遞不同的值。

先前的範本一律會部署 Standard_LRS 儲存體帳戶。 您可能想要彈性地根據環境來部署不同的 SKU。 下列範例顯示為 SKU 新增參數所做的變更。 複製整個檔案，並貼到您的範本上。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

**storageSKU** 參數有預設值。 當部署期間未指定值時，就會使用此值。 它也有一份允許的值清單。 這些值符合建立儲存體帳戶所需的值。 您不希望範本的使用者傳入無法使用的 SKU。

## <a name="redeploy-template"></a>重新部署範本

您已準備好再次進行部署。 因為預設 SKU 會設為 **Standard_LRS**，所以您不需要提供該參數的值。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

若要查看範本的彈性，讓我們再次進行部署。 這次將 SKU 參數設為 **Standard_GRS**。 您可以傳入新名稱來建立不同的儲存體帳戶，或使用相同名稱來更新現有的儲存體帳戶。 這兩個選項都可運作。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

最後，讓我們再執行一次測試，並查看當您傳入的 SKU 不是其中一個允許值時，會發生什麼事。 在此情況下，我們會測試範本的使用者認為 **basic** 是其中一個 SKU 的案例。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

此命令會立即失敗，並顯示會指出允許哪些值的錯誤訊息。 Resource Manager 會在部署開始之前識別錯誤。

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個教學課程，則不需要刪除資源群組。

如果您現在要停止，則可能想要刪除資源群組來清除您部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

您已藉由新增參數來改善在[第一個教學課程](template-tutorial-create-first-template.md)中建立的範本。 在下一個教學課程中，您將了解範本函式。

> [!div class="nextstepaction"]
> [新增範本函式](template-tutorial-add-functions.md)