---
title: 將 IPv4 應用程式升級到 Azure 虛擬網路中的 IPv6 - PowerShell
titlesuffix: Azure Virtual Network
description: 本文演示如何使用 Azure Powershell 將 IPv6 位址部署到 Azure 虛擬網路中的現有應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: c733538a4e730a95008a8ec1e4d50c20d6ce24ec
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420770"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>將 IPv4 應用程式升級到 Azure 虛擬網路中的 IPv6 - PowerShell

本文介紹如何在 Azure 虛擬網路中使用標準負載均衡器和公共 IP 的 Azure 虛擬網路中將 IPv6 連接添加到現有 IPv4 應用程式。 就地升級包括:
- 虛擬網路和子網路的 IPv6 位址空間
- 具有 IPv4 和 IPV6 前端設定的標準負載均衡器
- 具有同時具有 IPv4 和 IPv6 設定的 NIC 的 VM
- IPv6 公共 IP 使負載均衡器具有針對 Internet 的 IPv6 連接



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果選擇在本地安裝和使用 PowerShell,本文需要 Azure PowerShell 模組版本 6.9.0 或更高版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="prerequisites"></a>Prerequisites

本文假定部署了標準負載均衡器,如[快速入門:創建標準負載均衡器 - Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md)。

## <a name="retrieve-the-resource-group"></a>檢索資源組

在創建雙堆疊虛擬網路之前,必須使用[Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)檢索資源組。

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>建立 IPv6 IP 位址

使用[新 AzPublicIp 位址](/powershell/module/az.network/new-azpublicipaddress)為您的標準負載均衡器創建公共 IPv6 位址。 以下範例在*myResourceGroupSLB*資源群組中建立名為*PublicIP_v6*的 IPv6 公共 IP 位址:

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>設定負載均衡器前端

檢索現有負載均衡器配置,然後使用[Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig)新增新的 IPv6 IP 位址,如下所示:

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

## <a name="configure-load-balancer-backend-pool"></a>設定負載均衡器後端介面

在負載均衡器配置的本地副本上創建後端池,並更新正在運行的負載均衡器,並採用新的後端池配置,如下所示:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>設定負載平衡器規則
檢索現有的負載平衡器前端和後端介面配置,然後使用[Add-AzLoad平衡器RuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)添加新的負載平衡規則。

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

將 IPv6 位址範圍新增到承載 VM 的虛擬網路和子網,如下所示:

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
## <a name="add-ipv6-configuration-to-nic"></a>新增 IPv6 設定加入 NIC

使用[Add-AzNetwork 介面 IpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig)使用 IPv6 位址設定所有 VM NIC,如下所示:

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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路
您可以在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路,如下所示:
1. 在門戶的搜尋列中,輸入*myVnet*。
2. 當**myVnet**顯示在搜尋結果中時,請選擇它。 這將啟動名為*myVNet*的雙堆疊虛擬網路**的概述**頁面。 雙堆疊虛擬網路顯示三個 NIC,其中 IPv4 和 IPv6 配置都位於名為*mySubnet*的雙堆疊子網中。

  ![Azure 的 IPv6 雙堆疊虛擬網路](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)



## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>後續步驟

在本文中,您將具有 IPv4 前端 IP 配置的現有標準負載均衡器更新為雙堆疊(IPv4 和 IPv6) 配置。 您還可以將 IPv6 配置添加到後端池中的 VM NIC 和託管它們的虛擬網路。 要瞭解有關 Azure 虛擬網路中 IPv6 支援的更多資訊,請參閱[什麼是 Azure 虛擬網路的 IPv6?](ipv6-overview.md)
