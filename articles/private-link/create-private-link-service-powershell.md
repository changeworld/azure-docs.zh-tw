---
title: 使用 Azure PowerShell 建立 Azure 私人連結服務 |Microsoft Docs
description: 瞭解如何使用 Azure PowerShell 建立 Azure 私人連結服務
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76932087"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>使用 Azure PowerShell 建立私人連結服務
本文說明如何使用 Azure PowerShell 在 Azure 中建立私人連結服務。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本文需要最新的 Azure PowerShell 模組版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

您必須先使用[remove-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)來建立資源群組，才能建立私人連結。 下列範例會在*WestCentralUS*位置中建立名為*myResourceGroup*的資源群組：

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>建立虛擬網路
使用[new-azvirtualnetwork](/powershell/module/az.network/new-azvirtualnetwork)為您的私用連結建立虛擬網路。 下列範例會建立名為*myvnet*的虛擬網路，其中包含適用于前端（*frontendSubnet*）、後端（*backendSubnet*）、私用連結（*otherSubnet*）的子網：

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
## <a name="create-internal-load-balancer"></a>建立內部 Load Balancer
使用[remove-azloadbalancer](/powershell/module/az.network/new-azloadbalancer)建立內部 Standard Load Balancer。 下列範例會使用您在先前步驟中建立的前端 IP 設定、探查、規則和後端集區，建立內部 Standard Load Balancer：

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
使用[AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer)建立私人連結服務。  這個範例會使用名為*myResourceGroup*的資源群組中的 Standard Load Balancer，建立名為*myPLS*的私用連結服務。 
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

### <a name="get-private-link-service"></a>取得私用連結服務
使用[AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice)取得私人連結服務的詳細資料，如下所示：

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

在此階段，您的私用連結服務已成功建立，並已準備好接收流量。 請注意，上述範例只是為了示範如何使用 PowerShell 建立私用連結服務。  我們尚未設定負載平衡器後端集區或後端集區上的任何應用程式來接聽流量。 如果您想要查看端對端流量流程，強烈建議您將應用程式設定在您的標準負載平衡器後方。 

接下來，我們將示範如何使用 PowerShell 將此服務對應至不同 VNet 中的私人端點。 同樣地，此範例僅限於建立私人端點，以及連接到上面所建立的私用連結服務。 您可以在虛擬網路中建立虛擬機器，以將流量傳送至私用端點，以建立您的案例。 

## <a name="create-a-private-endpoint"></a>建立私人端點
### <a name="create-a-virtual-network"></a>建立虛擬網路
使用[new-azvirtualnetwork](/powershell/module/az.network/new-azvirtualnetwork)為您的私用端點建立虛擬網路。 這個範例會在名為*myResourceGroup*的資源群組中建立名為 *vnetPE* 的虛擬網路：
 
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
針對在您的虛擬網路中建立的取用私人連結服務，建立私用端點：
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>取得私用端點
使用`Get-AzPrivateEndpoint`取得私人端點的 IP 位址，如下所示：

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>核准私人端點連接
使用「核准-AzPrivateEndpointConnection」來核准私人連結服務的私用端點連線。

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>後續步驟
- 深入瞭解[Azure 私人連結](private-link-overview.md)
 
