---
title: 使用 PowerShell 建立 Azure 內部負載平衡器
titleSuffix: Azure Load Balancer
description: 了解如何使用 Azure Resource Manager 的 Azure PowerShell 模組建立內部負載平衡器
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/02/2020
ms.author: allensu
ms.openlocfilehash: 7e962b87b1bd7ef200aa276d8dbecffd5d3fed04
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090428"
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>使用 Azure PowerShell 模組建立內部負載平衡器

> [!div class="op_single_selector"]
> * [Azure 入口網站](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [範本](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>開始使用設定

本文說明如何使用 Azure Resource Manager 與 Azure PowerShell 模組來建立內部負載平衡器。 在 Resource Manager 部署模型中，需要建立內部負載平衡器的物件會個別設定。 建立和設定物件之後，它們會結合以建立負載平衡器。

若要部署負載平衡器，必須建立下列物件：

* 前端 IP 集區：傳入網路流量的私人 IP 位址。
* 後端位址集區：從前端 IP 位址接收負載平衡流量的網路介面。
* 負載平衡規則：負載平衡器的連接埠 (來源與本機) 設定。
* 探查設定：虛擬機器的健康情況狀態探查。
* 輸入 NAT 規則：直接存取虛擬機器的連接埠規則。

如需負載平衡器元件的詳細資訊，請參閱[Azure Load Balancer 元件](components.md)。

下列步驟說明如何在兩部虛擬機器之間設定負載平衡器。

## <a name="set-up-powershell-to-use-resource-manager"></a>設定 PowerShell 以使用 Resource Manager

請確定您擁有最新生產環境版本的 Azure PowerShell 模組。 必須正確設定 PowerShell 才能存取 Azure 訂用帳戶。

### <a name="step-1-start-powershell"></a>步驟 1：啟動 PowerShell

啟動 Azure Resource Manager 適用的 PowerShell 模組。

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="step-2-view-your-subscriptions"></a>步驟 2：檢視您的訂用帳戶

檢查您的可用 Azure 訂用帳戶。

```azurepowershell-interactive
Get-AzSubscription
```

當系統提示您進行驗證時，請輸入您的認證。

### <a name="step-3-select-the-subscription-to-use"></a>步驟 3：選取要使用的訂用帳戶

選擇要使用哪一個 Azure 訂用帳戶來部署負載平衡器。

```azurepowershell-interactive
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>步驟 4：選擇負載平衡器的資源群組

為負載平衡器建立新的資源群組。 如果您使用現有的資源群組，請略過此步驟。

```azurepowershell-interactive
New-AzResourceGroup -Name NRP-RG -location "West US"
```

Azure 資源管理員需要所有的資源群組指定一個位置。 此位置會作為資源群組中之資源的預設位置。 針對與建立負載平衡器相關的所有命令，一律使用相同的資源群組。

在範例中，我們已建立名為 **NRP-RG** 的資源群組，且位置為「美國西部」。

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>建立前端 IP 集區的虛擬網路和 IP 位址

建立虛擬網路的子網路，並指派給變數 **$backendSubnet**。

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

建立虛擬網路。

```azurepowershell-interactive
$vnet= New-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

虛擬網路已建立。 **LB-Subnet-BE** 子網路會新增至 **NRPVNet** 虛擬網路。 這些值會指派給 **$vnet** 變數。

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>建立前端 IP 集區與後端位址集區

針對傳入流量建立前端 IP 集區，以及建立後端位址集區以接收負載平衡流量。

### <a name="step-1-create-a-front-end-ip-pool"></a>步驟 1：建立前端 IP 集區

建立針對子網路 10.0.2.0/24 具有 IP 位址 10.0.2.5 的前端 IP 集區。 這個位址是傳入網路流量端點。

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>步驟 2：建立後端位址集區

建立後端位址集區以從前端 IP 集區接收傳入流量：

```powershell
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>建立設定規則、探查，以及負載平衡器

前端 IP 集區和後端位址集區建立之後，請指定負載平衡器資源的規則。

### <a name="step-1-create-the-configuration-rules"></a>步驟 1：建立設定規則

此範例會建立下列四個規則物件：

* 遠端桌面通訊協定 (RDP) 的輸入 NAT 規則：將連接埠 3441 上的所有傳入流量重新導向至連接埠 3389。
* RDP 的第二個輸入 NAT 規則：將連接埠 3442 上的所有傳入流量重新導向至連接埠 3389。
* 健康情況探查規則：檢查 HealthProbe.aspx 路徑的健康情況狀態。
* 負載平衡器規則：將公用埠80上的所有連入流量負載平衡至後端位址集區中的本機埠80。
* [HA 埠負載平衡器規則](load-balancer-ha-ports-overview.md)，可將所有連入流量負載平衡到所有埠，以簡化標準 ILB 的 HA 案例。

```azurepowershell-interactive
$inboundNATRule1= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

$haportslbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="step-2-create-the-load-balancer"></a>步驟 2：建立負載平衡器

建立負載平衡器並結合規則物件 (適用於 RDP 的輸入 NAT 、負載平衡器，以及健康情況探查)：

```azurepowershell-interactive
$NRPLB = New-AzLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -SKU Standard -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

使用 `-SKU Basic` 建立基本 Load Balancer。 Microsoft 建議對生產工作負載使用「標準」。

## <a name="create-the-network-interfaces"></a>建立網路介面

建立內部負載平衡器之後，定義哪些網路介面 (NIC) 可以接收傳入的負載平衡網路流量、NAT 規則和探查。 個別設定每個網路介面，並在稍後指派給虛擬機器。

### <a name="step-1-create-the-first-network-interface"></a>步驟 1：建立第一個網路介面

取得資源虛擬網路和子網路。 這些值會用來建立網路介面：

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

使用名稱 **lb-nic1-be** 建立第一個網路介面。 將介面指派給負載平衡器後端集區。 將第一個適用於 RDP 的 NAT 規則與此 NIC 相關聯：

```azurepowershell-interactive
$backendnic1= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>步驟 2：建立第二個網路介面

使用名稱 **lb-nic2-be** 建立第二個網路介面。 將第二個介面指派給與第一個介面相同的負載平衡器後端集區。 關聯第二個 NIC 與第二個適用於 RDP 的 NAT 規則：

```azurepowershell-interactive
$backendnic2= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

檢閱組態：

```azurepowershell-interactive
$backendnic1
```

設定應如下所示：

```output
Name                 : lb-nic1-be
ResourceGroupName    : NRP-RG
Location             : westus
Id                   : /subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
ProvisioningState    : Succeeded
Tags                 :
VirtualMachine       : null
IpConfigurations     : [
                     {
                       "PrivateIpAddress": "10.0.2.6",
                       "PrivateIpAllocationMethod": "Static",
                       "Subnet": {
                         "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                       },
                       "PublicIpAddress": {
                         "Id": null
                       },
                       "LoadBalancerBackendAddressPools": [
                         {
                           "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                         }
                       ],
                       "LoadBalancerInboundNatRules": [
                         {
                           "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                         }
                       ],
                       "ProvisioningState": "Succeeded",
                       "Name": "ipconfig1",
                       "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                       "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                     }
                   ]
DnsSettings          : {
                     "DnsServers": [],
                     "AppliedDnsServers": []
                   }
AppliedDnsSettings   :
NetworkSecurityGroup : null
Primary              : False
```

### <a name="step-3-assign-the-nic-to-a-vm"></a>步驟 3：將 NIC 指派給 VM

使用 `Add-AzVMNetworkInterface` 命令將 NIC 指派給虛擬機器。

如需建立虛擬機器並指派 NIC 的逐步指示，請參閱[使用 PowerShell 建立 Azure VM](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fload-balancer%2ftoc.json)。

## <a name="add-the-network-interface"></a>加入網路介面

在建立虛擬機器之後，新增網路介面。

### <a name="step-1-store-the-load-balancer-resource"></a>步驟 1：儲存負載平衡器資源

將負載平衡器資源儲存在變數中 (如果您還沒這麼做)。 我們會使用變數名稱 **$lb**。針對腳本中的屬性值，使用在先前步驟中建立的負載平衡器資源名稱。

```azurepowershell-interactive
$lb = Get-AzLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>步驟 2：儲存後端設定

將後端設定儲存到 **$backend** 變數。

```azurepowershell-interactive
$backend = Get-AzLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>步驟 3：儲存網路介面

將網路介面儲存在另一個變數中。 這個介面是在「建立網路介面，步驟 1」中建立。 我們會使用變數名稱 **$nic1**。 使用與上述範例相同的網路介面名稱。

```azurepowershell-interactive
$nic = Get-AzNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>步驟 4：變更後端設定

變更網路介面上的後端組態。

```azurepowershell-interactive
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>步驟 5：儲存網路介面物件

儲存網路介面物件。

```azurepowershell-interactive
Set-AzNetworkInterface -NetworkInterface $nic
```

介面新增至後端集區之後，網路流量會根據規則進行負載平衡。 這些規則是在「建立設定規則、探查，以及負載平衡器」中建立。

## <a name="update-an-existing-load-balancer"></a>更新現有負載平衡器

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>步驟 1：將負載平衡器物件指派給變數

使用 `Get-AzLoadBalancer` 命令將負載平衡器物件 (來自上一個範例) 指派給 **$slb** 變數：

```azurepowershell-interactive
$slb = Get-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>步驟 2：新增 NAT 規則

將新的輸入 NAT 規則新增至現有的負載平衡器。 針對前端集區使用連接埠 81，針對後端集區使用連接埠 8181：

```azurepowershell-interactive
$slb | Add-AzLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>步驟 3：儲存設定

使用 `Set-AzureLoadBalancer` 命令來儲存新的設定：

```azurepowershell-interactive
$slb | Set-AzLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>移除現有負載平衡器

使用 `Remove-AzLoadBalancer` 命令，刪除 **NRP-RG** 資源群組中的 **NRP-LB** 負載平衡器：

```azurepowershell-interactive
Remove-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> 使用選擇性 **-Force** 參數來避免刪除的確認提示。

## <a name="next-steps"></a>後續步驟

* [設定負載平衡器分配模式](load-balancer-distribution-mode.md)
* [設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
