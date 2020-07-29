---
title: 在 Azure 中將 IPv4 應用程式升級至 IPv6 虛擬網路-PowerShell
titlesuffix: Azure Virtual Network
description: 本文說明如何使用 Azure Powershell，將 IPv6 位址部署至 Azure 虛擬網路中的現有應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 82c8b71c154336cf59610d8f0b99e70eccee266f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84688546"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>將 IPv4 應用程式升級至 Azure 虛擬網路中的 IPv6-PowerShell

本文說明如何使用 Standard Load Balancer 和公用 IP，將 IPv6 連線能力新增至 Azure 虛擬網路中的現有 IPv4 應用程式。 就地升級包括：
- 虛擬網路和子網的 IPv6 位址空間
- 具有 IPv4 和 IPV6 前端設定的 Standard Load Balancer
- 具有同時具有 IPv4 + IPv6 設定之 Nic 的 Vm
- IPv6 公用 IP，讓負載平衡器具有網際網路對向 IPv6 連線能力



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組 6.9.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="prerequisites"></a>必要條件

本文假設您已部署 Standard Load Balancer，如[快速入門：建立 Standard Load Balancer Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md)中所述。

## <a name="retrieve-the-resource-group"></a>取出資源群組

您必須先使用[remove-azresourcegroup](/powershell/module/az.resources/get-azresourcegroup)來取出資源群組，才能建立雙重堆疊虛擬網路。

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>建立 IPv6 IP 位址

使用[get-azpublicipaddress](/powershell/module/az.network/new-azpublicipaddress)為您的 Standard Load Balancer 建立公用 IPv6 位址。 下列範例會在*myResourceGroupSLB*資源群組中建立名為*PublicIP_v6*的 IPv6 公用 IP 位址：

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>設定負載平衡器前端

取出現有的負載平衡器設定，然後使用[new-azloadbalancerfrontendipconfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig)新增 IPv6 IP 位址，如下所示：

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>設定負載平衡器後端集區

在負載平衡器設定的本機複本上建立後端集區，並使用新的後端集區設定來更新執行中的負載平衡器，如下所示：

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>設定負載平衡器規則
取出現有的負載平衡器前端和後端集區設定，然後使用[add-azloadbalancerruleconfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)來新增負載平衡規則。

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>新增 IPv6 位址範圍

將 IPv6 位址範圍新增至虛擬網路和裝載 Vm 的子網，如下所示：

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>將 IPv6 設定新增至 NIC

使用[AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig)設定具有 IPv6 位址的所有 VM nic，如下所示：

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 入口網站中查看 IPv6 雙重堆疊虛擬網路
您可以在 Azure 入口網站中查看 IPv6 雙重堆疊虛擬網路，如下所示：
1. 在入口網站的搜尋列中，輸入*myVnet*。
2. 當**myVnet**出現在搜尋結果中時，請選取它。 這會啟動名為*myVNet*的雙重堆疊虛擬網路的 [**總覽**] 頁面。 雙重堆疊虛擬網路會顯示三個 Nic，其中 IPv4 和 IPv6 設定都位於名為*mySubnet*的雙重堆疊子網中。

  ![Azure 中的 IPv6 雙重堆疊虛擬網路](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)



## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>後續步驟

在本文中，您已將具有 IPv4 前端 IP 設定的現有 Standard Load Balancer 更新為雙重堆疊（IPv4 和 IPv6）設定。 您也會將 IPv6 設定新增至後端集區中 Vm 的 Nic，以及裝載它們的虛擬網路。 若要深入瞭解 Azure 虛擬網路中的 IPv6 支援，請參閱[什麼是適用于 azure 虛擬網路的 ipv6？](ipv6-overview.md)
