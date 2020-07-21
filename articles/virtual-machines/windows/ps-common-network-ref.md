---
title: Azure 虛擬網路的常用 PowerShell 命令
description: 常用 PowerShell 命令，可讓您開始為 VM 建立虛擬網路與其相關聯的資源。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 5d1787b5bfc2939dca23c6898d89130aa3fcb170
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525750"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Azure 虛擬網路的常用 PowerShell 命令

如果您想要建立虛擬機器，您需要建立 [虛擬網路](../../virtual-network/virtual-networks-overview.md) 或了解可以加入 VM 的現有虛擬網路。 一般而言，當您建立 VM 時，您也需要考慮建立本文中所述的資源。

如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入帳戶的相關資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

如果您要執行本文中的多個命令，有些變數可能會相當實用：

- $location - 網路資源的位置。 您可以使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 來尋找適合您的[地理區域](https://azure.microsoft.com/regions/)。
- $myResourceGroup - 網路資源所在的資源群組名稱。

## <a name="create-network-resources"></a>建立網路資源

| Task | Command |
| ---- | ------- |
| 建立子網路組態 |$subnet1 = [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>一般網路可能有[網際網路面向負載平衡器](../../load-balancer/load-balancer-overview.md)的子網路和[內部負載平衡器](../../load-balancer/load-balancer-overview.md)的不同子網路。 |
| 建立虛擬網路 |$vnet = [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| 唯一網域名稱的測試 |[Test-AzDnsAvailability](/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>您可以指定[公用 IP 資源](../../virtual-network/public-ip-addresses.md)的 DNS 網域名稱，以建立 domainname.location.cloudapp.azure.com 與 Azure 受控 DNS 伺服器中的公用 IP 位址的對應。 名稱只能包含字母、數字和連字號。 第一個和最後一個字元必須是字母或數字，而網域名稱必須是其 Azure 位置內唯一的。 如果傳回 **True** ，表示您提出的名稱是全域唯一的。 |
| 建立公用 IP 位址 |$pip = [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>公用 IP 位址會使用您先前測試並由負載平衡器前端組態使用的網域名稱。 |
| 建立前端 IP 組態 |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>前端組態包含您先前針對連入網路流量建立的公用 IP 位址。 |
| 建立後端位址集區 |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>提供可透過網路介面存取的負載平衡器後端內部位址。 |
| 建立探查 |$healthProbe = [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>包含健康情況探查，可用來檢查後端位址集區中虛擬機器執行個體的可用性。 |
| 建立負載平衡規則 |$lbRule = [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>包含將負載平衡器上的公用連接埠指派至後端位址集區中的連接埠的規則。 |
| 建立輸入 NAT 規則 |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>包含將負載平衡器上的公用連接埠對應至後端位址集區中特定虛擬機器的連接埠的規則。 |
| 建立負載平衡器 |$loadBalancer = [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| 建立網路介面 |$nic1= [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>建立一個網路介面，其使用您先前建立的公用 IP 位址和虛擬網路子網路。 |

## <a name="get-information-about-network-resources"></a>取得關於網路資源的資訊

| Task | Command |
| ---- | ------- |
| 列出虛擬網路 |[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>列出資源群組中的所有虛擬網路。 |
| 取得虛擬網路的相關資訊 |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| 列出虛擬網路中的子網路 |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| 取得子網路的相關資訊 |[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>取得指定虛擬網路中的子網路相關資訊。 $Vnet 值代表 Get-AzVirtualNetwork 所傳回的物件。 |
| 列出 IP 位址 |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>列出資源群組中的公用 IP 位址。 |
| 列出負載平衡器 |[Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>列出資源群組中的所有負載平衡器。 |
| 列出網路介面 |[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>列出資源群組中的所有網路介面。 |
| 取得網路介面的相關資訊 |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>取得特定網路介面的相關資訊。 |
| 取得網路介面的 IP 組態 |[Get-AzNetworkInterfaceIPConfig](/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>取得指定網路介面的 IP 組態相關資訊。 $nic 值代表 Get-AzNetworkInterface 所傳回的物件。 |

## <a name="manage-network-resources"></a>管理網路資源

| Task | Command |
| ---- | ------- |
| 將子網路新增至虛擬網路 |[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>可將子網路新增現有虛擬網路。 $Vnet 值代表 Get-AzVirtualNetwork 所傳回的物件。 |
| 刪除虛擬網路 |[Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>從資源群組移除指定的虛擬網路。 |
| 刪除網路介面 |[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>從資源群組移除指定的網路介面。 |
| 刪除負載平衡器 |[Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>從資源群組移除指定的負載平衡器。 |
| 刪除公用 IP 位址 |[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>從資源群組移除指定的公用 IP 位址。 |

## <a name="next-steps"></a>後續步驟
使用您剛才[建立 VM](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json) 時所建立的網路介面。
