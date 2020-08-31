---
title: 快速入門：建立公用負載平衡器 - Azure PowerShell
titleSuffix: Azure Load Balancer
description: 本快速入門說明如何使用 Azure PowerShell 建立負載平衡器
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 3589aeb21053525e481f3448270d236265dd698e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815312"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立公用負載平衡器以平衡 VM 的負載

使用 Azure PowerShell 建立公用負載平衡器和三部虛擬機器，以開始使用 Azure Load Balancer。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已在本機安裝 Azure PowerShell 或 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組：

* 具名 **myResourceGroupLB**。
* 在 **eastus** 位置中。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**標準 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

## <a name="create-a-public-ip-address"></a>建立公用 IP 位址

若要在網際網路上存取您的 Web 應用程式，您需要負載平衡器的公用 IP 位址。 

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 來：

* 建立名為 **myPublicIP** 的標準區域冗餘公用 IP 位址。
* 在 **myResourceGroupLB**。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

若要在區域 1 中建立區域性公用 IP 位址，請使用下列命令：

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```

## <a name="create-standard-load-balancer"></a>建立標準負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

  * 前端 IP 集區，可接收負載平衡器上的連入網路流量。
  * 後端 IP 集區，前端集區在其中傳送負載平衡網路流量。
  * 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
  * 負載平衡器規則，可定義如何將流量分散至 VM。

### <a name="create-frontend-ip"></a>建立前端 IP

使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 建立前端 IP：

* 具名 **myFrontEnd**。
* 已連結至公用 IP **myPublicIP**。

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>設定後端位址集區

使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 建立後端位址集區： 

* 具名 **myBackendPool**。
* 在其餘步驟中，VM 會連結至此後端集區。

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>建立健康狀態探查

健全狀況探查會檢查所有虛擬機器執行個體，確認可以傳送網路流量。 

已從負載平衡器移除具有失敗探查檢查的虛擬機器。 解決失敗情況之後，系統會將虛擬機器新增回負載平衡器。

使用 [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) 建立健康情況探查：

* 監視虛擬機器的健康情況。
* 具名 **myHealthProbe**。
* 通訊協定 **TCP**。
* 監視**連接埠 80**。

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則定義：

* 傳入流量的前端 IP 設定。
* 接收流量的後端 IP 集區。
* 所需的來源和目的地連接埠。 

使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 建立負載平衡器規則： 

* 已命名為 **myHTTPRule**
* 接聽前端集區 **myFrontEnd** 中的**連接埠 80**。
* 使用**連接埠 80** 將負載平衡的網路流量傳送到後端位址集區 **myBackEndPool**。 
* 使用健康情況探查 **MyHealthProbe**。
* 通訊協定 **TCP**。

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT
```

### <a name="create-load-balancer-resource"></a>建立負載平衡器資源

使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 建立公用負載平衡器：

* 具名 **myLoadBalancer**
* 在 **eastus** 中。
* 在 **myResourceGroupLB** 資源群組中。

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network"></a>設定虛擬網路

請先建立支援的虛擬網路資源，才可部署 VM 並測試您的負載平衡器。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路：

* 具名 **myVNet**。
* 在 **myResourceGroupLB** 資源群組中。
* 名為 **MyBackendSubnet** 的子網路。
* 虛擬網路 **10.0.0.0/16**。
* 子網路 **10.0.0.0/24**。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>建立網路安全性群組
建立網路安全性群組，以定義虛擬網路的輸入連線。

#### <a name="create-a-network-security-group-rule-for-port-80"></a>建立連接埠 80 的網路安全性群組規則
使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 建立網路安全性群組規則：

* 具名 **myNSGRuleHTTP**。
* **允許 HTTP** 的描述。
* **允許**的存取權。
* 通訊協定 **(*)** 。
* 方向**輸入**。
* 優先順序 **2000**。
* **網際網路**的來源。
* **(*)** 的來源連接埠範圍。
* **(*)** 摸目的地位址前置詞。
* 目的地**連接埠 80**。

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>建立網路安全性群組

使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 建立網路安全性群組：

* 具名 **myNSG**。
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 使用在先前步驟中建立的安全性規則，儲存在變數中。

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

### <a name="create-network-interfaces"></a>建立網路介面

使用 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 建立三個網路介面：

#### <a name="vm-1"></a>VM 1

