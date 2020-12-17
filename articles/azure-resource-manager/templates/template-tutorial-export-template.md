---
title: 教學課程 - 從 Azure 入口網站匯出範本
description: 了解如何使用匯出的範本來完成範本開發。
author: mumian
ms.date: 09/09/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ba1797da5a78eeebd25f5df1b6e37eb92470f584
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106915"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>教學課程：從 Azure 入口網站使用匯出的範本

在此教學課程系列中，您已建立範本來部署 Azure 儲存體帳戶。 在接下來的兩個教學課程中，您將會新增「App Service 方案」  和「網站」  。 您不需要從頭開始建立範本，而是要了解如何從 Azure 入口網站匯出範本，以及如何使用來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)的範例範本。 您可以基於自己的用途來自訂那些範本。 此教學課程著重於匯出範本，以及自訂範本的結果。 完成此教學課程大約需要 **14 分鐘**。

## <a name="prerequisites"></a>Prerequisites

我們建議您完成[有關輸出的教學課程](template-tutorial-add-outputs.md)，但並非必要。

您必須擁有含 Resource Manager 工具延伸模組的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[範本工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>檢閱範本

在上一個教學課程結束時，您的範本會具有下列 JSON：

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

此範本適用於部署儲存體帳戶，但您可能想要在其中新增更多資源。 您可以從現有資源匯出範本，以快速取得該資源的 JSON。

## <a name="create-app-service-plan"></a>建立 App Service 方案

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [建立資源]  。
1. 在 [搜尋 Marketplace]  中，輸入 **App Service 方案**，然後選取 [App Service 方案]  。  不要選取 [App Service 方案 (傳統)] 
1. 選取 [建立]  。
1. 輸入：

    - **訂用帳戶**：選取您的 Azure 訂用帳戶。
    - **資源群組**：選取 [新建]  ，然後指定名稱。 提供與您在此教學課程系列中所使用名稱不同的資源群組名稱。
    - **名稱**：輸入 App Service 方案的名稱。
    - **作業系統**：選取 [Linux]  。
    - **區域**：選取 Azure 位置。 例如，**美國中部**。
    - **定價層**：若要節省成本，請將 SKU 變更為 [基本 B1]  (在 [開發/測試 ] 底下)。

    ![Resource Manager 範本匯出範本入口網站](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. 選取 [檢閱及建立]  。
1. 選取 [建立]  。 建立資源需要一些時間。

## <a name="export-template"></a>匯出範本

1. 選取 [前往資源]  。

    ![前往資源](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. 選取 [匯出範本]  。

    ![Resource Manager 範本匯出範本](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   匯出範本功能會取得資源的目前狀態，並產生範本來進行部署。 匯出範本是快速取得部署資源所需之 JSON 的實用方式。

1. 在匯出的範本中尋找 `Microsoft.Web/serverfarms` 定義和參數定義。 您不需要複製這些區段。 您可以只使用此匯出的範本作為要如何將此資源新增至範本的範例。

    ![Resource Manager 範本匯出範本匯出的範本](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> 通常，匯出的範本會比您在建立範本時所需的更詳細。 例如，匯出範本中的 SKU 物件具有五個屬性。 此範本可以運作，但您可以只使用 `name` 屬性。 您可以從匯出的範本開始，然後視需要進行修改以符合您的需求。

## <a name="revise-existing-template"></a>修訂現有範本

匯出的範本會提供您所需的大部分 JSON，但您需要針對範本進行自訂。 請特別注意您的範本與匯出範本間參數和變數的差異。 很明顯地，匯出程式並不知道您已定義於範本中的參數和變數。

下列範例會反白顯示範本的新增項目。 其中包含匯出的程式碼加上一些變更。 首先，它會變更參數的名稱，以符合您的命名慣例。 其次，它會使用您的 location 參數來取得 App Service 方案的位置。 第三，其會移除預設值正常的部分屬性。

複製整個檔案，並以其內容取代您的範本。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>部署範本

使用 Azure CLI 或 Azure PowerShell 來部署範本。

如果您尚未建立資源群組，請參閱[建立資源群組](template-tutorial-create-first-template.md#create-resource-group)。 此範例假設您已將 `templateFile` 變數設為範本檔案的路徑，如[第一個教學課程](template-tutorial-create-first-template.md#deploy-template)所示。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要執行此部署命令，您必須擁有[最新版](/cli/azure/install-azure-cli)的 Azure CLI。

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> 如果部署失敗，請使用 `verbose` 參數來取得所建立資源的相關資訊。 使用 `debug` 參數來取得更多資訊以進行偵錯。

## <a name="verify-deployment"></a>驗證部署

您可以從 Azure 入口網站探索資源群組，藉以確認部署。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 從左側功能表中，選取 [資源群組]  。
1. 選取您已部署的目標資源群組。
1. 資源群組會包含儲存體帳戶和 App Service 方案。

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行下一個教學課程，則不需要刪除資源群組。

如果您現在要停止，則可能想要刪除資源群組來清除您部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

您已了解如何從 Azure 入口網站匯出範本，以及如何使用匯出的範本來進行範本開發。 您也可以使用 Azure 快速入門範本來簡化範本開發。

> [!div class="nextstepaction"]
> [使用 Azure 快速入門範本](template-tutorial-quickstart-template.md)
