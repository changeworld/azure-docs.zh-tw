---
title: 使用 Azure 中的標準內部負載等化器部署 IPv6 雙堆疊應用程式 - PowerShell
titlesuffix: Azure Virtual Network
description: 本文演示如何使用 Azure Powershell 在 Azure 虛擬網路中使用標準內部負載等化器部署 IPv6 雙堆疊應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333361"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>使用 Azure - PowerShell 中的標準內部負載等化器部署 IPv6 雙堆疊應用程式（預覽版）

本文介紹如何在 Azure 中部署雙堆疊 （IPv4 + IPv6） 應用程式，該應用程式包括雙堆疊虛擬網路和子網、具有雙 （IPv4 + IPv6） 前端配置的標準內部負載等化器、具有雙 IP 的 NIC 的 VM配置、網路安全性群組和公共 IP。

> [!Important]
> 對 Azure 虛擬網路的 IPv6 支援當前處於公共預覽版中。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

創建支援 IPv6 的內部負載等化器的過程幾乎與創建面向 Internet 的 IPv6 負載等化器的過程[幾乎相同](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)。 創建內部負載等化器的唯一區別在前端配置中，如下 PowerShell 示例所示：

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

使上述內部負載等化器前端配置的更改包括：
- 被指定為`PrivateIpAddressVersion`"IPv6"
- 參數`-PublicIpAddress`已被省略或替換為`-PrivateIpAddress`。 請注意，專用位址必須位於將部署內部負載等化器的子網 IP 空間範圍內。 如果省略靜態`-PrivateIpAddress`，將從部署內部負載等化器的子網中選擇下一個可用 IPv6 位址。
- 將部署內部負載等化器的雙堆疊子網使用 a`-Subnet`或`-SubnetId`參數指定。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果選擇在本地安裝和使用 PowerShell，本文需要 Azure PowerShell 模組版本 6.9.0 或更高版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="prerequisites"></a>Prerequisites
在 Azure 中部署雙堆疊應用程式之前，必須使用以下 Azure PowerShell 配置此預覽功能的訂閱：

註冊如下：
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
需要 30 分鐘才能完成功能註冊。 您可以通過運行以下 Azure PowerShell 命令來檢查註冊狀態：按如下方式檢查註冊：
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
註冊完成之後，請執行下列命令：

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>建立資源群組

在創建雙堆疊虛擬網路之前，必須使用[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)創建資源組。 以下示例*在位於我們東部*的位置創建名為*dsStd_ILB_RG*的資源組：

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>創建 IPv4 和 IPv6 公共 IP 位址
要從 Internet 訪問虛擬機器，您需要 VM 的 IPv4 和 IPv6 公共 IP 位址。 使用[New-AzPublicIp 位址](/powershell/module/az.network/new-azpublicipaddress)創建公共 IP 位址。 下面的示例在*dsStd_ILB_RG*資源組中創建名為*RdpPublicIP_1*和*RdpPublicIP_2*的 IPv4 和 IPv6 公共 IP 位址：

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
## <a name="create-the-virtual-network-and-the-subnet"></a>創建虛擬網路和子網

使用具有雙堆疊的雙堆疊使用新阿茲虛擬網路子網配置使用[New-Az 虛擬網路](/powershell/module/az.network/new-azvirtualnetwork)[子網配置創建新](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)網路。 下面的示例創建一個虛擬網路，名為*dsVnet，* 帶有*dsSubnet*。

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>建立標準負載平衡器

在本節中，您可以為負載等化器配置雙前端 IP（IPv4 和 IPv6）和後端位址集區，然後創建標準負載等化器。

### <a name="create-front-end-ip"></a>建立前端 IP

使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 建立前端 IP。 以下示例創建名為*dsLbFrontEnd_v4*和*dsLbFrontEnd_v6*的 IPv4 和 IPv6 前端 IP 配置：

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>設定後端位址集區

使用[New-AzLoad平衡器Backend位址集區配置創建](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)後端位址集區。 在其餘步驟中，VM 會連結至此後端集區。 以下示例創建名為*dsLbBackEndPool_v4*和*dsLbBackEndPool_v6*的後端位址集區，以包括同時具有 IPV4 和 IPv6 NIC 配置的 VM：

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 為了確保只有健康的 VM 接收流量，可以選擇定義運行狀況探測。 基本負載等化器使用 IPv4 探頭來評估 VM 上的 IPv4 和 IPv6 端點的健康情況。 標準負載等化器包括對顯式 IPv6 運行狀況探頭的支援。

使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 建立負載平衡器規則。 以下示例創建名為*dsLBrule_v4*的負載等化器規則，並*dsLBrule_v6*並平衡*TCP*埠*80*上的流量到 IPv4 和 IPv6 前端 IP 配置：

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

使用[新負載平衡器](/powershell/module/az.network/new-azloadbalancer)創建標準負載等化器。 以下示例使用您在上述步驟中創建的 IPv4 和 IPv6 前端 IP 配置、後端池和負載平衡規則創建名為*myEndLoad平衡器*的公共標準負載等化器：

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
在部署某些 VM 並可以測試平衡器之前，必須創建支援的網路資源 - 可用性集、網路安全性群組和虛擬 NIC。 

### <a name="create-an-availability-set"></a>建立可用性設定組
要提高應用程式的高可用性，請將 VM 放在可用性集中。

使用 [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) 建立可用性設定組。 下面的示例創建名為*dsAVset*的可用性集：

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

為 VNet 中管理入站和出站通信的規則創建網路安全性群組。

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

創建網路安全性群組規則，允許通過埠 80 與[New-AzNetworkSecurityRule Config](/powershell/module/az.network/new-aznetworksecurityruleconfig)進行互聯網連接。

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

使用[新阿茲網介面](/powershell/module/az.network/new-aznetworkinterface)創建虛擬 NIC。 下面的示例創建兩個虛擬 NIC，同時使用 IPv4 和 IPv6 配置。 (您在下列步驟中針對應用程式建立的每部 VM 都有一個虛擬 NIC)。

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
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

使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 的系統管理員使用者名稱和密碼：

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
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路
您可以在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路，如下所示：
1. 在門戶的搜索欄中，輸入*dsVnet*。
2. 當**dsVnet**顯示在搜尋結果中時，請選擇它。 這將啟動名為*dsVnet*的雙堆疊虛擬網路**的概述**頁面。 雙堆疊虛擬網路顯示兩個 NIC，其中 IPv4 和 IPv6 配置都位於名為*dsSubnet*的雙堆疊子網中。

![IPv6 雙堆疊虛擬網路，配備標準內部負載等化器](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Azure 虛擬網路的 IPv6 在此預覽版本中以唯讀的 Azure 門戶提供。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>後續步驟

在本文中，您創建了具有雙前端 IP 配置（IPv4 和 IPv6）的標準負載等化器。 還創建了兩個虛擬機器，其中包括具有雙 IP 配置 （IPV4 + IPv6） 的 NIC，這些配置已添加到負載等化器的後端池中。 要瞭解有關 Azure 虛擬網路中 IPv6 支援的更多資訊，請參閱[什麼是 Azure 虛擬網路的 IPv6？](ipv6-overview.md)