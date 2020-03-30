---
title: 使用 Azure PowerShell 配置負載平衡和出站規則
titleSuffix: Azure Load Balancer
description: 本文演示如何使用 Azure PowerShell 配置標準負載平衡器中的負載平衡和出站規則。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225435"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>使用 Azure PowerShell 配置標準負載等化器中的負載平衡和出站規則

本文介紹如何使用 Azure PowerShell 在標準負載等化器中配置出站規則。  

完成本文的方案後，負載等化器資源包含兩個前端及其關聯規則。 對於入站流量，您有一個前端，另一個前端用於出站流量。  

每個前端引用公共 IP 位址。 在這種情況下，入站流量的公共 IP 位址與出站流量的位址不同。   負載平衡規則僅提供入站負載平衡。 出站規則控制 VM 的出站網路位址轉譯 （NAT）。  

該方案使用兩個後端池：一個用於入站流量，一個用於出站流量。 這些池說明了功能，並為方案提供了靈活性。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶
使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登入您的 Azure 訂用帳戶。 然後，遵循螢幕上的指示來進行。
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>建立資源群組

使用[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)創建資源組。 Azure 資源組是一個邏輯容器，將 Azure 資源部署到其中。 然後從組管理資源。

下列範例會在 eastus2** 位置建立名為 myresourcegroupoutbound** 的資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>建立虛擬網路
創建名為*myvnet 出站的*虛擬網路。 命名其子*網我的子網出站*。 使用[新阿茲虛擬網路和](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0)[新-Az虛擬網路子系統配置](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)，將其放入*我的資源組出站*。

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>創建入站公共 IP 位址 

要訪問互聯網上的 Web 應用，您需要負載等化器的公共 IP 位址。 標準負載等化器僅支援標準公共 IP 位址。 

使用[New-AzPublicIp 位址](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)在我的*資源組出站*中創建名為*mypublicipinininininininininininininininininininininininininininininininininin 的*標準公共 IP 位址。

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>創建出站公共 IP 位址 

使用[New-AzPublicIp位址](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)為負載等化器的前端出站配置創建標準 IP 位址。

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>建立 Azure Load Balancer

本節介紹如何創建和配置負載等化器的以下元件：
  - 接收負載等化器上傳入網路流量的前端 IP
  - 前端 IP 發送負載平衡網路流量的後端池
  - 用於出站連接的後端池
  - 確定後端 VM 實例運行狀況的運行狀況探測
  - 負載等化器入站規則，用於定義流量如何分發到 VM
  - 負載等化器出站規則，用於定義流量如何從 VM 分發

### <a name="create-an-inbound-front-end-ip"></a>創建入站前端 IP
使用[New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)為負載等化器創建入站前端 IP 配置。 負載等化器應包括名為*myfrontendinininininininininininininin 的*入站前端 IP 配置。 將此配置與公共 IP 位址*mypublicipinininininininininininininininininininininininininininininininininin*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>創建出站前端 IP
使用[New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)為負載等化器創建出站前端 IP 配置。 此負載等化器應包括名為*myfrontend 出站*的出站前端 IP 配置。 將此配置與公共 IP 位址*mypublicip 出站相關聯*。

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>創建入站後端池
使用[New-AzLoad平衡器Backend位址集區配置為](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)載入等化器創建後端入站池。 命名*池入網*。

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>創建出站後端池
使用以下命令創建另一個後端位址集區，通過使用[New-AzLoadBalancerBackend 位址集區 Config，](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)為 VM 池定義出站連接。 命名此*池要出站*。 

通過創建單獨的出站池，您可以提供最大的靈活性。 但是，您可以省略此步驟，如果您願意，則僅使用入*站綁定。*  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>建立健康狀態探查

運行狀況探測檢查所有 VM 實例，以確保它們可以發送網路流量。 未通過探測檢查的 VM 實例將從負載等化器中刪除，直到它重新連線，並且探測檢查確定它是否正常。 

要監視 VM 的運行狀況，請使用[New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0)創建運行狀況探測器。 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>創建負載等化器規則

負載等化器規則定義傳入流量的前端 IP 配置和接收流量的後端池。 它還定義所需的源和目標埠。 

使用[New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0)創建名為*mybundlbrule*的負載等化器規則。 此規則將偵聽埠 80 在前端池*我的前置綁定*。 它還將使用埠80將負載平衡的網路流量發送到後端位址集區 *。* 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>由於 **-禁用出站SNAT**參數，此負載平衡規則禁用自動出站安全 NAT （SNAT）。 出站 NAT 僅由出站規則提供。

### <a name="create-an-outbound-rule"></a>建立輸出規則

出站規則定義前端公共 IP，該 IP 由前端*myfrontend出站*表示。 此前端將用於所有出站 NAT 流量以及規則適用的後端池。  

使用以下命令創建出*站規則 my出站規則*，用於*在 bepool*後端池中所有 VM（在 NIC IP 配置中）的出站網路轉換。  該命令將出站空閒超時從 4 分鐘更改為 15 分鐘。 它分配 10，000 個 SNAT 埠，而不是 1，024 個。 有關詳細資訊，請參閱[新-AzLoad平衡器出位規則配置](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)。

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
如果不想使用單獨的出站池，則可以更改上述命令中的位址集區參數以指定 *$bepoolin。*  我們建議使用單獨的池來使生成的配置靈活且可讀。

### <a name="create-a-load-balancer"></a>建立負載平衡器

使用以下命令使用[New-AzLoad平衡器](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0)為入站 IP 位址創建負載等化器。 命名負載等化器*磅*。它應包括入站前端 IP 配置。 其後端池綁定應與您在前面步驟中創建的公共 IP 位址*mypublicipinininininininininininininininininininininininininininin）* 相關聯。 *bepoolinbound*

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

此時，您可以通過更新相應 NIC 資源的 IP 配置，繼續將 VM 添加到*要綁定*和*到池出的後端*池。 使用[Add-AzNetwork 介面 IpConfig 更新](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)資源配置。

## <a name="clean-up-resources"></a>清除資源

當您不再需要資源組、負載等化器和相關資源時，可以使用[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0)刪除這些資源。

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>後續步驟
在本文中，您創建了一個標準負載等化器，配置了入站和出站負載平衡器流量規則，並為後端池中的 VM 配置了運行狀況探測。 

要瞭解更多資訊，請繼續訪問[Azure 負載等化器](tutorial-load-balancer-standard-public-zone-redundant-portal.md)的教程。
