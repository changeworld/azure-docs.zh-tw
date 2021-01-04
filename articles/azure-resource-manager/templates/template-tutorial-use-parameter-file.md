---
title: 教學課程 - 使用參數檔案來部署範本
description: 使用包含用來部署 Azure Resource Manager 範本 (ARM 範本) 之值的參數檔。
author: mumian
ms.date: 09/10/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: b6257161017afc9dab692c43fcc64e5d961a90ba
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368421"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>教學課程：使用參數檔案部署您的 ARM 範本

在此教學課程中，您將了解如何使用[參數檔案](parameter-files.md)來儲存您在部署期間傳入的值。 在先前的教學課程中，您已將內嵌參數與部署命令搭配使用。 這種方法可用來測試 Azure Resource Manager 範本 (ARM 範本)，但在將部署自動化時，可以更輕易地為您的環境傳遞一組值。 參數檔案可讓您更輕鬆地為特定環境封裝參數值。 在此教學課程中，您將建立適用於開發和實際執行環境的參數檔案。 完成此教學課程大約需要 **12 分鐘**。

## <a name="prerequisites"></a>Prerequisites

我們建議您完成[有關標記的教學課程](template-tutorial-add-tags.md)，但並非必要。

您必須擁有含 Resource Manager 工具延伸模組的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[範本工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>檢閱範本

範本有許多您可在部署期間提供的參數。 在上一個教學課程結束時，您的範本看起來如下：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

此範本運作良好，但現在您想要輕鬆地管理您為範本傳入的參數。

## <a name="add-parameter-files"></a>新增參數檔案

參數檔案是 JSON 檔案，其結構類似於您的範本。 在檔案中，您會提供想要在部署期間傳入的參數值。

在參數檔案中，您可以在範本中提供參數的值。 參數檔案中每個參數的名稱都必須符合範本中參數的名稱。 名稱不區分大小寫，但若要輕鬆查看相符的值，建議您比對範本中的大小寫。

您不需要為每個參數提供值。 如果未指定的參數具有預設值，則會在部署期間使用該值。 如果參數沒有預設值，而且未在參數檔案中指定，系統就會提示您在部署期間提供值。

您不能在參數檔案中指定與範本中參數名稱不相符的參數名稱。 當提供未知的參數時，您會收到錯誤。

在 Visual Studio Code 中，建立含有下列內容的新檔案。 以 _azuredeploy.parameters.dev.json_ 名稱來儲存檔案。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

此檔案是適用於開發環境的參數檔案。 請注意，該檔案會針對儲存體帳戶使用 **Standard_LRS**、使用 **dev** 前置詞來為資源命名，並將 `Environment` 標記設定為 **Dev**。

同樣地，建立含有下列內容的新檔案。 以 _azuredeploy.parameters.prod.json_ 名稱來儲存檔案。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

此檔案是適用於實際執行環境的參數檔案。 請注意，該檔案會針對儲存體帳戶使用 **Standard_GRS**、使用 **contoso** 前置詞來為資源命名，並將 `Environment` 標記設定為 **Production**。 在真正的實際執行環境中，您也想要將應用程式服務與免費的 SKU 搭配使用，但我們將繼續使用該 SKU 來進行此教學課程。

## <a name="deploy-template"></a>部署範本

使用 Azure CLI 或 Azure PowerShell 來部署範本。

作為範本的最終測試，讓我們建立兩個新的資源群組。 一個用於開發環境，另一個用於實際執行環境。

針對範本和參數變數，將 `{path-to-the-template-file}`、`{path-to-azuredeploy.parameters.dev.json}`、`{path-to-azuredeploy.parameters.prod.json}` 和大括弧 `{}` 取代為您的範本和參數檔案路徑。

首先，我們將部署至開發環境。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要執行此部署命令，您必須擁有[最新版本](/cli/azure/install-azure-cli)的 Azure CLI。

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

現在，我們將部署至實際執行環境。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> 如果部署失敗，請使用 `verbose` 參數來取得所建立資源的相關資訊。 使用 `debug` 參數來取得更多資訊以進行偵錯。

## <a name="verify-deployment"></a>驗證部署

您可以從 Azure 入口網站探索資源群組，藉以確認部署。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 從左側功能表中，選取 [資源群組]  。
1. 您會看到兩個您在此教學課程中部署的新資源群組。
1. 選取任一個資源群組，並檢視已部署的資源。 請注意，它們會符合您在該環境之參數檔案中指定的值。

## <a name="clean-up-resources"></a>清除資源

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。 如果您已完成本系列，您會有三個要刪除的資源群組：**myResourceGroup**、**myResourceGroupDev** 和 **myResourceGroupProd**。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

恭喜，您已完成這個將範本部署至 Azure 的簡介。 如果您對於意見反應區段有任何意見和建議，請讓我們知道。 感謝您！

下一個教學課程系列將詳細說明如何部署範本。

> [!div class="nextstepaction"]
> [部署本機範本](./deployment-tutorial-local-template.md)
