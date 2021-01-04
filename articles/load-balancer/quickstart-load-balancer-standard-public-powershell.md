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
ms.date: 11/22/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 3599f098aa73b7433e2e5af0047c694634e2f933
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630525"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立公用負載平衡器以平衡 VM 的負載

使用 Azure PowerShell 建立公用負載平衡器和三部虛擬機器，以開始使用 Azure Load Balancer。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已在本機安裝 Azure PowerShell 或 Azure Cloud Shell

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePubLBQS-rg' -Location 'eastus'

```
---

# <a name="standard-sku"></a>[**標準 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="針對快速入門所建立的標準負載平衡器資源。" border="false":::

## <a name="create-a-public-ip-address---standard"></a>建立公用 IP 位址 - 標準

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 建立公用 IP 位址。

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
}
New-AzPublicIpAddress @publicip

```

若要在區域 1 中建立區域性公用 IP 位址，請使用下列命令：

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = '1'
}
New-AzPublicIpAddress @publicip

```

## <a name="create-standard-load-balancer"></a>建立標準負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

* 使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 建立前端 IP 集區的前端 IP。 此 IP 會接收負載平衡器上的連入流量

* 使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)，為從負載平衡器的前端傳送的流量建立後端位址集區。 此集區是您的後端虛擬機器部署所在的位置。

* 使用 [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) 建立健全狀態探查，以判斷後端 VM 執行個體的健全狀態。

* 使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 建立負載平衡器規則，以定義如何將流量分散至 VM。

* 使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 建立公用負載平衡器。


```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$publicIp = Get-AzPublicIpAddress -Name 'myPublicIP' -ResourceGroupName 'CreatePubLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset -DisableOutboundSNAT

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="configure-virtual-network---standard"></a>設定虛擬網路 - 標準

請先建立支援的虛擬網路資源，才可部署 VM 並測試您的負載平衡器。

建立後端虛擬機器的虛擬網路。

建立網路安全性群組，以定義虛擬網路的輸入連線。

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>建立虛擬網路、網路安全性群組和堡壘主機

* 使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路。

* 使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 建立網路安全性群組規則。

* 使用 [New-AzBastion](/powershell/module/az.network/new-azbastion) 建立 Azure Bastion 主機。

* 使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 建立網路安全性群組。

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```

## <a name="create-virtual-machines---standard"></a>建立虛擬機器 - 標準

在本節中，您將為負載平衡器的後端集區建立三個虛擬機器。

* 使用 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 建立三個網路介面。

* 使用 [Get-credential](/powershell/module/microsoft.powershell.security/get-credential) 來設定 VM 的系統管理員使用者名稱和密碼。

* 使用下列項目建立虛擬機器：
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePubLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreatePubLBQS-rg'

## For loop with variable to create virtual machines for load balancer backend pool. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
    Zone = "$i"
}
New-AzVM @vm -AsJob
}

```

虛擬機器和堡壘主機的部署會以 PowerShell 作業的形式提交。 若要檢視作業的狀態，請使用 [Get-Job](/powershell/module/microsoft.powershell.core/get-job)：

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

## <a name="create-outbound-rule-configuration"></a>建立輸出規則設定
負載平衡器輸出規則會在後端集區中設定 VM 的輸出來源網路位址轉譯 (SNAT)。 

如需輸出連線詳細資訊，請參閱 [Azure 中的輸出連線](load-balancer-outbound-connections.md)。

### <a name="create-outbound-public-ip-address"></a>建立輸出公用 IP 位址

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 建立名為 **myPublicIPOutbound** 的標準區域備援公用 IP 位址。

```azurepowershell-interactive
$publicipout = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
}
New-AzPublicIpAddress @publicipout

```

若要在區域 1 中建立區域性公用 IP 位址，請使用下列命令：

```azurepowershell-interactive
$publicipout = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = '1'
}
New-AzPublicIpAddress @publicipout

```

### <a name="create-outbound-configuration"></a>建立輸出設定

* 使用 [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig) 建立新的前端 IP 設定。

* 使用 [Add-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig) 建立新的輸出集區。 

* 使用 [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer)，將集區和前端 IP 位址套用至負載平衡器。
*  使用 [Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig) 為輸出後端集區建立新的輸出規則。 

```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$pubip = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$publicIp = Get-AzPublicIpAddress @pubip

## Get the load balancer configuration ##
$lbc = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @lbc

## Create the frontend configuration ##
$fe = @{
    Name = 'myFrontEndOutbound'
    PublicIPAddress = $publicIP
}
$lb | Add-AzLoadBalancerFrontendIPConfig @fe | Set-AzLoadBalancer

## Create the outbound backend address pool ##
$be = @{
    Name = 'myBackEndPoolOutbound'
}
$lb | Add-AzLoadBalancerBackendAddressPoolConfig @be | Set-AzLoadBalancer

## Apply the outbound rule configuration to the load balancer. ##
$rule = @{
    Name = 'myOutboundRule'
    AllocatedOutboundPort = '10000'
    Protocol = 'All'
    IdleTimeoutInMinutes = '15'
    FrontendIPConfiguration = $lb.FrontendIpConfigurations[1]
    BackendAddressPool = $lb.BackendAddressPools[1]
}
$lb | Add-AzLoadBalancerOutBoundRuleConfig @rule | Set-AzLoadBalancer

```

### <a name="add-virtual-machines-to-outbound-pool"></a>將虛擬機器新增至輸出集區

