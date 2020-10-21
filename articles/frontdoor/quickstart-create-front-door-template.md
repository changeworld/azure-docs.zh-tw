---
title: 快速入門：使用 Azure Resource Manager 範本 (ARM 範本) 建立 Azure Front Door Service
description: 本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 來建立 Azure Front Door Service。
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: e7c3f2f50d9ac1fb1731f70f7b442ab4a2e44425
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088919"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>快速入門：使用 ARM 範本建立 Front Door

本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 來建立 Front Door，以設定 Web 端點的高可用性。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure]**** 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 網站或 Web 應用程式的 IP 或 FQDN。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-front-door-create-basic)。

在本快速入門中，您將使用單一後端以及符合 "/*" 的單一預設路徑來建立 Front Door 設定。 

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

此範本中已定義一項 Azure 資源：

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>部署範本

1. 選取以下程式碼區塊的 [試用]**** 以開啟 Azure Cloud Shell，然後遵循指示登入 Azure。 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等候直到您看見主控台的提示字元。

1. 從先前的程式碼區塊選取 [複製]**** 以複製 PowerShell 指令碼。

1. 以滑鼠右鍵按一下殼層主控台窗格，然後選取 [貼上]****。

1. 輸入這些值。

    範本部署會使用單一後端建立 Front Door。 在此範例中，會使用 *<span>microsoft.</span>com* 作為 **backendAddress**。

    資源群組名稱是附加 **rg** 的專案名稱。

    > [!NOTE]
    > **frontDoorName** 必須是全域唯一的名稱，才能成功部署範本。 如果部署失敗，請從步驟 1 開始。

    部署範本需要幾分鐘的時間。 完成時，輸出如下：

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Front Door Resource Manager 範本 PowerShell 部署輸出":::

Azure PowerShell 用於部署範本。 除了 Azure PowerShell 以外，您也可以使用 Azure 入口網站、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>驗證部署

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取左側面板中的 [資源群組]****。

1. 選取您在上一節中建立的資源群組。 預設的資源群組名稱是附加 **rg** 的專案名稱。

1. 選取您先前建立的 Front Door，然後按一下 [前端主機] 連結。 此連結會開啟網頁瀏覽器，將您重新導向至您在建立期間定義的後端 FQDN。

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Front Door Resource Manager 範本 PowerShell 部署輸出":::

## <a name="clean-up-resources"></a>清除資源

當您不再需要 Front Door 服務時，請刪除資源群組。 這會移除 Front Door 和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立一個：
* Front Door

若要了解如何將自訂網域新增至您的 Front Door，請繼續進行 Front Door 教學課程。

> [!div class="nextstepaction"]
> [Front Door 教學課程](front-door-custom-domain.md)