* 具名 **myNicVM1**。
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。
* 已連結至 **myBackEndPool** 中的負載平衡器 **myLoadBalancer**。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* 具名 **myNicVM2**。
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。
* 已連結至 **myBackEndPool** 中的負載平衡器 **myLoadBalancer**。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>VM 3

* 具名 **myNicVM3**。
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。
* 已連結至 **myBackEndPool** 中的負載平衡器 **myLoadBalancer**。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```
### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell
$cred = Get-Credential
```

使用下列項目建立虛擬機器：

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* 具名 **myVM1**。
* 在 **myResourceGroupLB** 資源群組中。
* 已連結至網路介面 **myNicVM1**。
* 已連結至負載平衡器 **myLoadBalancer**。
* 在 **區域 1** 中。
* 在 **eastus** 位置中。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* 具名 **myVM2**。
* 在 **myResourceGroupLB** 資源群組中。
* 連結到網路介面 **myNicVM2**。
* 已連結至負載平衡器 **myLoadBalancer**。
* 在 **區域 2** 中。
* 在 **eastus** 位置中。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

#### <a name="vm3"></a>VM3

* 具名 **myVM3**。
* 在 **myResourceGroupLB** 資源群組中。
* 連結到網路介面 **myNicVM3**。
* 已連結至負載平衡器 **myLoadBalancer**。
* 在 **區域 3** 中。
* 在 **eastus** 位置中。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '3'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

## <a name="create-outbound-rule-configuration"></a>建立輸出規則設定
負載平衡器輸出規則會在後端集區中設定 VM 的輸出來源網路位址轉譯 (SNAT)。 

如需輸出連線詳細資訊，請參閱 [Azure 中的輸出連線](load-balancer-outbound-connections.md)。

### <a name="create-outbound-public-ip-address"></a>建立輸出公用 IP 位址

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 來：

* 建立名為 **myPublicIPOutbound** 的標準區域冗餘公用 IP 位址。
* 在 **myResourceGroupLB**。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

若要在區域 1 中建立區域性公用 IP 位址，請使用下列命令：

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIPOutbound'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```
### <a name="create-outbound-frontend-ip-configuration"></a>建立輸出前端 IP 組態

使用 [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig) 建立新的前端 IP 設定：

* 具名 **myFrontEndOutbound**。
* 與公用 IP 位址 **myPublicIPOutbound** 相關聯。

```azurepowershell-interactive
## Variables for the command ##
$fen = 'myFrontEndOutbound'
$lbn = 'myLoadBalancer'

## Get the load balancer configuration  and apply the frontend config##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerFrontendIPConfig -Name $fen -PublicIpAddress $publicIP | Set-AzLoadBalancer
```

### <a name="create-outbound-pool"></a>建立輸出集區

使用 [Add-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig) 建立新的輸出集區。 

使用 [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer)，將集區和前端 IP 位址套用至負載平衡器：

* 具名 **myBackendPoolOutbound**。

```azurepowershell-interactive
## Variables for the command ##
$ben = 'myBackEndPoolOutbound'
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'

## Get the load balancer configuration and create the outbound backend address pool##
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg | Add-AzLoadBalancerBackendAddressPoolConfig -Name $ben | Set-AzLoadBalancer
```
### <a name="create-outbound-rule-and-apply-to-load-balancer"></a>建立輸出規則並套用至負載平衡器

使用 [Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig) 為輸出後端集區建立新的輸出規則。 

使用 [Set-AzLoadBalancerr](/powershell/module/az.network/set-azloadbalancer) 將規則套用至負載平衡器：

* 具名 **myOutboundRule**。
* 與負載平衡器 **myLoadBalancer** 相關聯。
* 與前端 **myFrontEndOutbound** 相關聯。
* 通訊協定**所有**。
* 閒置逾時 **15**。
* 輸出連接埠 **10000**。
* 與後端集區 **myBackEndPoolOutbound** 相關聯。
* 在 **myResourceGroupLB** 資源群組中。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$brn = 'myOutboundRule'
$pro = 'All'
$idl = '15'
$por = '10000'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg 