使用 [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)，將虛擬機器網路介面新增至負載平衡器的輸出集區：

```azurepowershell-interactive
## Get the load balancer configuration ##
$lbc = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @lbc

# For loop with variable to add virtual machines to backend outbound pool. ##
for ($i=1; $i -le 3; $i++)
{
$nic = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = "myNicVM$i"
}
$nicvm = Get-AzNetworkInterface @nic

## Apply the backend to the network interface ##
$be = @{
    Name = 'ipconfig1'
    LoadBalancerBackendAddressPoolId = $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id
}
$nicvm | Set-AzNetworkInterfaceIpConfig @be | Set-AzNetworkInterface
}

```

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。 如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="快速入門中建立的基本負載平衡器資源。" border="false":::

## <a name="create-a-public-ip-address---basic"></a>建立公用 IP 位址 - 基本

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 建立公用 IP 位址。

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Basic'
    AllocationMethod = 'static'
}
New-AzPublicIpAddress @publicip

```

## <a name="create-basic-load-balancer"></a>建立基本負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

* 使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 建立前端 IP 集區的前端 IP。 此 IP 會接收負載平衡器上的連入流量

* 使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)，為從負載平衡器的前端傳送的流量建立後端位址集區。 此集區是您的後端虛擬機器部署所在的位置。

* 使用 [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) 建立健全狀態探查，以判斷後端 VM 執行個體的健全狀態。

* 使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 建立負載平衡器規則，以定義如何將流量分散至 VM。

* 使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 建立公用負載平衡器。

```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$publicIp = Get-AzPublicIpAddress -Name 'myPublicIP' -ResourceGroupName 'CreatePubLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Basic'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="configure-virtual-network---basic"></a>設定虛擬網路 - 基本

請先建立支援的虛擬網路資源，才可部署 VM 並測試您的負載平衡器。

建立後端虛擬機器的虛擬網路。

建立網路安全性群組，以定義虛擬網路的輸入連線。

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>建立虛擬網路、網路安全性群組和堡壘主機

* 使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路。

* 使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 建立網路安全性群組規則。

* 使用 [New-AzBastion](/powershell/module/az.network/new-azbastion) 建立 Azure Bastion 主機。

* 使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 建立網路安全性群組。

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```

## <a name="create-virtual-machines---basic"></a>建立虛擬機器 - 基本

在本節中，您將為負載平衡器的後端集區建立虛擬機器。

* 使用 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 建立三個網路介面。

* 使用 [Get-credential](/powershell/module/microsoft.powershell.security/get-credential) 來設定 VM 的系統管理員使用者名稱和密碼。

* 使用 [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) 建立虛擬機器的可用性設定組。

* 使用下列項目建立虛擬機器：
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePubLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreatePubLBQS-rg'

## Create availability set for the virtual machines. ##
$set = @{
    Name = 'myAvSet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Aligned'
    PlatformFaultDomainCount = '2'
    PlatformUpdateDomainCount =  '2'
}
$avs = New-AzAvailabilitySet @set

## For loop with variable to create virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'
    AvailabilitySetId = $avs.Id   
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm -AsJob
}

```

虛擬機器和堡壘主機的部署會以 PowerShell 作業的形式提交。 若要檢視作業的狀態，請使用 [Get-Job](/powershell/module/microsoft.powershell.core/get-job)：

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

---

## <a name="install-iis"></a>安裝 IIS

使用 [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) 來安裝自訂指令碼擴充功能。 

擴充功能會執行 `PowerShell Add-WindowsFeature Web-Server` 以安裝 IIS Web 伺服器，然後更新 Default.htm  頁面以顯示 VM 的主機名稱：

> [!IMPORTANT]
> 請先確定您已完成先前步驟中的虛擬機器部署，再繼續操作。  請使用 `Get-Job` 檢查虛擬機器部署作業的狀態。

```azurepowershell-interactive
## For loop with variable to install custom script extension on virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
$ext = @{
    Publisher = 'Microsoft.Compute'
    ExtensionType = 'CustomScriptExtension'
    ExtensionName = 'IIS'
    ResourceGroupName = 'CreatePubLBQS-rg'
    VMName = "myVM$i"
    Location = 'eastus'
    TypeHandlerVersion = '1.8'
    SettingString = '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
}
Set-AzVMExtension @ext -AsJob
}
```

擴充功能會以 PowerShell 作業的形式部署。 若要檢視安裝作業的狀態，請使用 [Get-Job](/powershell/module/microsoft.powershell.core/get-job)：


```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
8      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
9      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
10     Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
```

## <a name="test-the-load-balancer"></a>測試負載平衡器

使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 取得負載平衡器的公用 IP 位址：

```azurepowershell-interactive
$ip = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myPublicIP'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```

將公用 IP 位址複製並貼到您瀏覽器的網址列。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

   ![IIS Web 伺服器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

若要讓負載平衡器將流量分散到這三個 VM，您可以為每個 VM 的 IIS Web 伺服器自訂預設頁面，然後從用戶端機器強制重新整理您的網頁瀏覽器。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、負載平衡器和其餘資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePubLBQS-rg'

```

## <a name="next-steps"></a>後續步驟

在本快速入門中：

* 您建立了標準或基本公用負載平衡器
* 連結的虛擬機器。 
* 設定了負載平衡器流量規則和健康情況探查。
* 測試了負載平衡器。

若要深入了解 Azure Load Balancer，請繼續：
> [!div class="nextstepaction"]
> [什麼是 Azure Load Balancer？](load-balancer-overview.md)