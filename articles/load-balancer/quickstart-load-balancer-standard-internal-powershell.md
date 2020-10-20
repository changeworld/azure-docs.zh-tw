---
title: 快速入門：建立內部負載平衡器 - Azure PowerShell
titleSuffix: Azure Load Balancer
description: 本快速入門說明如何使用 Azure PowerShell 建立內部負載平衡器
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: ee7c1c57c271a6173f4ee978a10ff37526c04c33
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047840"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 來建立內部負載平衡器以平衡 VM 的負載

使用 Azure PowerShell 建立內部負載平衡器和二部虛擬機器，以開始使用 Azure Load Balancer。

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

## <a name="configure-virtual-network"></a>設定虛擬網路

請先建立支援的虛擬網路資源，才可部署 VM 並測試您的負載平衡器。

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>建立虛擬網路和 Azure Bastion 主機

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路：

* 具名 **myVNet**。
* 在 **myResourceGroupLB** 資源群組中。
* 名為 **MyBackendSubnet** 的子網路。
* 虛擬網路 **10.0.0.0/16**。
* 子網路 **10.0.0.0/24**。
* 子網路的名稱為 **AzureBastionSubnet**。
* 子網路 **10.0.1.0/24**。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>建立 Azure Bastion 主機的公用 IP 位址

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 建立堡壘主機的公用 IP 位址：

* 命名為 **myPublicIPBastion**
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 配置方法為 **static**。
* **標準** SKU。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>建立 Azure Bastion 主機

使用 [New-AzBastion](/powershell/module/az.network/new-azbastion) 建立堡壘主機：

* 命名為 **myBastion**。
* 在 **myResourceGroupLB** 資源群組中。
* 在虛擬網路 **MyVNet** 中。
* 與公用 IP 位址 **myPublicIPBastion** 相關聯。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

部署 Azure Bastion 主機需要幾分鐘的時間。

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

## <a name="create-standard-load-balancer"></a>建立標準負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

  * 前端 IP 集區，可接收負載平衡器上的連入網路流量。
  * 後端 IP 集區，前端集區在其中傳送負載平衡網路流量。
  * 健康狀態探查，可判斷後端 VM 執行個體的健康狀態。
  * 負載平衡器規則，可定義如何將流量分散至 VM。

### <a name="create-frontend-ip"></a>建立前端 IP

使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 建立前端 IP：

* 具名 **myFrontEnd**。
* 私人 IP 位址為 **10.0.0.4**。

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
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
>[!NOTE]
>後端集區中的虛擬機器將不會有使用此設定的輸出網際網路連線。 </br> 如需有關提供輸出連線的詳細資訊，請參閱： </br> **[Azure 中的輸出連線](load-balancer-outbound-connections.md)**</br> 提供連線的選項： </br> **[僅輸出負載平衡器組態](egress-only.md)** </br> **[什麼是虛擬網路 NAT？](https://docs.microsoft.com/azure/virtual-network/nat-overview)**


### <a name="create-load-balancer-resource"></a>建立負載平衡器資源

使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 建立內部負載平衡器：

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

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

## <a name="configure-virtual-network"></a>設定虛擬網路

請先建立支援的虛擬網路資源，才可部署 VM 並測試您的負載平衡器。

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>建立虛擬網路和 Azure Bastion 主機

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路：

* 具名 **myVNet**。
* 在 **myResourceGroupLB** 資源群組中。
* 名為 **MyBackendSubnet** 的子網路。
* 虛擬網路 **10.0.0.0/16**。
* 子網路 **10.0.0.0/24**。
* 子網路的名稱為 **AzureBastionSubnet**。
* 子網路 **10.0.1.0/24**。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>建立 Azure Bastion 主機的公用 IP 位址

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 建立堡壘主機的公用 IP 位址：

* 命名為 **myPublicIPBastion**
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 配置方法為 **static**。
* **標準** SKU。

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>建立 Azure Bastion 主機

使用 [New-AzBastion](/powershell/module/az.network/new-azbastion) 建立堡壘主機：

* 命名為 **myBastion**。
* 在 **myResourceGroupLB** 資源群組中。
* 在虛擬網路 **MyVNet** 中。
* 與公用 IP 位址 **myPublicIPBastion** 相關聯。

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'myResourceGroupLB'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

部署 Azure Bastion 主機需要幾分鐘的時間。


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

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
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
* 私人 IP 位址為 **10.0.0.4**。

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
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

## <a name="test-the-load-balancer"></a>測試負載平衡器

### <a name="create-network-interface"></a>建立網路介面

使用 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 建立網路介面：

#### <a name="mytestvm"></a>myTestVM

* 命名為 **myNicTestVM**。
* 在 **myResourceGroupLB** 資源群組中。
* 在 **eastus** 位置中。
* 在虛擬網路 **MyVNet** 中。
* 在子網路 **MyBackendSubnet** 中。
* 在網路安全性群組 **myNSG** 中。

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>建立虛擬機器

使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell
$cred = Get-Credential
```

使用下列方式建立虛擬機器：

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* 命名為 **myTestVM**。
* 在 **myResourceGroupLB** 資源群組中。
* 已連結至網路介面 **myNicTestVM**。
* 在 **eastus** 位置中。

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>測試

1. [登入](https://portal.azure.com) Azure 入口網站。

1. 在 [概觀] 畫面上尋找負載平衡器的私人 IP 位址。 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後選取 [myLoadBalancer]。

2. 在 **myLoadBalancer** 的**概觀**中，記下或複製**私人 IP 位址** 旁的位址。

3. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單選取 **myResourceGroupLB** 資源群組中的 [myTestVM]。

4. 在 [概觀] 頁面上，選取 [連線]，然後選 [Bastion]。

6. 輸入在 VM 建立期間輸入的使用者名稱和密碼。

7. 在 **myTestVM** 上開啟 **Internet Explorer**。

8. 在瀏覽器的網址列中，輸入上一個步驟中的 IP 位址。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="建立標準內部負載平衡器" border="true":::
   
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

* 您已建立 Azure 標準或基本內部負載平衡器
* 連結的虛擬機器。 
* 設定了負載平衡器流量規則和健康情況探查。
* 測試了負載平衡器。

若要深入了解 Azure Load Balancer，請繼續...
> [!div class="nextstepaction"]
> [什麼是 Azure Load Balancer？](load-balancer-overview.md)