## Apply the outbound rule configuration to the load balancer. ##
$lb | Add-AzLoadBalancerOutBoundRuleConfig -Name $brn -FrontendIPConfiguration $lb.FrontendIpConfigurations[1] -BackendAddressPool $lb.BackendAddressPools[1] -Protocol $pro -IdleTimeoutInMinutes $idl -AllocatedOutboundPort $por | Set-AzLoadBalancer
```

### <a name="add-virtual-machines-to-outbound-pool"></a>將虛擬機器新增至輸出集區

使用 [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)，將虛擬機器網路介面新增至負載平衡器的輸出集區：


#### <a name="vm1"></a>VM1
* 在後端位址集區 **myBackEndPoolOutbound** 中。
* 在 **myResourceGroupLB** 資源群組中。
* 與網路介面 **myNicVM1** 和 **ipconfig1** 相關聯。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic1 = 'myNicVM1'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic1 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm2"></a>VM2
* 在後端位址集區 **myBackEndPoolOutbound** 中。
* 在 **myResourceGroupLB** 資源群組中。
* 與網路介面 **myNicVM2** 和 **ipconfig1** 相關聯。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic2 = 'myNicVM2'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic2 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface
```

#### <a name="vm3"></a>VM3
* 在後端位址集區 **myBackEndPoolOutbound** 中。
* 在 **myResourceGroupLB** 資源群組中。
* 與網路介面 **myNicVM3** 和 **ipconfig1** 相關聯。
* 與負載平衡器 **myLoadBalancer** 相關聯。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$lbn = 'myLoadBalancer'
$bep = 'myBackEndPoolOutbound'
$nic3 = 'myNicVM3'
$ipc = 'ipconfig1'

## Get the load balancer configuration ##
$lb = 
Get-AzLoadBalancer -Name $lbn -ResourceGroupName $rg

## Get the network interface configuration ##
$nic = 
Get-AzNetworkInterface -Name $nic3 -ResourceGroupName $rg

## Apply the backend to the network interface ##
$nic | Set-AzNetworkInterfaceIpConfig -Name $ipc -LoadBalancerBackendAddressPoolId $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id | Set-AzNetworkInterface

```

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

## <a name="create-a-public-ip-address"></a>建立公用 IP 位址

若要在網際網路上存取您的 Web 應用程式，您需要負載平衡器的公用 IP 位址。 

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 來：

* 建立名為 **myPublicIP** 的標準區域冗餘公用 IP 位址。
* 在 **myResourceGroupLB**。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Basic'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

## <a name="create-basic-load-balancer"></a>建立基本負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

  * 前端 IP 集區，可接收負載平衡器上的連入網路流量。
  * 後端 IP 集區，前端集區在其中傳送負載平衡網路流量。
  * 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
  * 負載平衡器規則，可定義如何將流量分散至 VM。

### <a name="create-frontend-ip"></a>建立前端 IP

使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 建立前端 IP：

* 具名 **myFrontEnd**。
* 已連結至公用 IP **myPublicIP**。

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>設定後端位址集區

使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 建立後端位址集區： 

* 具名 **myBackendPool**。
* 在其餘步驟中，VM 會連結至此後端集區。

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>建立健康狀態探查

健全狀況探查會檢查所有虛擬機器執行個體，確認可以傳送網路流量。 

已從負載平衡器移除具有失敗探查檢查的虛擬機器。 解決失敗情況之後，系統會將虛擬機器新增回負載平衡器。

使用 [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) 建立健康情況探查：

* 監視虛擬機器的健康情況。
* 具名 **myHealthProbe**。
* 通訊協定 **TCP**。
* 監視**連接埠 80**。

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則定義：

* 傳入流量的前端 IP 設定。
* 接收流量的後端 IP 集區。
* 所需的來源和目的地連接埠。 

使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 建立負載平衡器規則： 

* 已命名為 **myHTTPRule**
* 接聽前端集區 **myFrontEnd** 中的**連接埠 80**。
* 使用**連接埠 80** 將負載平衡的網路流量傳送到後端位址集區 **myBackEndPool**。 
* 使用健康情況探查 **MyHealthProbe**。
* 通訊協定 **TCP**。
* 使用前端 IP 位址啟用輸出來源網路位址轉譯 (SNAT)。


```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool
```

### <a name="create-load-balancer-resource"></a>建立負載平衡器資源

使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 建立公用負載平衡器：

* 具名 **myLoadBalancer**
* 在 **eastus** 中。
* 在 **myResourceGroupLB** 資源群組中。

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network"></a>設定虛擬網路

請先建立支援的虛擬網路資源，才可部署 VM 並測試您的負載平衡器。

### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路：

* 具名 **myVNet**。
* 在 **myResourceGroupLB** 資源群組中。
* 名為 **MyBackendSubnet** 的子網路。
* 虛擬網路 **10.0.0.0/16**。
* 子網路 **10.0.0.0/24**。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>建立網路安全性群組
建立網路安全性群組，以定義虛擬網路的輸入連線。

#### <a name="create-a-network-security-group-rule-for-port-80"></a>建立連接埠 80 的網路安全性群組規則
使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 建立網路安全性群組規則：

* 具名 **myNSGRuleHTTP**。
* **允許 HTTP** 的描述。
* **允許**的存取權。
* 通訊協定 **(*)** 。
* 方向**輸入**。
* 優先順序 **2000**。
* **網際網路**的來源。
* **(*)** 的來源連接埠範圍。
* **(*)** 摸目的地位址前置詞。
* 目的地**連接埠 80**。

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule2 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>建立網路安全性群組

使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 建立網路安全性群組：

* 具名 **myNSG**。
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 使用在先前步驟中建立的安全性規則，儲存在變數中。

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1,$rule2
```

