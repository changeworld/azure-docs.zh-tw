---
title: 教學課程 - 將資源新增至範本
description: 描述建立第一個 Azure Resource Manager 範本的步驟。 您將了解範本檔案語法及部署儲存體帳戶的方式。
author: mumian
ms.date: 02/24/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: af571b6503f04c809b62c530f6d6254082b838be
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586677"
---
# <a name="tutorial-add-a-resource-to-your-resource-manager-template"></a>教學課程：將資源新增至 Resource Manager 範本

在[上一個教學課程](template-tutorial-create-first-template.md)中，您已了解如何建立空白範本並加以部署。 您現在已準備好要部署實際的資源。 在此教學課程中，您會新增儲存體帳戶。 完成此教學課程大約需要 **9 分鐘**。

## <a name="prerequisites"></a>Prerequisites

我們建議您完成[有關範本的簡介教學課程](template-tutorial-create-first-template.md)，但並非必要。

您必須擁有含 Resource Manager 工具延伸模組的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[範本工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="add-resource"></a>新增資源

若要將儲存體帳戶定義新增至現有範本，查看下列範例中反白顯示的 JSON。 請複製整個檔案，並以其內容取代您的範本，而不要嘗試複製範本的區段。

以唯一的儲存體帳戶名稱取代 **{provide-unique-name}** 。

> [!IMPORTANT]
> 儲存體帳戶名稱必須是 Azure 中是獨一無二的。 名稱必須只有小寫字母或數字。 名稱長度不得超過 24 個字元。 您可以嘗試使用 **store1** 作為前置詞，然後加上您的姓名縮寫和今天日期之類的命名模式。 例如，您使用的名稱看起來可能像 **store1abc09092019**。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

猜測儲存體帳戶的唯一名稱並不容易，而且不適合用於自動化大型部署。 在此教學課程系列稍後，您將使用範本功能更輕鬆地建立唯一名稱。

## <a name="resource-properties"></a>資源屬性

您可能想知道如何尋找要用於每個資源類型的屬性。 您可以使用 [Resource Manager 範本參考](/azure/templates/)來尋找想要部署的資源類型。

您部署的每個資源至少都有下列三個屬性：

- **類型**：資源類型。 此值是資源提供者的命名空間與資源類型的組合 (例如 Microsoft.Storage/storageAccounts)。
- **apiVersion**：要用來建立資源的 REST API 版本。 每個資源提供者都發佈了自己的 API 版本，所以此值是類型特有的。
- **名稱**：資源名稱。

大部分的資源也都有 **location** 屬性，此屬性會設定部署資源的區域。

其他屬性會因為資源類型和 API 版本而有所不同。 了解 API 版本與可用屬性之間的連線非常重要，因此，讓我們深入了解詳細資料。

在此教學課程中，您已將儲存體帳戶新增至範本。 您可以在 [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts) 上查看該 API 版本。 請注意，您並未將所有屬性新增至範本。 其中有許多選擇性的屬性。 Microsoft 儲存體資源提供者可以發行新的 API 版本，但您要部署的版本不需要變更。 您可以繼續使用該版本，並且知道部署的結果將會一致。

如果您檢視較舊的 API 版本 (例如 [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts))，您將會看到有較小的屬性集可供使用。

如果您決定要變更資源的 API 版本，請務必評估該版本的屬性，並適當地調整您的範本。

## <a name="deploy-template"></a>部署範本

您可以部署範本來建立儲存體帳戶。 為您的部署提供不同的名稱，讓您可以輕鬆地在記錄中找到它。

如果您尚未建立資源群組，請參閱[建立資源群組](template-tutorial-create-first-template.md#create-resource-group)。 此範例假設您已將 **templateFile** 變數設為範本檔案的路徑，如[第一個教學課程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

您可能遇到的兩個可能部署失敗：

- 錯誤：Code=AccountNameInvalid; Message={provide-unique-name} 不是有效的儲存體帳戶名稱。 儲存體帳戶名稱長度必須介於 3 到 24 個字元，而且只能使用數字和小寫字母。

    在範本中，以唯一的儲存體帳戶名稱取代 **{provide-unique-name}** 。  請參閱[新增資源](#add-resource)。

- 錯誤：Code=StorageAccountAlreadyTaken; Message=名稱為 store1abc09092019 的儲存體帳戶已在使用。

    在範本中，嘗試使用不同的儲存體帳戶名稱。

因為儲存體帳戶已建立，所以，此部署所花費的時間會比您的空白範本部署還要長。 這大約可能需要一分鐘，但通常更快。

## <a name="verify-deployment"></a>驗證部署

您可以從 Azure 入口網站探索資源群組，藉以確認部署。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 從左側功能表中，選取 [資源群組]  。
1. 選取您已部署的目標資源群組。
1. 您會看到儲存體帳戶已經部署。
1. 請注意，部署標籤現在會顯示：**部署：2 已成功**。

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個教學課程，則不需要刪除資源群組。

如果您現在要停止，則可能想要刪除資源群組來清除您部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

您已建立簡單的範本來部署 Azure 儲存體帳戶。  在後續的教學課程中，您會了解如何將參數、變數、資源和輸出新增至範本。 這些功能都是適用於更複雜範本的建置組塊。

> [!div class="nextstepaction"]
> [新增參數](template-tutorial-add-parameters.md)