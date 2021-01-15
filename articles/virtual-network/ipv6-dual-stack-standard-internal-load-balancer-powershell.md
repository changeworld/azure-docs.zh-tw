---
title: 在 Azure 中使用標準內部 Load Balancer 部署 IPv6 雙重堆疊應用程式-PowerShell
titlesuffix: Azure Virtual Network
description: 本文說明如何使用 Azure Powershell，在 Azure 虛擬網路中使用標準內部 Load Balancer 部署 IPv6 雙重堆疊應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: 3df89b84e748f041f13866c1eb3c0b8a3341209c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220825"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>在 Azure 中使用 Standard Internal Load Balancer 部署 IPv6 雙重堆疊應用程式-PowerShell (Preview) 

本文說明如何在 Azure 中部署雙堆疊 (IPv4 + IPv6) 應用程式，其中包含雙重堆疊虛擬網路和子網、雙 (IPv4 + IPv6) 前端設定的標準內部 Load Balancer、具有雙重 IP 設定、網路安全性群組和公用 Ip 的 Nic 的 Vm。

建立具備 IPv6 功能的內部 Load Balancer 的程式與建立網際網路對應 IPv6 Load Balancer 的程式幾乎完全相同， [如下](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)所述。 建立內部負載平衡器的唯一差異在於前端設定，如下列 PowerShell 範例所示：

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "fd00:db8:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

導致上述內部負載平衡器前端設定的變更如下：
- `PrivateIpAddressVersion`指定為「IPv6」
- 已 `-PublicIpAddress` 省略或取代引數 `-PrivateIpAddress` 。 請注意，私人位址必須在將部署內部負載平衡器的子網 IP 空間範圍內。 如果省略靜態 `-PrivateIpAddress` ，則會從部署內部負載平衡器的子網中選取下一個可用的 IPv6 位址。
- 將在其中部署內部負載平衡器的雙堆疊子網會以 `-Subnet` 或 `-SubnetId` 引數指定。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組 6.9.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="prerequisites"></a>先決條件
在 Azure 中部署雙重堆疊應用程式之前，您必須使用下列 Azure PowerShell 設定此預覽功能的訂用帳戶：

註冊方式如下：
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
需要 30 分鐘才能完成功能註冊。 您可以執行下列 Azure PowerShell 命令以檢查註冊狀態：請檢查註冊，如下所示：
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
註冊完成之後，請執行下列命令：

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>建立資源群組

您必須先使用 [>new-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup)建立資源群組，才能建立雙重堆疊虛擬網路。 下列範例會在 *美國東部* 位置建立名為 *dsStd_ILB_RG* 的資源群組：

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>建立 IPv4 和 IPv6 公用 IP 位址
若要從網際網路存取您的虛擬機器，您需要 Vm 的 IPv4 和 IPv6 公用 IP 位址。 使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 建立公用 IP 位址。 下列範例會在 *dsStd_ILB_RG* 資源群組中建立名為 *RdpPublicIP_1* 和 *RdpPublicIP_2* 的 IPv4 和 IPv6 公用 IP 位址：

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>建立虛擬網路和子網

使用新的 [-new-azvirtualnetwork](/powershell/module/az.network/new-azvirtualnetwork) 搭配雙 stack a 子網設定，使用 [>new-azvirtualnetworksubnetconfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)來建立虛擬網路。 下列範例會使用 *dsSubnet* 建立名為 *dsVnet* 的虛擬網路。

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>建立標準負載平衡器

在本節中，您會為負載平衡器設定雙重前端 IP (IPv4 和 IPv6) 和後端位址集區，然後建立 Standard Load Balancer。

### <a name="create-front-end-ip"></a>建立前端 IP

使用 [New->new-azloadbalancerfrontendipconfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig)建立前端 IP。 下列範例會建立名為 *dsLbFrontEnd_v4* 和 *dsLbFrontEnd_v6* 的 IPV4 和 IPv6 前端 IP 設定：

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "fd00:db8:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>設定後端位址集區

