---
title: 使用 Azure PowerShell 設定負載平衡和輸出規則
titleSuffix: Azure Load Balancer
description: 本文說明如何使用 Azure PowerShell，在 Standard Load Balancer 中設定負載平衡和輸出規則。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74225435"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>使用 Azure PowerShell 在 Standard Load Balancer 中設定負載平衡和輸出規則

本文說明如何使用 Azure PowerShell 在 Standard Load Balancer 中設定輸出規則。  

當您完成本文的案例時，負載平衡器資源會包含兩個前端和其相關聯的規則。 您有一個前端用於輸入流量，另一個前端用於輸出流量。  

每個前端都會參考公用 IP 位址。 在此案例中，輸入流量的公用 IP 位址與輸出流量的位址不同。   負載平衡規則只會提供輸入負載平衡。 輸出規則會控制 VM 的輸出網路位址轉譯（NAT）。  

此案例使用兩個後端集區：一個用於輸入流量，另一個用於輸出流量。 這些集區說明功能，並提供案例的彈性。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶
使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 命令登入您的 Azure 訂用帳戶。 然後，遵循螢幕上的指示來進行。
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>建立資源群組

使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)建立資源群組。 Azure 資源群組是 Azure 資源部署所在的邏輯容器。 然後，系統會從群組管理資源。

下列範例會在 eastus2** 位置建立名為 myresourcegroupoutbound** 的資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>建立虛擬網路
建立名為*myvnetoutbound*的虛擬網路。 將其子網命名為*myresourcegroupoutbound*。 使用[new-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0)和[new-azvirtualnetworksubnetconfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0)將它放在*myresourcegroupoutbound*中。

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>建立輸入公用 IP 位址 

若要在網際網路上存取您的 web 應用程式，您需要負載平衡器的公用 IP 位址。 Standard Load Balancer 僅支援標準公用 IP 位址。 

使用[get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)在*myresourcegroupoutbound*中建立名為*mypublicipinbound*的標準公用 IP 位址。

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>建立輸出公用 IP 位址 

使用[get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0)建立負載平衡器前端輸出設定的標準 IP 位址。

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>建立 Azure Load Balancer

本節說明如何建立及設定下列負載平衡器元件：
  - 接收負載平衡器上的連入網路流量的前端 IP
  - 後端集區，前端 IP 會在其中傳送負載平衡的網路流量
  - 輸出連線能力的後端集區
  - 可判斷後端 VM 實例健康情況的健康情況探查
  - 定義如何將流量分散至 Vm 的負載平衡器輸入規則
  - 一種負載平衡器輸出規則，可定義如何從 Vm 散發流量

### <a name="create-an-inbound-front-end-ip"></a>建立輸入前端 IP
使用[new-azloadbalancerfrontendipconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)建立負載平衡器的輸入前端 IP 設定。 負載平衡器應包含名為*myfrontendinbound*的輸入前端 IP 設定。 將此設定與公用 IP 位址*mypublicipinbound*建立關聯。

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>建立輸出前端 IP
使用[new-azloadbalancerfrontendipconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0)建立負載平衡器的輸出前端 IP 設定。 此負載平衡器應包含名為*myfrontendoutbound*的輸出前端 IP 設定。 將此設定與公用 IP 位址*mypublicipoutbound*建立關聯。

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>建立輸入後端集區
使用[new-azloadbalancerbackendaddresspoolconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)建立負載平衡器的後端輸入集區。 將集區命名為*bepoolinbound*。

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>建立輸出後端集區
使用下列命令建立另一個後端位址集區，以使用[new-azloadbalancerbackendaddresspoolconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0)定義 vm 集區的輸出連線能力。 將此集區命名為*bepooloutbound*。 

藉由建立個別的輸出集區，您可以提供最大的彈性。 但您可以省略此步驟，並在您偏好的情況下使用輸入*bepoolinbound* 。  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>建立健康狀態探查

健康情況探查會檢查所有 VM 實例，以確保它們可以傳送網路流量。 失敗探查檢查的 VM 實例會從負載平衡器中移除，直到它重新上線，探查檢查判斷其狀況良好為止。 

若要監視 Vm 的健康情況，請使用[new-azloadbalancerprobeconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0)建立健康狀態探查。 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則會定義連入流量的前端 IP 設定和後端集區，以接收流量。 它也會定義所需的來源和目的地埠。 

使用[add-azloadbalancerruleconfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0)建立名為*myinboundlbrule*的負載平衡器規則。 此規則會接聽前端集區*myfrontendinbound*中的埠80。 它也會使用埠80，將負載平衡的網路流量傳送到後端位址集區*bepoolinbound*。 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>由於 **-DisableOutboundSNAT**參數，此負載平衡規則會停用自動輸出安全 NAT （SNAT）。 輸出 NAT 僅由輸出規則提供。

### <a name="create-an-outbound-rule"></a>建立輸出規則

輸出規則會定義前端*myfrontendoutbound*所代表的前端公用 IP。 此前端將用於所有輸出 NAT 流量，以及套用規則的後端集區。  

使用下列命令，針對*bepool*後端集區中的所有 vm （在 NIC IP 設定中），建立輸出網路轉譯的輸出規則*myoutboundrule* 。  此命令會將輸出閒置時間從4變更為15分鐘。 它會配置 10000 SNAT 埠，而不是1024。 如需詳細資訊，請參閱[AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)。

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
如果您不想要使用個別的輸出集區，您可以變更上述命令中的位址集區引數，改為指定 *$bepoolin* 。  我們建議使用不同的集區，讓產生的設定具有彈性且可讀取。

### <a name="create-a-load-balancer"></a>建立負載平衡器

使用下列命令，使用[remove-azloadbalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0)建立輸入 IP 位址的負載平衡器。 將負載平衡器命名為*lb*。它應該包含輸入前端 IP 設定。 其後端集區*bepoolinbound*應該與您在上一個步驟中建立的公用 IP 位址*mypublicipinbound*相關聯。

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

此時，您可以藉由更新個別 NIC 資源的 IP 設定，繼續將您的 Vm 新增至*bepoolinbound*和*bepooloutbound*後端集區。 使用[AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)更新資源設定。

## <a name="clean-up-resources"></a>清除資源

當您不再需要資源群組、負載平衡器和相關資源時，可以使用[remove-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0)將它們移除。

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>後續步驟
在本文中，您已建立標準負載平衡器、設定輸入和輸出負載平衡器流量規則，並已針對後端集區中的 Vm 設定健康情況探查。 

若要深入瞭解，請繼續進行[Azure Load Balancer 的教學](tutorial-load-balancer-standard-public-zone-redundant-portal.md)課程。
