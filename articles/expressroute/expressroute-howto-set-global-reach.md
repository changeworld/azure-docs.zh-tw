---
title: Azure ExpressRoute：設定全球存取範圍
description: 本文可協助您將 ExpressRoute 線路連結在一起，在內部部署網路之間產生私人網路，並使觸角擴及全球。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.openlocfilehash: 2365317d83a4c11fa17cb5c449a25b70da17c2eb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368361"
---
# <a name="configure-expressroute-global-reach"></a>設定 ExpressRoute Global Reach

本文可協助您使用 PowerShell 設定 ExpressRoute Global Reach。 如需詳細資訊，請參閱 [ExpressRouteRoute Global Reach](expressroute-global-reach.md)。

 ## <a name="before-you-begin"></a>開始之前

在開始進行設定之前，請先確認下列情況︰

* 您瞭解 ExpressRoute 線路布建 [工作流程](expressroute-workflows.md)。
* 您的 ExpressRoute 線路處於已布建狀態。
* Azure 私用對等互連是在您的 ExpressRoute 線路上設定的。
* 如果您想要在本機執行 PowerShell，請確認您的電腦上已安裝最新版本的 Azure PowerShell。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>識別線路

1. 若要開始設定，請登入您的 Azure 帳戶，然後選取您想要使用的訂用帳戶。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. 識別您想要使用的 ExpressRoute 線路。 您可以在任何兩個 ExpressRoute 線路的私人對等互連之間啟用 ExpressRoute 全球存取，只要它們位於支援的國家/地區，且是在不同的對等互連位置建立。 

   * 如果兩個線路皆為您的訂用帳戶所擁有，則您可以選擇使用任一線路來執行下列各節中的設定。
   * 如果兩個線路在不同的 Azure 訂用帳戶中，您便需要從一個 Azure 訂用帳戶取得授權。 接著，您需在於另一個 Azure 訂用帳戶中執行設定命令時，傳入該授權金鑰。

## <a name="enable-connectivity"></a>啟用連線能力

啟用內部部署網路之間的連線能力。 相同 Azure 訂用帳戶中的線路有不同的指令組，以及不同訂用帳戶的線路。

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>相同 Azure 訂用帳戶中的 ExpressRoute 線路

1. 使用下列命令來取得線路 1 和線路 2。 這兩個線路位於相同的訂用帳戶中。

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. 針對線路 1 執行下列命令，並傳入線路 2 的私用對等互連的識別碼。 執行命令時，請注意下列事項：

   * 私用對等互連識別碼會類似以下範例： 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* 必須是 /29 IPv4 子網路，例如 "10.0.0.0/29"。 我們會使用此子網路中的 IP 位址，在兩個 ExpressRoute 線路之間建立連線。 您不應該在 Azure 虛擬網路或內部部署網路中，使用此子網路中的位址。

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. 儲存線路 1 上的設定，如下所示：

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

當先前的作業完成時，您的內部部署網路會透過兩個 ExpressRoute 線路連線到兩端。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>在不同 Azure 訂用帳戶中的 ExpressRoute 線路

如果兩個線路不在相同的 Azure 訂用帳戶中，您便需要授權。 在以下設定中，會在線路 2 訂用帳戶中產生授權，並將授權金鑰傳遞給線路 1。

1. 產生授權金鑰。

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   請記下線路 2 的私用對等互連識別碼，以及授權金鑰。
2. 針對線路 1 執行下列命令。 傳入線路 2 的私用對等互連識別碼及授權金鑰。

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. 在線路 1 上儲存設定。

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

當先前的作業完成時，您的內部部署網路會透過兩個 ExpressRoute 線路連線到兩端。

## <a name="verify-the-configuration"></a>驗證組態

使用下列命令來確認進行設定之線路 (例如上述範例中的線路 1) 上的設定。
```azurepowershell-interactive
$ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

如果您只是在 PowerShell 中執行 *$ckt _1* ，您會在輸出中看到 *>circuitconnectionstatus* 。 它會告訴您連線狀態是已建立 (已連線) 還是未建立 (已中斷連線)。 

## <a name="disable-connectivity"></a>停用連線

若要停用內部部署網路之間的連線，請針對進行設定的線路執行命令 (例如，上一個範例中的線路 1) 。

```azurepowershell-interactive
$ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

您可以執行 Get 作業以驗證狀態。

在上述作業完成後，您的內部部署網路之間便無法再透過 ExpressRoute 線路進行連線。

## <a name="next-steps"></a>後續步驟
1. [深入了解 ExpressRoute 觸角擴及全球](expressroute-global-reach.md)
2. [確認 ExpressRoute 連線能力](expressroute-troubleshooting-expressroute-overview.md)
3. [將 ExpressRoute 線路連結到 Azure 虛擬網路](expressroute-howto-linkvnet-arm.md)
