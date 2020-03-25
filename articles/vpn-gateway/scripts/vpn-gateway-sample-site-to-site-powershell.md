---
title: Azure PowerShell 指令碼範例 - 設定站對站 VPN | Microsoft Docs
description: 設定站對站 VPN。
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/30/2018
ms.author: alzam
ms.openlocfilehash: 33a79461e1373657c366623e7ef5dbaf6aecf78f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "66113708"
---
# <a name="create-a-vpn-gateway-and-add-a-site-to-site-connection-using-powershell"></a>使用 PowerShell 建立 VPN 閘道並新增站對站連線

此指令碼會建立路由式 VPN 閘道，並新增站台對站組態。 為建立連線，您也需要設定 VPN 裝置。 如需詳細資訊，請參閱[關於 VPN 裝置和站對站 VPN 閘道連線的 IPsec/IKE 參數](../vpn-gateway-about-vpn-devices.md)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "10.0.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $BESubPrefix = "10.1.1.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "192.168.0.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $GWIPconfName = "gwipconf"
# Create a resource group
New-AzResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
 -Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1
# Create the local network gateway
New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
 -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
# Configure your on-premises VPN device
# Create the VPN connection
$gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
$local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
 -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
 -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前建立的資源時，請使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令刪除資源群組。 這會刪除資源群組及其包含的所有資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 新增子網路設定。 此組態可使用於虛擬網路建立程序。 |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 取得虛擬網路詳細資料。 |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | 取得虛擬網路閘道詳細資料。 |
| [Get-AzLocalNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | 取得本機網路閘道詳細資料。 |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得虛擬網路的子網路設定詳細資料。 |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 建立子網路組態。 此組態可使用於虛擬網路建立程序。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。 |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 建立新的閘道 IP 設定。 |
| [New-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgateway) | 建立 VPN 閘道。 |
| [New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway) | 建立區域網路閘道。 |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 建立站對站連線。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 設定虛擬網路的子網路設定。 |
| [Set-AzVirtualNetworkGateway](/powershell/module/az.network/set-azvirtualnetworkgateway) | 設定 VPN 閘道的設定。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。
