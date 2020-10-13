---
title: 使用 Azure Resource Manager 範本 (ARM 範本) 建立 ExpressRoute 線路
description: 了解如何使用 Azure Resource Manager 範本 (ARM 範本) 建立 ExpressRoute 線路。
services: expressroute
author: duongau
mnager: kumud
ms.service: expressroute
ms.topic: quickstart
ms.custom: subject-armsq
ms.date: 08/31/2020
ms.author: duau
ms.openlocfilehash: f45fc32e99fff7403c40048b8c7ad117b0395cc9
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759232"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>快速入門：使用 ARM 範本建立具有私人對等互連的 ExpressRoute 線路

本快速入門說明如何使用 Azure Resource Manager 範本 (ARM 範本) 建立具有私人對等互連的 ExpressRoute 線路。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet)。

在本快速入門中，您將使用 Equinix 作為服務提供者來建立 ExpressRoute 線路。 此線路將會使用 Premium SKU，其頻寬為 50 Mbps，以及華盛頓特區的對等互連位置。 私人對等互連會分別在 *192.168.10.16/30* 和 *192.168.10.20/30* 的主要和次要子網路上啟用。 虛擬網路也會隨著 HighPerformance ExpressRoute 閘道一併建立。

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json" range="001-351" highlight="183-219":::

範本中已定義多個 Azure 資源：

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (用於在線路上啟用私人對等互連)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (網路安全性群組會套用至虛擬網路中的子網路)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks) 
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (ExpressRoute 閘道會使用公用 IP)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (ExpressRoute 閘道用於將 VNet 連結至線路)

若要尋找更多有關 ExpressRoute 的範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下程式碼區塊的 [試用] 以開啟 Azure Cloud Shell，然後遵循指示登入 Azure。 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等候直到您看見主控台的提示字元。

1. 從先前的程式碼區塊選取 [複製] 以複製 PowerShell 指令碼。

1. 以滑鼠右鍵按一下殼層主控台窗格，然後選取 [貼上]。

1. 輸入這些值。

    資源群組名稱是附加 **rg** 的專案名稱。

    部署範本需要約 20 分鐘。 完成時，輸出如下：

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="ExpressRoute Resource Manager 範本 PowerShell 部署輸出":::

Azure PowerShell 用於部署範本。 除了 Azure PowerShell 以外，您也可以使用 Azure 入口網站、Azure CLI 和 REST API。 若要了解其他部署方法，請參閱[部署範本](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>驗證部署

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取左側面板中的 [資源群組]。

1. 選取您在上一節中建立的資源群組。 預設的資源群組名稱是附加 **rg** 的專案名稱。

1. 資源群組應包含下列資源：

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="ExpressRoute Resource Manager 範本 PowerShell 部署輸出":::

1. 選取 ExpressRoute 線路 **er-ck01**，以確認線路狀態為 [已啟用]、提供者狀態為 [未佈建]，且私人對等互連的狀態為 [已佈建]。

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute Resource Manager 範本 PowerShell 部署輸出":::

> [!NOTE]
> 您必須先呼叫提供者才能完成佈建程序，才能將虛擬網路連結至線路。

## <a name="clean-up-resources"></a>清除資源

當您不再需要透過 ExpressRoute 線路建立的資源時，請刪除資源群組。 這會移除 ExpressRoute 線路和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立一個：
* ExpressRoute 線路
* 虛擬網路
* VPN 閘道
* 公用 IP
* 網路安全性群組

若要了解如何將虛擬網路連結至線路，請繼續進行 ExpressRoute 教學課程。

> [!div class="nextstepaction"]
> [ExpressRoute 教學課程](expressroute-howto-linkvnet-portal-resource-manager.md)