使用 [New->new-azloadbalancerbackendaddresspoolconfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)來建立後端位址集區。 在其餘步驟中，VM 會連結至此後端集區。 下列範例會建立名為 *dsLbBackEndPool_v4* 的後端位址集區，並 *dsLbBackEndPool_v6* 以包含具有 IPV4 和 IPv6 NIC 配置的 vm：

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 若要確定只有狀況良好的 Vm 會接收流量，您可以選擇性地定義健康情況探查。 基本負載平衡器會使用 IPv4 探查來評估 Vm 上 IPv4 和 IPv6 端點的健康情況。 標準負載平衡器包含明確 IPv6 健康情況探查的支援。

使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 建立負載平衡器規則。 下列範例會建立名為 *dsLBrule_v4* 的負載平衡器規則，並 *dsLBrule_v6* ，並將 *TCP* 通訊埠 *80* 上的流量平衡至 IPv4 和 IPv6 前端 IP 設定：

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>建立負載平衡器

使用 [>new-azloadbalancer](/powershell/module/az.network/new-azloadbalancer)建立 Standard Load Balancer。 下列範例會使用 IPv4 和 IPv6 前端 IP 設定、後端集區，以及您在先前步驟中建立的負載平衡規則，建立名為 *myInternalLoadBalancer* 的公用 Standard Load Balancer：

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>建立網路資源
部署一些 Vm 並可以測試您的平衡器之前，您必須先建立支援的網路資源-可用性設定組、網路安全性群組和虛擬 Nic。 

### <a name="create-an-availability-set"></a>建立可用性設定組
若要改善應用程式的高可用性，請將您的 Vm 放在可用性設定組中。

使用 [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) 建立可用性設定組。 下列範例會建立名為 *dsAVset* 的可用性設定組：

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>建立網路安全性群組

針對將在您的 VNet 中管理輸入和輸出通訊的規則，建立網路安全性群組。

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>建立連接埠 3389 的網路安全性群組規則

使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 建立網路安全性群組規則，以允許透過連接埠 3389 的 RDP 連線。

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>建立連接埠 80 的網路安全性群組規則

使用 [>new-aznetworksecurityruleconfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)建立網路安全性群組規則，以允許透過埠80的網際網路連線。

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>建立網路安全性群組

使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 建立網路安全性群組。

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>建立 NIC

使用 [get-aznetworkinterface](/powershell/module/az.network/new-aznetworkinterface)建立虛擬 nic。 下列範例會建立兩個具有 IPv4 和 IPv6 設定的虛擬 Nic。 (您在下列步驟中針對應用程式建立的每部 VM 都有一個虛擬 NIC)。

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [Get-credential](/powershell/module/microsoft.powershell.security/get-credential) 來設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

現在您可以使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 建立 VM。 下列範例會建立兩個 VM 及必要的虛擬網路元件 (如果尚未存在)。

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 入口網站中查看 IPv6 雙重堆疊虛擬網路
您可以在 Azure 入口網站中看到 IPv6 雙重堆疊虛擬網路，如下所示：
1. 在入口網站的搜尋列中，輸入 *dsVnet*。
2. 當 **dsVnet** 出現在搜尋結果中時，請加以選取。 這會啟動名為 *dsVnet* 的雙重堆疊虛擬網路的 [**總覽**] 頁面。 雙重堆疊虛擬網路會顯示兩個 Nic，兩者皆位於名為 *dsSubnet* 的雙重堆疊子網中的 IPv4 和 IPv6 設定。

![具有標準內部 Load Balancer 的 IPv6 雙重堆疊虛擬網路](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> 適用于 Azure 虛擬網路的 IPv6 可在此預覽版本的唯讀 Azure 入口網站中使用。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>後續步驟

在本文中，您已建立具有雙重前端 IP 設定 (IPv4 和 IPv6) 的 Standard Load Balancer。 您也建立了兩部虛擬機器，其中包含雙 IP 設定 (IPV4 + IPv6) 的 Nic，並已新增至負載平衡器的後端集區。 若要深入瞭解 Azure 虛擬網路中的 IPv6 支援，請參閱 [什麼是 Azure 虛擬網路的 ipv6？](ipv6-overview.md)