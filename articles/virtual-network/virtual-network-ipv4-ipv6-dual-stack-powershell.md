---
title: 部署 IPv6 雙堆疊應用程式 - 基本負載均衡器 - PowerShell
titlesuffix: Azure Virtual Network
description: 本文演示如何使用 Azure Powershell 在 Azure 虛擬網路中部署 IPv6 雙堆疊應用程式。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3cfe80962c11e37c79549a74d7e4b19cd08f4684
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420911"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---powershell"></a>使用基本負載均衡器部署 IPv6 雙堆疊應用程式 - PowerShell

本文介紹如何使用 Azure PowerShell 使用基本負載均衡器部署雙堆疊 (IPv4 + IPv6) 應用程式,該應用程式包括雙堆疊虛擬網路和子網、具有雙 (IPv4 + IPv6) 前端配置的基本負載均衡器、具有雙 IP 配置的 NIC 的 VM、網路安全組和公共 IP。

要使用標準負載均衡器部署雙堆疊 (IPV4+ IPv6) 應用程式,請參閱[使用 Azure PowerShell 使用標準負載均衡器部署 IPv6 雙堆疊應用程式](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果選擇在本地安裝和使用 PowerShell,本文需要 Azure PowerShell 模組版本 6.9.0 或更高版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。


## <a name="create-a-resource-group"></a>建立資源群組

在創建雙堆疊虛擬網路之前,必須使用[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)創建資源組。 以下範例*在我們東部*位置建立名為*myRGDualStack*的資源組:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>建立 IPv4 與 IPv6 公共 IP 位址
要從 Internet 存取虛擬機器,您需要負載均衡器的 IPv4 和 IPv6 公共 IP 位址。 使用[New-AzPublicIp 位址](/powershell/module/az.network/new-azpublicipaddress)建立公共 IP 位址。 下面的範例在*dsRG1*資源群組中建立名為*dsPublicIP_v4*和*dsPublicIP_v6*的 IPv4 和 IPv6 公共 IP 位址:

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
要使用 RDP 連接存取虛擬機器,請使用[New-AzPublicIp 位址](/powershell/module/az.network/new-azpublicipaddress)為虛擬機器創建 IPV4 公共 IP 位址。

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>建立基本負載平衡器

在本節中,您可以為負載均衡器配置雙前端 IP(IPv4 和 IPv6)和後端位址池,然後創建基本負載均衡器。

### <a name="create-front-end-ip"></a>建立前端 IP

使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 建立前端 IP。 以下範例建立名為*dsLbFrontEnd_v4*和*dsLbFrontEnd_v6*的 IPv4 和 IPv6 前端 IP 設定:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>設定後端位址集區

使用[New-AzLoad平衡器Backend位址池配置創建](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)後端位址池。 在其餘步驟中，VM 會連結至此後端集區。 以下範例建立名為*dsLbBackEndPool_v4*和*dsLbBackEndPool_v6*的後端位址池,以包括同時具有 IPV4 和 IPv6 NIC 設定的 VM:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>建立健康狀態探查
使用[Add-AzLoad平衡器ProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig)創建運行狀況探測器來監視 VM 的運行狀況。
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 為了確保只有健康的 VM 接收流量,可以選擇定義運行狀況探測。 基本負載均衡器使用IPv4探頭來評估VM上的IPv4和IPv6端點的健康情況。 標準負載均衡器包括對顯式 IPv6 運行狀況探頭的支援。

使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 建立負載平衡器規則。 以下範例建立名為*dsLBrule_v4*的負載均衡器規則,並*dsLBrule_v6*並平衡*TCP*連接*埠 80*上的流量到 IPv4 和 IPv6 前端 IP 配置:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe
```

### <a name="create-load-balancer"></a>建立負載平衡器

使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 建立基本負載平衡器。 以下範例使用您在上述步驟中建立的 IPv4 和 IPv6 前端 IP 設定、後端池和負載平衡規則建立名為*myLoad平衡器*的公共基本負載平衡器:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>建立網路資源
在部署某些 VM 並可以測試平衡器之前,必須創建支援的網路資源 -可用性集、網路安全組、虛擬網路和虛擬 NIC。 
### <a name="create-an-availability-set"></a>建立可用性設定組
若要改善您應用程式的高可用性，請將 VM 放在可用性設定組中。

使用 [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) 建立可用性設定組。 下列範例會建立名為 myAvailabilitySet** 的可用性設定組：

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>建立網路安全性群組

為在 VNET 中管理入站和出站通信的規則創建網路安全組。

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>建立連接埠 3389 的網路安全性群組規則

使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 建立網路安全性群組規則，以允許透過連接埠 3389 的 RDP 連線。

```azurepowershell-interactive
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

創建網路安全組規則,允許透過埠 80 與[New-AzNetworkSecurityRule Config](/powershell/module/az.network/new-aznetworksecurityruleconfig)進行網路連接。

```azurepowershell-interactive
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

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路。 下列範例會建立名為 myVnet** 的虛擬網路和 mySubnet**：

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>建立 NIC

使用[新阿茲網介面](/powershell/module/az.network/new-aznetworkinterface)創建虛擬 NIC。 下面的範例創建兩個虛擬 NIC,同時使用 IPv4 和 IPv6 配置。 (您在下列步驟中針對應用程式建立的每部 VM 都有一個虛擬 NIC)。

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
      
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

現在您可以使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 建立 VM。 下列範例會建立兩個 VM 及必要的虛擬網路元件 (如果尚未存在)。 

```azurepowershell-interactive
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

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>確定 IPv4 與 IPv6 終結點的 IP 位址
獲取資源群組中的所有網路介面物件,以便用`get-AzNetworkInterface`匯總此部署中使用的 IP。 此外,使用取得 IPv4 和 IPv6 終結點的負載均衡`get-AzpublicIpAddress`器前端位址 。

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
下圖顯示了一個範例輸出,其中列出了兩個 VM 的專用 IPv4 和 IPv6 位址,以及負載均衡器的前端 IPv4 和 IPv6 IP 位址。

![Azure 中雙堆疊 (IPv4/IPv6) 應用程式部署的 IP 摘要](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路
您可以在 Azure 門戶中查看 IPv6 雙堆疊虛擬網路,如下所示:
1. 在門戶的搜尋列中,輸入*dsVnet*。
2. 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。 這將啟動名為*dsVnet*的雙堆疊虛擬網路**的概述**頁面。 雙堆疊虛擬網路顯示兩個 NIC,其中 IPv4 和 IPv6 配置都位於名為*dsSubnet*的雙堆疊子網中。

  ![Azure 的 IPv6 雙堆疊虛擬網路](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)


## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>後續步驟

在本文中,您創建了具有雙前端 IP 配置(IPv4 和 IPv6)的基本負載均衡器。 還創建了兩個虛擬機,其中包括具有雙 IP 配置 (IPV4 + IPv6) 的 NIC,這些配置已添加到負載均衡器的後端池中。 要瞭解有關 Azure 虛擬網路中 IPv6 支援的更多資訊,請參閱[什麼是 Azure 虛擬網路的 IPv6?](ipv6-overview.md)
