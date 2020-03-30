---
title: Azure 快速路由範本：創建快速路由電路
description: 創建、預配、刪除和取消預配 ExpressRoute 電路。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 78da84a462566cca1a2800174849159ace8dd6dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981143"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>使用 Azure 資源管理器範本創建快速路由電路

> [!div class="op_single_selector"]
> * [Azure 門戶](expressroute-howto-circuit-portal-resource-manager.md)
> * [電源外殼](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure 資源管理器範本](expressroute-howto-circuit-resource-manager-template.md)
> * [視頻 - Azure 門戶](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [電源外殼（經典）](expressroute-howto-circuit-classic.md)
>

瞭解如何通過使用 Azure PowerShell 部署 Azure 資源管理器範本來創建 ExpressRoute 電路。 如需開發 Resource Manager 範本的詳細資訊，請參閱 [Resource Manager 文件](/azure/azure-resource-manager/)和[範本參考](/azure/templates/microsoft.network/expressroutecircuits)。

## <a name="before-you-begin"></a>開始之前

* 開始設定之前，請檢閱[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)。
* 確定您具有建立新網路資源的權限。 如果您沒有正確的權限，請連絡您的帳戶管理員。
* 您可以在開始前先[觀賞影片](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)，以便更加了解步驟。

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>建立和佈建 ExpressRoute 線路

[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)具有資源管理器範本的良好集合。 您可以使用[一個現有範本](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/)創建 ExpressRoute 電路。

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

要查看更多相關的範本，[請在此處](https://azure.microsoft.com/resources/templates/?term=expressroute)選擇 。

要通過部署範本創建快速路由電路，請：

1. 選取下列程式碼區塊中的 [試用]****，然後依照指示登入 Azure Cloud Shell。

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

   * **SKU 層**確定 ExpressRoute 電路是[本地](expressroute-faqs.md#expressroute-local)電路、標準電路還是[高級](expressroute-faqs.md#expressroute-premium)電路。 您可以指定*本地*、*標準*或*高級*。
   * **SKU 系列**確定計費類型。 您可以為按流量計費的通話方案指定*計量資料*，並為無限*通話方案指定無限資料*。 您可以將計費類型從 *Metereddata* 變更為 *Unlimiteddata*，但無法將類型從 *Unlimiteddata* 變更為 *Metereddata*。 *本地*電路僅是*無限資料*。
   * [對等位置]**** 是您與 Microsoft 對等互連的實體位置。

     > [!IMPORTANT]
     > [對等位置] 表示您與 Microsoft 對等互連的[實體位置](expressroute-locations.md)。 這**不會**連結到「位置」屬性，這是指 Azure 網路資源提供者所在的地理位置。 儘管它們並無關聯，但最好還是選擇地理位置靠近線路對等位置的網路資源提供者。

    資源組名稱是附加**rg**的服務匯流排命名空間名稱。

2. 選取 [複製]**** 來複製 PowerShell 指令碼。
3. 以滑鼠右鍵按一下殼層主控台，然後選取 [貼上]****。

建立事件中樞需要幾分鐘的時間。

Azure PowerShell 用於在本教程中部署範本。 有關其他範本部署方法，請參閱：

* [通過使用 Azure 門戶](../azure-resource-manager/templates/deploy-portal.md)。
* [通過使用 Azure CLI](../azure-resource-manager/templates/deploy-cli.md)。
* [通過使用 REST API](../azure-resource-manager/templates/deploy-rest.md)。

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>取消佈建和刪除 ExpressRoute 線路

您可以通過選擇**刪除**圖示來刪除您的 ExpressRoute 電路。 請注意下列資訊︰

* 您必須取消連結 ExpressRoute 線路的所有虛擬網路。 如果此操作失敗，請檢查您是否有任何虛擬網路連結至線路。
* 如果 ExpressRoute 電路服務提供者預配狀態為**預配**或**預配**，則必須與服務提供者合作，取消在其一側的電路預配。 我們會繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。
* 如果服務提供者已取消預配電路（服務提供者預配狀態設置為**未預配**），則可以刪除該電路。 這樣會停止針對線路計費。

您可以通過運行以下 PowerShell 命令來刪除您的 ExpressRoute 電路：

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>後續步驟

建立線路後，繼續執行下列步驟：

* [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-portal-resource-manager.md)
* [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)
