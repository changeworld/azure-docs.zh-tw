---
title: 設定 ExpressRoute 和 S2S VPN 並存連接： Azure PowerShell
description: 使用 PowerShell 為 Resource Manager 模型設定可以並存的 ExpressRoute 和站對站 VPN 連線。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 5a7ac1b6a9f75655f7e07cc8af89b676ec611421
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905467"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>使用 PowerShell 設定 ExpressRoute 和站對站並存連線
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - 傳統](expressroute-howto-coexist-classic.md)
> 
> 

本文將協助您設定並存的 ExpressRoute 和站對站 VPN 連線。 能夠設定站對站 VPN 和 ExpressRoute 有諸多好處。 您可以將站對站 VPN 設定為 ExressRoute 的安全容錯移轉路徑，或使用站對站 VPN 來連線至不是透過 ExpressRoute 連線的網站。 本文中將說明設定這兩個案例的步驟。 本文適用於資源管理員部署模型。

設定站對站 VPN 和 ExpressRoute 並存連線有諸多好處：

* 您可以設定站對站 VPN 作為 ExpressRoute 的安全容錯移轉路徑。 
* 或者，您可以使用站對站 VPN 來連線到未透過 ExpressRoute 連線的網站。 

本文涵蓋設定這兩個案例的步驟。 本文適用於 Resource Manager 部署模型並使用 PowerShell。 您也可以使用 Azure 入口網站來設定這些案例，但目前尚未提供相關文件。 您可以先設定任一個閘道。 通常，在新增閘道或閘道連線時，將不會導致任何停機時間。

>[!NOTE]
>如果您想要建立透過 ExpressRoute 線路的站對站 VPN，請參閱[本文](site-to-site-vpn-over-microsoft-peering.md)。
>

## <a name="limits-and-limitations"></a>限制
* **不支援傳輸路由。** 您無法在透過站對站 VPN 連線的區域網路與透過 ExpressRoute 連線的區域網路之間，進行路由傳送 (透過 Azure)。
* **不支援基本 SKU 閘道。** 您必須對 [ExpressRoute 閘道](expressroute-about-virtual-network-gateways.md)和 [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)使用非基本 SKU 閘道。
* **僅支援路由式 VPN 閘道。** 您必須使用以路由為基礎的[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)。 您也可以使用路由式 VPN 閘道，並針對 [以原則為基礎的流量選取器] 設定 VPN 連線，如連線[到多個原則式 vpn 裝置](../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)中所述。
* **應該為 VPN 閘道設定靜態路由。** 如果您的區域網路連線到 ExpressRoute 和站對站 VPN，您必須在區域網路中設定靜態路由，才能將站對站 VPN 連線路由傳送到公用網際網路。
* **VPN 閘道若未指定會預設為 ASN 65515。** Azure VPN 閘道支援 BGP 路由通訊協定。 您可以加入 -Asn 參數，為虛擬網路指定 ASN (AS 號碼)。 若未指定此參數，預設 AS 號碼將是 65515。 您可以在組態中使用任何 ASN，但若選取 65515 以外的值，則必須重設閘道，設定才會生效。
* **閘道子網必須是/27 或更短的首碼**（例如/26、/25），否則當您新增 ExpressRoute 虛擬網路閘道時，將會收到錯誤訊息。

## <a name="configuration-designs"></a>組態設計
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>設定站對站 VPN 作為 ExpressRoute 的容錯移轉路徑
您可以設定站對站 VPN 連線作為 ExpressRoute 的備用連線。 此連線僅適用於連結至 Azure 私用對等路徑的虛擬網路。 對於可透過 Microsoft Azure 對等互連存取的服務，沒有以 VPN 為基礎的容錯移轉解決方案。 ExpressRoute 線路一律為主要連結。 只有在 ExpressRoute 線路故障時，資料才能流經站對站 VPN 路徑。 為了避免對稱路由，您的本機網路組態應該也偏好透過站對站 VPN 的 ExpressRoute 線路。 您可以針對接收 ExpressRoute 的路由設定較高的本機喜好設定，即表示偏好使用 ExpressRoute 路徑。 

