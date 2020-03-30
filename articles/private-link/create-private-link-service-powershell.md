---
title: 使用 Azure PowerShell* 創建 Azure 專用連結服務*微軟文檔
description: 瞭解如何使用 Azure PowerShell 創建 Azure 專用連結服務
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932087"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>使用 Azure PowerShell 創建專用連結服務
本文介紹如何使用 Azure PowerShell 在 Azure 中創建專用連結服務。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果選擇在本地安裝和使用 PowerShell，本文需要最新的 Azure PowerShell 模組版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

在創建專用連結之前，必須使用[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)創建資源組。 下面的示例*在 WestCentralUS*位置創建名為*myResourceGroup*的資源組：

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>建立虛擬網路
使用[New-Az虛擬網路](/powershell/module/az.network/new-azvirtualnetwork)為您的專用連結創建虛擬網路。 下面的示例創建一個名為*myvnet*的虛擬網路，該虛擬網路的子網用於前端（*前端子網*）、後端（*後端子網*）、專用連結（*其他子網*）：

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>創建內部負載平衡器
使用[新負載平衡器創建內部標準負載平衡器](/powershell/module/az.network/new-azloadbalancer)。 以下示例使用在前面的步驟中創建的前端 IP 配置、探測、規則和後端池創建內部標準負載等化器：

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>建立私人連結服務
使用[New-AzPrivateLink 服務創建專用連結服務](/powershell/module/az.network/new-azloadbalancer)。  本示例使用名為*myResourceGroup*的資源組中的標準負載等化器創建名為*myPLS*的專用連結服務。 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>獲取專用連結服務
通過[Get-AzPrivateLink 服務](/powershell/module/az.network/get-azprivatelinkservice)獲取有關您的私人連結服務的詳細資訊，如下所示：

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

在此階段，您的專用連結服務已成功創建，並準備好接收流量。 請注意，上述示例僅用於演示使用 PowerShell 創建專用連結服務。  我們尚未配置負載等化器後端池或後端池上的任何應用程式來偵聽流量。 如果要查看端到端流量，強烈建議您在標準負載等化器後面配置應用程式。 

接下來，我們將演示如何使用 PowerShell 將此服務映射到不同 VNet 中的專用終結點。 同樣，該示例僅限於創建專用終結點並連接到上面創建的專用連結服務。 您可以在虛擬網路中創建虛擬機器，以便向專用終結點發送/接收流量以構建方案。 

## <a name="create-a-private-endpoint"></a>建立私人端點
### <a name="create-a-virtual-network"></a>建立虛擬網路
使用[New-Az虛擬網路](/powershell/module/az.network/new-azvirtualnetwork)為您的專用終結點創建虛擬網路。 本示例在名為*myResourceGroup*的資源組中創建名為 *vnetPE* 的虛擬網路：
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>建立私人端點
創建專用終結點以使用上述在虛擬網路中創建的專用連結服務：
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>獲取專用終結點
獲取專用終結點的 IP 位址，`Get-AzPrivateEndpoint`如下所示：

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>批准專用終結點連接
使用"批准-AzPrivate終結點連接"批准與專用連結服務的專用端點連接。

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>後續步驟
- 瞭解有關[Azure 專用連結](private-link-overview.md)的更多
 
