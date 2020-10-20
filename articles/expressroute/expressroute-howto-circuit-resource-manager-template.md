---
title: Azure ExpressRoute 範本：建立 ExpressRoute 線路
description: 瞭解如何使用 Azure PowerShell 部署 Azure Resource Manager 範本來建立 Azure ExpressRoute 線路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: duau
ms.openlocfilehash: 2e9b6ddc9da4467590946af12a47f1473a4ea494
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202050"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立 ExpressRoute 線路

> [!div class="op_single_selector"]
> * [Azure 入口網站](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager 範本](expressroute-howto-circuit-resource-manager-template.md)
> * [影片 - Azure 入口網站](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (傳統)](expressroute-howto-circuit-classic.md)
>

瞭解如何使用 Azure PowerShell 部署 Azure Resource Manager 範本來建立 ExpressRoute 線路。 如需開發 Resource Manager 範本的詳細資訊，請參閱 [Resource Manager 文件](../azure-resource-manager/index.yml)和[範本參考](/azure/templates/microsoft.network/expressroutecircuits)。

## <a name="before-you-begin"></a>開始之前

* 開始設定之前，請檢閱[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)。
* 確定您具有建立新網路資源的權限。 如果您沒有正確的權限，請連絡您的帳戶管理員。
* 您可以在開始前先[觀賞影片](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)，以便更加了解步驟。

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>建立和佈建 ExpressRoute 線路

[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/) 有良好的 Resource Manager 樣板集合。 您可以使用其中一個 [現有的範本](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) 來建立 ExpressRoute 線路。

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

若要查看更多相關的範本，請 [在這裡](https://azure.microsoft.com/resources/templates/?term=expressroute)選取。

若要透過部署範本來建立 ExpressRoute 線路：

1. 從下列程式碼區塊中選取 [ **試試看** ]，然後依照指示登入 Azure Cloud shell。

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **SKU 層** 會決定 ExpressRoute 線路為 [本機](expressroute-faqs.md#expressroute-local)、標準或 [Premium](expressroute-faqs.md#expressroute-premium)。 您可以指定「本機」、「*標準」或「進階」。 您無法將 SKU 從*標準/進階*變更為*本機*。
   * **SKU 系列** 決定計費類型。 您可以針對計量付費資料方案指定 *[metereddata* ，並針對無限制的資料方案指定 *Unlimiteddata* 。 您可以將計費類型從 *[metereddata* 變更為 *Unlimiteddata*，但無法將類型從 *Unlimiteddata* 變更為 *[metereddata*。 *本機*電路僅供*Unlimiteddata* 。
   * [對等位置]**** 是您與 Microsoft 對等互連的實體位置。

     > [!IMPORTANT]
     > [對等位置] 表示您與 Microsoft 對等互連的[實體位置](expressroute-locations.md)。 這**不會**連結到「位置」屬性，這是指 Azure 網路資源提供者所在的地理位置。 儘管它們並無關聯，但最好還是選擇地理位置靠近線路對等位置的網路資源提供者。

    資源組名是具有 **rg** 附加的服務匯流排命名空間名稱。

2. 選取 [複製] 來複製 PowerShell 指令碼。
3. 以滑鼠右鍵按一下殼層主控台，然後選取 [貼上]。

建立事件中樞需要幾分鐘的時間。

在本教學課程中，會使用 Azure PowerShell 來部署範本。 如需其他範本部署方法，請參閱：

* [使用 Azure 入口網站](../azure-resource-manager/templates/deploy-portal.md)。
* [使用 Azure CLI](../azure-resource-manager/templates/deploy-cli.md)。
* [使用 REST API](../azure-resource-manager/templates/deploy-rest.md)。

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>取消佈建和刪除 ExpressRoute 線路

您可以選取 **刪除** 圖示來刪除您的 ExpressRoute 線路。 請注意下列資訊︰

* 您必須取消連結 ExpressRoute 線路的所有虛擬網路。 如果此操作失敗，請檢查您是否有任何虛擬網路連結至線路。
* 如果 ExpressRoute 線路服務提供者佈建狀態為 **Provisioning** 或 **Provisioned**，您就必須與服務提供者一起合作，取消佈建他們那邊的線路。 我們會繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。
* 若服務提供者已取消佈建線路 (服務提供者佈建狀態設定為 [未佈建])，則可以刪除線路。 這樣會停止針對線路計費。

您可以執行下列 PowerShell 命令來刪除您的 ExpressRoute 線路：

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>後續步驟

建立線路後，繼續執行下列步驟：

* [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-portal-resource-manager.md)
* [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)