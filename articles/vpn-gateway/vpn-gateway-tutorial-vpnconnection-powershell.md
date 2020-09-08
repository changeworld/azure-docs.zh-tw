---
title: Azure VPN 閘道：建立和管理 S2S VPN 連線：教學課程
description: 教學課程 - 使用 Azure PowerShell 模組建立和管理 Azure S2S VPN 連線
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 09/02/2020
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 9b712c1b2e8ac33ec189542bf6f60929ac59858d
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89420552"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>教學課程：使用 PowerShell 建立和管理 S2S VPN 連線

Azure S2S VPN 連線提供客戶組織內部與 Azure 之間的跨單位安全連線。 本教學課程將逐步解說 IPsec S2S VPN 連線的生命週期，例如建立和管理 S2S VPN 連線。 您會了解如何：

> [!div class="checklist"]
> * 建立 S2S VPN 連線
> * 更新連線屬性：預先共用的金鑰、BGP、IPsec/IKE 原則
> * 新增更多 VPN 連線
> * 刪除 VPN 連線

下圖顯示本教學課程的拓撲：

![端對端 VPN 連線圖表](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>使用 Azure Cloud Shell 和 Azure PowerShell

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="requirements"></a>需求

請完成第一個教學課程：[使用 Azure PowerShell 建立 VPN 閘道](vpn-gateway-tutorial-create-gateway-powershell.md)，以建立下列資源：

1. 資源群組 (TestRG1)、虛擬網路 (VNet1) 和 GatewaySubnet
2. VPN 閘道 (VNet1GW)

虛擬網路參數值如下所列。 請留意其他代表內部部署網路的區域網路閘道值。 請根據您的環境和網路設定變更下列值，然後複製並貼上以設定本教學課程的變數。 如果您的 Cloud Shell 工作階段逾時，或您需要使用不同的 PowerShell 視窗，請變數複製並貼到您的新工作階段，再繼續進行教學課程。

>[!NOTE]
> 如果您以此方式建立連線，請務必變更值以符合您的內部部署網路。 如果您只是在教學課程中執行這些步驟，則不需要進行變更，但連線將無法運作。
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

建立 S2S VPN 連線的工作流程很簡單：

1. 建立區域網路閘道以代表您的內部部署網路。
2. 建立 Azure VPN 閘道與區域網路閘道之間的連線

## <a name="create-a-local-network-gateway"></a>建立區域網路閘道

區域網路閘道代表您的內部部署網路。 您可以在區域網路閘道中指定內部部署網路的屬性，包括：

* VPN 裝置的公用 IP 位址
* 內部部署位址空間
* (選擇性) BGP 屬性 (BGP 對等 IP 位址和 AS 號碼)

使用 [New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway) 命令建立區域網路閘道。

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location $Location1 -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>建立 S2S VPN 連線

接著，使用 [New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) 在虛擬網路閘道與 VPN 裝置之間建立站對站 VPN 連線。 請注意，站對站 VPN 的 '-ConnectionType' 為 *IPsec*。

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3" -ConnectionProtocol IKEv2
```

如果您使用 BGP，請新增選用的 " **-EnableBGP $True**" 屬性，以啟用連線的 BGP。 此選項預設為停用。 參數 '-ConnectionProtocol' 為選用，IKEv2 為預設值。 您可以指定 **ConnectionProtocol IKEv1**，建立與 IKEv1 通訊協定的連線。

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>更新 VPN 連線預先共用的金鑰、BGP、IPsec/IKE 原則

### <a name="view-and-update-your-pre-shared-key"></a>檢視與更新您的預先共用金鑰

Azure S2S VPN 連線會使用預先共用的金鑰 (密碼) 在您的內部部署 VPN 裝置與 Azure VPN 閘道之間進行驗證。 您可以使用 [Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey) 和 [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey) 檢視及更新連線的預先共用金鑰。

> [!IMPORTANT]
> 預先共用的金鑰是一串長度不超過 128 的**可列印 ASCII 字元**。

此命令會顯示連線的預先共用金鑰：

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

上述範例的輸出將是 "**Azure\@!b2C3**"。 請使用下列命令將預先共用的金鑰值變更為 "**Azure\@!_b2=C3**"：

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>在 VPN 連線上啟用 BGP

Azure VPN 閘道支援 BGP 動態路由通訊協定。 您可以在個別的連線上啟用 BGP，這取決於您是否在內部部署網路與裝置上使用 BGP。 在連線上啟用 BGP 之前，請指定下列 BGP 屬性：

* Azure VPN ASN (自治號碼)
* 內部部署區域網路閘道 ASN
* 內部部署區域網路閘道 BGP 對等 IP 位址

如果您未設定 BGP 屬性，下列命令會將這些屬性新增至您的 VPN 閘道與區域網路閘道：[Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway) 和 [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway)。

使用下列範例來設定 BGP 屬性：

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

使用 [Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection) 啟用 BGP。

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

您可以將 "-EnableBGP" 屬性值變更為 **$False**，以停用 BGP。 請參閱 [Azure VPN 閘道上的 BGP](vpn-gateway-bgp-resource-manager-ps.md)，以深入了解 Azure VPN 閘道上的 BGP。

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>在連線上套用自訂 IPsec/IKE 原則

您可以套用選用的 IPsec/IKE 原則，以指定連線上的 IPsec/IKE 密碼編譯演算法和金鑰長度的確切組合，而不要使用[預設建議](vpn-gateway-about-vpn-devices.md#ipsec)。 下列範例指令碼會使用下列演算法和參數來建立不同的 IPsec/IKE 原則：

* IKEv2：AES256、SHA256、DHGroup14
* IPsec：AES128、SHA1、PFS14、SA 存留期 14,400 秒，和 102,400,000 KB

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

請參閱 [S2S 或 VNet 對 VNet 連線的 IPsec/IKE 原則](vpn-gateway-ipsecikepolicy-rm-powershell.md)，以取得演算法和指示的完整清單。

## <a name="add-another-s2s-vpn-connection"></a>新增另一個 S2S VPN 連線

將其他 S2S VPN 連線新增至相同的 VPN 閘道、建立另一個區域網路閘道，並在新的區域網路閘道與 VPN 閘道之間建立新的連線。 請使用下列範例，並確實修改變數以反映您自己的網路組態。

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

現在，您的 Azure VPN 閘道有兩個 S2S VPN 連線。

![MultiSite VPN 連線](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>刪除 S2S VPN 連線

使用 [Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection) 刪除 S2S VPN 連線。

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

如果您不再需要區域網路閘道，請加以刪除。 如果區域網路閘道有相關聯的其他連線，則無法刪除。

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>清除資源

如果此組態屬於原型、測試或概念證明部署的一部分，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VPN 閘道和所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立及管理 S2S VPN 連線，像是如何：

> [!div class="checklist"]
> * 建立 S2S VPN 連線
> * 更新連線屬性：預先共用的金鑰、BGP、IPsec/IKE 原則
> * 新增更多 VPN 連線
> * 刪除 VPN 連線

請繼續進行下列教學課程，以了解 S2S、VNet 對 VNet 和 P2S 連線。

> [!div class="nextstepaction"]
> * [建立 VNet 對 VNet 連線](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [建立 P2S 連線](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
