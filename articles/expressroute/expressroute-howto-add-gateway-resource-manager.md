---
title: 教學課程 - Azure ExpressRoute：將閘道新增至 VNet - Azure PowerShell
description: 本教學課程會協助您使用 Azure PowerShell 將 VNet 閘道新增到已經建立的 ExpressRoute 的 Resource Manager VNet。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 9f01961ec7c7f8e0a4e2d72e28e6def50e93ad5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854302"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>教學課程：使用 PowerShell 為 ExpressRoute 設定虛擬網路閘道
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [傳統 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [影片 - Azure 入口網站](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

本教學課程會協助您為既存的 VNet 新增虛擬網路 (VNet) 閘道、調整該閘道大小及移除該閘道。 此設定的步驟適用於使用 Resource Manager 部署模型作為 ExpressRoute 設定所建立的 VNet。 如需詳細資訊，請參閱[關於 ExpressRoute 的虛擬網路閘道](expressroute-about-virtual-network-gateways.md)。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 建立閘道子網路。
> - 建立虛擬網路閘道。

## <a name="prerequisites"></a>必要條件

### <a name="configuration-reference-list"></a>設定參考清單

此工作的步驟會根據下列組態參考清單中的值來使用 VNet。 其他設定和名稱也會概述於這份清單中。 雖然我們會根據這份清單中的值加入變數，但不會在任何步驟中直接使用這份清單。 您可以複製清單以供參考，並使用您自己的值來取代其中的值。

| 設定                   | 值                                              |
| ---                       | ---                                                |
| 虛擬網路名稱 | *TestVNet* |    
| 虛擬網路位址空間 | *192.168.0.0/16* |
| 資源群組 | *TestRG* |
| Subnet1 名稱 | *FrontEnd* |   
| Subnet1 位址空間 | *192.168.1.0/24* |
| Subnet1 名稱 | *FrontEnd* |
| 閘道子網路名稱 | *GatewaySubnet* |    
| 閘道子網路位址空間 | *192.168.200.0/26* |
| 區域 | 美國東部 |
| 閘道名稱 | *GW* |   
| 閘道 IP 名稱 | *GWIP* |
| 閘道 IP 設定名稱 | *gwipconf* |
| 類型 | *ExpressRoute* |
| 閘道公用 IP 名稱  | *gwpip* |

## <a name="add-a-gateway"></a>新增閘道

1. 若要與 Azure 連線，請執行 `Connect-AzAccount`。

1. 宣告您在本練習中使用的變數。 請務必編輯範例，以反映您要使用的設定。

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. 將虛擬網路物件儲存為變數。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. 將閘道子網路加入至虛擬網路。 閘道子網路必須命名為 "GatewaySubnet"。 閘道子網路必須是 /27 或更大 (/26、/25 等等)。

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
1. 設定組態。

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. 將閘道子網路儲存為變數。

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. 要求公用 IP 位址。 建立閘道之前需要有 IP 位址。 您無法指定想要使用的 IP 位址；該 IP 位址會以動態方式指派。 下一個組態章節將使用此 IP 位址。 AllocationMethod 必須是動態的。

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. 建立適用於閘道的組態。 閘道器組態定義要使用的子網路和公用 IP 位址。 在此步驟中，您要指定在建立閘道將使用的組態。 使用下列範例來建立閘道組態。

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. 建立閘道。 在這個步驟中， **-GatewayType** 特別重要。 您必須使用值 **ExpressRoute**。 執行這些 Cmdlet 之後，閘道需要花費 45 分鐘或更久的時間來建立。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>確認已建立閘道
使用下列命令，確認已建立閘道：

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>調整閘道器大小
有幾個 [閘道 SKU](expressroute-about-virtual-network-gateways.md)。 您可以使用下列命令隨時變更閘道器 SKU。

> [!IMPORTANT]
> 此命令不適用於 UltraPerformance 閘道。 若要將您的閘道變更為 UltraPerformance 閘道，請先移除現有的 ExpressRoute 閘道，然後建立新的 UltraPerformance 閘道。 若要從 UltraPerformance 閘道降級您的閘道，請先移除 UltraPerformance 閘道，然後建立新的閘道。
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>清除資源
使用下列命令來移除閘道：

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>後續步驟
建立 VNet 閘道之後，您可以將 VNet 連結至 ExpressRoute 循環。 

> [!div class="nextstepaction"]
> [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)