### <a name="create-network-interfaces"></a>建立網路介面

使用 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 建立三個網路介面：

#### <a name="vm-1"></a>VM 1

* 具名 **myNicVM1**。
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。
* 已連結至 **myBackEndPool** 中的負載平衡器 **myLoadBalancer**。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* 具名 **myNicVM2**。
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。
* 已連結至 **myBackEndPool** 中的負載平衡器 **myLoadBalancer**。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>VM 3

* 具名 **myNicVM3**。
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。
* 已連結至 **myBackEndPool** 中的負載平衡器 **myLoadBalancer**。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>建立虛擬機器的可用性設定組

使用 [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) 建立虛擬機器的可用性設定組：

* 具名 **myAvSet**。
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'myResourceGroupLB'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell
$cred = Get-Credential
```

使用下列項目建立虛擬機器：

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* 具名 **myVM1**。
* 在 **myResourceGroupLB** 資源群組中。
* 已連結至網路介面 **myNicVM1**。
* 已連結至負載平衡器 **myLoadBalancer**。
* 在 **eastus** 位置中。
* 在 **myAvSet** 可用性設定組中。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* 具名 **myVM2**。
* 在 **myResourceGroupLB** 資源群組中。
* 連結到網路介面 **myNicVM2**。
* 已連結至負載平衡器 **myLoadBalancer**。
* 在 **eastus** 位置中。
* 在 **myAvSet** 可用性設定組中。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

#### <a name="vm3"></a>VM3

* 具名 **myVM3**。
* 在 **myResourceGroupLB** 資源群組中。
* 連結到網路介面 **myNicVM3**。
* 已連結至負載平衡器 **myLoadBalancer**。
* 在 **eastus** 位置中。
* 在 **myAvSet** 可用性設定組中。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

建立及設定這三部 VM 需要幾分鐘的時間。

---

## <a name="install-iis"></a>安裝 IIS

使用 [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension?view=latest) 來安裝自訂指令碼擴充功能。 

擴充功能會執行 PowerShell Add-WindowsFeature Web-Server 以安裝 IIS Web 伺服器，然後更新 Default.htm 頁面以顯示 VM 的主機名稱：

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm3"></a>VM3

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$enm = 'IIS'
$vmn = 'myVM3'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>測試負載平衡器

使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest) 取得負載平衡器的公用 IP 位址：

```azurepowershell-interactive
  ## Variables for command. ##
  $rg = 'myResourceGroupLB'
  $ipn = 'myPublicIP'
    
  Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ipn | select IpAddress
```

將公用 IP 位址複製並貼到您瀏覽器的網址列。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

   ![IIS Web 伺服器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

若要讓負載平衡器將流量分散到這三個 VM，您可以為每個 VM 的 IIS Web 伺服器自訂預設頁面，然後從用戶端機器強制重新整理您的網頁瀏覽器。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、負載平衡器和其餘資源。

```azurepowershell-interactive
## Variable for command. ##
$rg = 'myResourceGroupLB'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>後續步驟

在本快速入門中

* 您建立了標準或基本公用負載平衡器
* 連結的虛擬機器。 
* 設定了負載平衡器流量規則和健康情況探查。
* 測試了負載平衡器。

如需 Azure Load Balancer 的詳細資訊，請繼續參閱[什麼是 Azure Load Balancer？](load-balancer-overview.md)和 [Load Balancer 常見問題集](load-balancer-faqs.md)。

* 深入了解 [Load Balancer 和可用性區域](load-balancer-standard-availability-zones.md)。