> [!NOTE]
> 雖然當兩個路由相同時，ExpressRoute 線路偏好透過站對站 VPN，但 Azure 會使用最長的相符前置詞來選擇朝向封包目的地的路由。
> 
> 

![並存](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>設定站對站 VPN 來連線到未透過 ExpressRoute 連接的網站
您可以將網路設定成有些網站透過站對站 VPN 直接連線到 Azure，而有些網站透過 ExpressRoute 來連線。 

![並存](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> 您無法將虛擬網路設定為轉送路由器。
> 
> 

## <a name="selecting-the-steps-to-use"></a>選取要使用的步驟
有兩組不同的程序可供選擇。 您所選取的設定程序取決於您是已經有想要連線的現有虛擬網路，還是想要建立新的虛擬網路。

* 我沒有 VNet 且需要建立一個。
  
    如果您還沒有虛擬網路，這個程序將引導您使用 Resource Manager 部署模型來建立新的虛擬網路，並建立新的 ExpressRoute 和站對站 VPN 連線。 若要設定虛擬網路，請依照[建立新的虛擬網路和並存的連線](#new)中的步驟進行。
* 我已經有一個 Resource Manager 部署模型 VNet。
  
    您可能已經有虛擬網路，而且使用現有的站對站 VPN 連線或 ExpressRoute 連線。 在這個案例中，如果閘道子網路遮罩是 /28 或更小 (/28、/29 等等)，您就必須刪除現有的閘道。 [為已經存在的 VNet 設定並存的連線](#add)一節會引導您刪除閘道，然後建立新的 ExpressRoute 和站對站 VPN 連線。
  
    如果您刪除後重建閘道，您的跨單位連線將會停止。 不過，您的 VM 和服務仍能透過負載平衡器對外通訊，而您能夠在這兩者設定為這樣做時進行閘道器設定。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>建立新的虛擬網路和並存的連線
此程序會引導您建立會並存的 VNet、站對站和 ExpressRoute 連線。 您針對此組態使用的 Cmdlet 可能與您熟悉的 Cmdlet 有些微不同。 請務必使用這些指示中指定的 Cmdlet。

1. 登入並選取訂用帳戶。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. 設定變數。

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. 建立包含閘道子網路的虛擬網路。 如需有關建立虛擬網路的詳細資訊，請參閱[建立虛擬網路](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 如需有關建立子網路的詳細資訊，請參閱[建立子網路](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)。
   
   > [!IMPORTANT]
   > 閘道子網路必須是 /27 或更短的首碼 (例如 /26 或 /25)。
   > 
   > 
   
    建立新的 VNet。

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    新增子網路。

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    儲存 VNet 組態。

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>接下來，建立站對站 VPN 閘道。 如需 VPN 閘道組態的詳細資訊，請參閱[利用站對站連線設定 VNet](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)。 只有 VpnGw1**、VpnGw2**、VpnGw3**、Standard** 和 HighPerformance** VPN 閘道支援 GatewaySku。 基本 SKU 不支援 ExpressRoute-VPN 閘道共存組態。 VpnType 必須是 RouteBased**。

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    Azure VPN 閘道支援 BGP 路由通訊協定。 您可以為該虛擬網路指定 ASN (AS 號碼)，方法是在下列命令中新增 -Asn 參數。 未指定該參數，則會預設為 AS 號碼 65515。

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    您可以在 $azureVpn.BgpSettings.BgpPeeringAddress 和 $azureVpn.BgpSettings.Asn 中找到 Azure 用於 VPN 閘道的 BGP 對等互連 IP 和 AS 號碼。 如需詳細資訊，請參閱針對 Azure VPN 閘道[設定 BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)。
5. 建立本機的站台 VPN 閘道實體。 此命令不會設定內部部署 VPN 閘道。 相反地，它可讓您提供本機閘道器設定 (例如公用 IP 與內部位址空間)，使 Azure VPN 閘道能夠與其連線。
   
    如果您的本機 VPN 裝置只支援靜態路由，您可以利用下列方式設定靜態路由：

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    如果您的本機 VPN 裝置支援 BGP，而且您想要啟用動態路由，您必須本機 VPN 裝置所用的知道 BGP 對等互連 IP 和 AS 號碼。

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. 設定本機 VPN 裝置以連接到新的 Azure VPN 閘道。 如需關於 VPN 裝置組態的詳細資訊，請參閱 [VPN 裝置組態](../vpn-gateway/vpn-gateway-about-vpn-devices.md)。

7. 將 Azure 上的站對站 VPN 閘道連結至本機閘道器。

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. 如果您要連線到現有的 ExpressRoute 線路，請略過步驟 8 和 9 並跳至步驟 10。 設定 ExpressRoute 線路。 如需詳細資訊，請參閱[建立 ExpressRoute 路線](expressroute-howto-circuit-arm.md)。


9. 設定透過 ExpressRoute 線路的 Azure 私人對等互連。 如需設定透過 ExpressRoute 線路的 Azure 私人對等互連詳細資訊，請參閱[設定對等互連](expressroute-howto-routing-arm.md)

10. <a name="gw"></a>建立 ExpressRoute 閘道。 如需 ExpressRoute 閘道組態的詳細資訊，請參閱 [ExpressRoute 閘道組態](expressroute-howto-add-gateway-resource-manager.md)。 GatewaySKU 必須是 *Standard*、*HighPerformance* 或 *UltraPerformance*。

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. 將 ExpressRoute 閘道器連結到 ExpressRoute 電路。 完成這個步驟之後，內部部署網路和 Azure 之間的連線 (透過 ExpressRoute ) 便會建立。 如需連結作業的詳細資訊，請參閱 [將 Vnet 連結到 ExpressRoute](expressroute-howto-linkvnet-arm.md)。

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>為已經存在的 VNet 設定並存的連線
如果您的某個虛擬網路只有一個虛擬網路閘道 (比方說，站對站 VPN 閘道)，而您想要新增不同類型的另一個閘道 (比方說，ExpressRoute 閘道)，請檢查閘道子網路大小。 如果閘道器子網路是 /27 以上，您可以略過下列步驟，並依照上一節中的步驟新增站對站 VPN 閘道或 ExpressRoute 閘道。 如果閘道器子網路是/28 或/29，您必須先刪除虛擬網路閘道器，並增加閘道器子網路大小。 本節中的步驟示範如何執行該作業。

您針對此組態使用的 Cmdlet 可能與您熟悉的 Cmdlet 有些微不同。 請務必使用這些指示中指定的 Cmdlet。

1. 刪除現有的 ExpressRoute 或站對站 VPN 閘道。

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. 刪除閘道子網路。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. 新增 /27 或更大的閘道子網路。
   
   > [!NOTE]
   > 如果您的虛擬網路中沒有足夠的 IP 位址可以增加閘道器子網路大小，您必須新增更多 IP 位址空間。
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    儲存 VNet 組態。

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. 此時，您會有不具閘道的虛擬網路。 若要建立新的閘道並設定連接，請使用下列範例：

   設定變數。

    ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   ```

   建立閘道。

   ```azurepowershell-interactive
   $gw = New-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup> -Location <yourlocation> -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
   ```

   建立連線。

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
   New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
   ```

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>將點對站組態新增至 VPN 閘道

您可以在並存設定中，依照下列步驟來將點對站組態新增至您的 VPN 閘道。 若要上傳 VPN 根憑證，您必須在本機電腦上安裝 PowerShell，或使用 Azure 入口網站。

1. 新增 VPN 用戶端位址集區。

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. 針對您的 VPN 閘道將 VPN 根憑證上傳至 Azure。 在此範例中，假設根憑證是儲存在本機電腦，其中執行下列 PowerShell Cmdlet，而且您是在本機執行 PowerShell。 您也可以使用 Azure 入口網站上傳憑證。

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```

如需點對站 VPN 的詳細資訊，請參閱 [設定點對站連線](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)。

## <a name="next-steps"></a>後續步驟
如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
