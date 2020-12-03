---
title: 教學課程 - 使用 Azure VPN 閘道建立和管理閘道
description: 遵循本教學課程，以了解如何使用 PowerShell 來建立、部署和管理 Azure VPN 閘道。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: aaeb38b4d46188205841d6a93437533e30061485
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512083"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>教學課程：使用 PowerShell 建立和管理 VPN 閘道

Azure VPN 閘道提供客戶組織內部與 Azure 之間的跨單位連線。 本教學課程將說明基本的 Azure VPN 閘道部署項目，例如建立和管理 VPN 閘道。 您會了解如何：

> [!div class="checklist"]
> * 建立 VPN 閘道
> * 檢視公用 IP 位址
> * 調整 VPN 閘道的大小
> * 重設 VPN 閘道

下圖顯示在本教學課程中建立的虛擬網路和 VPN 閘道。

:::image type="content" source="./media/vpn-gateway-tutorial-create-gateway-powershell/diagram.png" alt-text="VNet 和 VPN 閘道圖表":::

## <a name="prerequisites"></a>必要條件

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>一般網路參數值

以下是本教學課程使用的參數值。 在範例中，變數會轉譯為下列項目：

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

請根據您的環境和網路設定變更下列值，然後複製並貼上以設定本教學課程的變數。 如果您的 Cloud Shell 工作階段逾時，或您需要使用不同的 PowerShell 視窗，請變數複製並貼到您的新工作階段，再繼續進行教學課程。

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 必須先建立資源群組。 在下列範例中，會在 [美國東部]  區域中建立名為 *TestRG1* 的資源群組：

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>建立虛擬網路

Azure VPN 閘道可為您的虛擬網路提供跨單位連線和 P2S VPN 伺服器功能。 請將 VPN 閘道新增至現有的虛擬網路，或建立新的虛擬網路和閘道。 請注意，此範例會特別指定閘道子網路的名稱。 您必須一律將閘道子網路的名稱指定為 "GatewaySubnet"，它才能正常運作。 此範例會建立具有三個子網路 (Frontend、Backend 和 GatewaySubnet) 的新虛擬網路：其做法是使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 和 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)：

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>要求 VPN 閘道的公用 IP 位址

Azure VPN 閘道會透過網際網路與您的內部部署 VPN 裝置通訊，以執行 IKE (網際網路金鑰交換) 交涉並建立 IPsec 通道。 請依照下列範例的說明，使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 和 [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) 建立公用 IP 位址，並將其指派給您的 VPN 閘道：

> [!IMPORTANT]
> 您目前只能將動態公用 IP 位址用於該閘道。 Azure VPN 閘道不支援靜態 IP 位址。

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>建立 VPN 閘道

VPN 閘道建立作業可能需要花費 45 分鐘以上的時間。 閘道建立完成之後，您便可以在您的虛擬網路與另一個 VNet 之間建立連線。 或者，您可以在虛擬網路與內部部署位置之間建立連線。 請使用 [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) Cmdlet 來建立 VPN 閘道。

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

金鑰參數值：
* GatewayType：使用 **Vpn** 進行站對站和 VNet 對 VNet 連線
* VpnType：使用 **RouteBased** 與較大範圍的 VPN 裝置和較多路由功能互動
* GatewaySku：**VpnGw1** 是預設值；如果您需要更高的輸送量或更多連線，請將其變更為其他 VpnGw SKU。 如需詳細資訊，請參閱[閘道 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

如果您使用 TryIt，則工作階段可能會逾時。沒關係。 閘道仍會建立。

閘道建立完成之後，您便可以在您的虛擬網路與另一個 VNet 之間建立連線，或是在您的虛擬網路與內部部署位置之間建立連線。 您也可以設定從用戶端電腦到 VNet 的 P2S 連線。

## <a name="view-the-gateway-public-ip-address"></a>檢視閘道公用 IP 位址

如果您知道公用 IP 位址的名稱，請使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 來顯示指派給該閘道的公用 IP 位址。

如果您的工作階段逾時，請將本教學課程一開始的一般網路參數複製到新的工作階段，再繼續操作。

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>調整閘道器大小

您可以在閘道建立後變更 VPN 閘道 SKU。 不同的閘道 SKU 支援不同的規格，例如輸送量、連線數目等。下列範例會使用 [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) 將閘道的大小從 VpnGw1 調整為 VpnGw2。 如需詳細資訊，請參閱[閘道 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

雖然為 VPN 閘道調整大小的作業並 **不會** 干擾或移除現有的連線和組態，但也需要約 30 到 45 分鐘的時間。

## <a name="reset-a-gateway"></a>重設閘道

在執行疑難排解步驟時，您可以重設 Azure VPN 閘道，以強制 VPN 閘道重新啟動 IPsec/IKE 通道設定。 請使用 [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) 重設您的閘道。

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

如需詳細資訊，請參閱[重設 VPN 閘道](./reset-gateway.md)。

## <a name="clean-up-resources"></a>清除資源

如果您要繼續進行[下一個教學課程](./vpn-gateway-create-site-to-site-rm-powershell.md)，您可以保留這些資源，因為它們是必要條件。

但如果閘道屬於原型、測試或概念證明部署的一部分，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VPN 閘道和所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解基本的 VPN 閘道建立和管理，像是如何：

> [!div class="checklist"]
> * 建立 VPN 閘道
> * 檢視公用 IP 位址
> * 調整 VPN 閘道的大小
> * 重設 VPN 閘道

接著，繼續進行下列教學課程：

> [!div class="nextstepaction"]
> * [建立 S2S 連線](vpn-gateway-create-site-to-site-rm-powershell.md)