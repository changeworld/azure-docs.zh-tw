---
title: 快速入門：使用 Azure PowerShell 建立 Azure private link 服務
description: 瞭解如何使用 Azure PowerShell 建立 Azure private link 服務
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 01/24/2021
ms.author: allensu
ms.openlocfilehash: e8d76e12dea27338e965d8e77871427e9dfabf23
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746674"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>使用 Azure PowerShell 建立 Private Link 服務

開始建立參考您服務的 Private Link 服務。  針對在 Azure Standard Load Balancer 後方部署的服務或資源，授與 Private Link 存取權限。  您服務的使用者可以從其虛擬網路進行私人存取。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 已在本機安裝 Azure PowerShell 或 Azure Cloud Shell

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 Azure PowerShell 模組版本 5.4.1 或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivLinkService-rg' -Location 'eastus2'

```
---
## <a name="create-an-internal-load-balancer"></a>建立內部負載平衡器

在本節中，您會建立虛擬網路和內部 Azure Load Balancer。

### <a name="virtual-network"></a>虛擬網路

在本節中，您會建立虛擬網路和子網路，以裝載可存取 Private Link 服務的負載平衡器。

* 使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路。

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    PrivateLinkServiceNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig
}
$vnet = New-AzVirtualNetwork @net

```

### <a name="create-standard-load-balancer"></a>建立標準負載平衡器

本節將詳細說明如何建立及設定下列負載平衡器元件：

* 使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 建立前端 IP 集區的前端 IP。 此 IP 會接收負載平衡器上的連入流量

* 使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig)，為從負載平衡器的前端傳送的流量建立後端位址集區。 此集區是您的後端虛擬機器部署所在的位置。

* 使用 [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) 建立健全狀態探查，以判斷後端 VM 執行個體的健全狀態。

* 使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 建立負載平衡器規則，以定義如何將流量分散至 VM。

* 使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 建立公用負載平衡器。


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

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
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus2'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-a-private-link-service"></a>建立私人連結服務

在本節中，建立使用上一個步驟中所建立之標準 Azure Load Balancer 的 private link 服務。

* 使用 [AzPrivateLinkServiceIpConfig](/powershell/module/az.network/new-azprivatelinkserviceipconfig)建立 private LINK 服務 IP 設定。

* 使用 [New-AzPrivateLinkService](/powershell/module/az.network/new-azprivatelinkservice)建立 private link 服務。

```azurepowershell
## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create the IP configuration for the private link service. ##
$ipsettings = @{
    Name = 'myIPconfig'
    PrivateIpAddress = '10.1.0.5'
    Subnet = $vnet.subnets[0]
}
$ipconfig = New-AzPrivateLinkServiceIpConfig @ipsettings

## Place the load balancer frontend configuration into a variable. ##
$par = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$fe = Get-AzLoadBalancer @par | Get-AzLoadBalancerFrontendIpConfig

## Create the private link service for the load balancer. ##
$privlinksettings = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    LoadBalancerFrontendIpConfiguration = $fe
    IpConfiguration = $ipconfig
}
New-AzPrivateLinkService @privlinksettings

```

系統會建立您的私人連結服務，並可接收流量。 如果您想要查看流量流程，請在您的標準負載平衡器後方設定您的應用程式。

## <a name="create-private-endpoint"></a>建立私人端點

在本節中，您會將 private link 服務對應至私人端點。 虛擬網路包含私用連結服務的私人端點。 此虛擬網路包含可存取私用連結服務的資源。

### <a name="create-private-endpoint-virtual-network"></a>建立私人端點虛擬網路

* 使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 建立虛擬網路。

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnetPE'
    AddressPrefix = '11.1.0.0/24'
    PrivateEndpointNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '11.1.0.0/16'
    Subnet = $subnetConfig
}
$vnetpe = New-AzVirtualNetwork @net

```

### <a name="create-endpoint-and-connection"></a>建立端點和連接

* 使用 [AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) 將您稍早建立之私用連結服務的設定放在變數中，以供稍後使用。

* 使用 [AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection) 建立連線設定。

* 使用 [AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) 建立端點。



```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

## Create the private link configuration and place in variable. ##
$par2 = @{
    Name = 'myPrivateLinkConnection'
    PrivateLinkServiceId = $pls.Id
}
$plsConnection = New-AzPrivateLinkServiceConnection @par2

## Place the virtual network into a variable. ##
$par3 = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$vnetpe = Get-AzVirtualNetwork @par3

## Create private endpoint ##
$par4 = @{
    Name = 'MyPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    Subnet = $vnetpe.subnets[0]
    PrivateLinkServiceConnection = $plsConnection
}
New-AzPrivateEndpoint @par4 -ByManualRequest
```

### <a name="approve-the-private-endpoint-connection"></a>核准私人端點連線

在本節中，您將核准您在先前的步驟中建立的連接。

* 使用 [AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnnection) 核准連接。

```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

$par2 = @{
    Name = $pls.PrivateEndpointConnections[0].Name
    ServiceName = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Description = 'Approved'
}
Approve-AzPrivateEndpointConnection @par2

```

### <a name="ip-address-of-private-endpoint"></a>私人端點的 IP 位址

在本節中，您會找到與負載平衡器和私用連結服務對應的私人端點 IP 位址。

* 使用 [AzPrivateEndpoint](/powershell/module/az.network/get-azprivateendpoint) 來取得 IP 位址。

```azurepowershell-interactive
## Get private endpoint and the IP address and place in a variable for display. ##
$par1 = @{
    Name = 'myPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    ExpandResource = 'networkinterfaces'
}
$pe = Get-AzPrivateEndpoint @par1

## Display the IP address by expanding the variable. ##
$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
```

```bash
❯ $pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
11.1.0.4
```

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、負載平衡器和其餘資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePrivLinkService-rg'
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已：

* 建立虛擬網路和內部 Azure Load Balancer。
* 建立私人連結服務

若要深入了解 Azure 私人端點，請繼續：
> [!div class="nextstepaction"]
> [快速入門：使用 Azure powershell 建立私人端點](create-private-endpoint-powershell.md)

